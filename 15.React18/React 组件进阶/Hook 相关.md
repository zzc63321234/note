## 闭包陷阱

对于函数式组件，一般使用 `useState` 来存储状态。

由于 `React` 的数据不可变原则，更新状态时，会创建一个新的状态值，而不会改变旧的状态。

当 `React` 进行 `re-render` 时，函数组件会作为函数重新调用（以下将其简称为函数组件调用）。该次函数组件调用的返回值会被 `React` 转换为新的虚拟 `DOM`，经过 `diffing` 算法后展示到页面上，也就是说页面上使用的应当是该次函数组件调用时的函数上下文（如果没有产生闭包）。而上一次组件函数调用的上下文中的变量如果被本次函数调用时引用到了，那么就产生了一个闭包。

如以下情况就产生了闭包：

> ~~~tsx
> import  { useEffect, useState } from "react";
> 
> function App() {
>   const [num, setNum] = useState(0);
> 
>   useEffect(() => {
>     setInterval(() => {
>       setNum(num + 1);
>     }, 500);
>   }, []);
> 
>   return <div>{num}</div>;
> }
> 
> export default App;
> ~~~
>
> 由于 `setInterval` 中的任务需要创建后定时触发，因此当 `re-render` 时不适合再执行 `setInterval` 来反复创建，因此这里 `useEffect` 的依赖性为空。也就是说这个 `useEffect` 中能获取到的 `num` 永远是该函数组件初次调用时用 `useState` 创建的那个。因此这里 `setNum()` 中接收的参数永远是 `1`，导致之后每次 `re-render` 时，最新的 `num` 永远是 `1`。
>
> 如果需要每次定时器中任务执行时，都将 `num` 变为上一次的 `num` 再加上一，可以将更新函数传入 `setNum()`，如：`setNum((num) => num + 1)`，这里的 `num` 由 `setNum()` 方法传入，永远是最新的 `num`，不会产生闭包。
>
> 但是如果除了在 `setNum()` 中，在定时器任务中其它地方直接获取 `num` 也会产生闭包：
>
> ~~~tsx
> import { useEffect, useState } from "react";
> 
> function App() {
>   const [num, setNum] = useState(0);
> 
>   useEffect(() => {
>     setInterval(() => {
>       console.log(num); // 这里的 num 是第一次函数组件调用时产生的
>       setNum((num) => num + 1); // 这里的 num 是最新的状态
>     }, 500);
>   }, []);
> 
>   return <div>{num}</div>;
> }
> 
> export default App;
> ~~~
>
> 如果想要在定时器任务中其它地方也获取到最新的 `num`，可以使用以下思路：
>
> - `useRef` 可以创建一个对象，对象中包含一个引用。`re-render` 后 `useRef` 返回的对象不会改变（始终是函数组件第一次调用时创建的对象），且如果修改对象中的引用不会引起 `re-render`。
> - 可以用 `useRef` 生成一个 `ref` 保存 `num` 的值，并使用另一个 `useEffect`，依赖项设为 `num`，使得每次 `num` 更新时同步更新 `ref` 中的 `num` 的值。在定时器任务中可以使用该 `ref` 来获取最新的 `num`。
>
> 实现如下：
>
> ~~~tsx
> import { useEffect, useRef, useState } from "react";
> 
> function App() {
>   const [num, setNum] = useState(0);
> 
>   const numRef = useRef(num);
> 
>   useEffect(() => {
>     numRef.current = num;
>   }, [num]);
> 
>   useEffect(() => {
>     setInterval(() => {
>       console.log(numRef.current);
>       setNum((num) => num + 1);
>     }, 500);
>   }, []);
> 
>   return <div>{num}</div>;
> }
> 
> export default App;
> ~~~
>
> 也可以动态更改定时器中执行的任务，思路如下：
>
> - 将定时器中要执行的任务封装为任务函数，任务函数在每次函数组件调用时都会生成，即 `re-render` 时会生成新的任务函数，这样任务函数中能访问的始终是当前函数组件调用时的上下文环境。
> - 使用 `useRef` 创建 `ref` 引用任务函数，通过 `useEffect` 当每次 `re-render` 时都将 `ref` 中的引用更新为最新的任务函数。
>
> 通过这种方式，可以封装一个 `Hook`，用来创建定时器任务，并且可以让其访问到的 `state` 始终是最新的：
>
> ~~~tsx
> import { useEffect, useRef, useState } from "react";
> 
> function useInterval(callback: () => void, ms?: number | undefined) {
>   const callbackRef = useRef(callback);
>   const cleanerRef = useRef<() => void>();
> 
>   useEffect(() => {
>     callbackRef.current = callback;
>   });
> 
>   useEffect(() => {
>     const timer = setInterval(() => {
>       callbackRef.current();
>     }, ms);
>     cleanerRef.current = () => clearInterval(timer);
>   }, []);
> 
>   return () => {
>     cleanerRef.current?.();
>   };
> }
> 
> function App() {
>   const [num, setNum] = useState(0);
> 
>   useInterval(() => {
>     setNum(num + 1);
>     console.log(num);
>   }, 500);
> 
>   return <div>{num}</div>;
> }
> 
> export default App;
> ~~~
