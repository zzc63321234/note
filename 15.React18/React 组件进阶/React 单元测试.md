`create-react-app` 工具默认使用 `@testing-library/react` 这个包进行单元测试。

如果遇到类型问题，可以更新下 `@testing-library/jest-dom` 的版本。

默认生成的 `App.test.tsx` 如下：

> ~~~tsx
> import React from "react";
> import { render, screen } from "@testing-library/react";
> import App from "./App";
> 
> // 定义一个测试用例，名称为 "renders learn react link"
> test("renders learn react link", () => {
>   // 使用 render 方法渲染 App 组件
>   render(<App />);
>   // 使用 screen.getByText 方法获取页面上的文本内容为 "learn react" 的元素
>   const linkElement = screen.getByText(/learn react/i);
>   // 使用 expect 和 toBeInTheDocument 方法断言该元素是否在文档中
>   expect(linkElement).toBeInTheDocument();
> });
> ~~~

其它形式的测试用例示例：

> ~~~tsx
> import React from "react";
> import { render, screen } from "@testing-library/react";
> import App from "./App";
> 
> // 定义一个测试用例，名称为 "renders learn react link"
> test('renders learn react link', () => {
>   // 使用 render 方法渲染 App 组件，并获取其容器元素
>   const { container } = render(<App />);
>   // 在容器元素中查找类名为 'App-link' 的元素
>   const linkElement = container.querySelector('.App-link');
> 
>   // 断言该元素的文本内容是否匹配 'learn react'
>   expect(linkElement?.textContent).toMatch(/learn react/i)
> });
> ~~~
>
> 相比于默认生成的 `App.test.tsx`  中使用  `screen.getByText()` 方法获取 `DOM`，这里使用 `render()` 方法获取到容器元素，再使用 `DOM API` 获取 `DOM`。
>
> 其中 `expect` 函数是测试框架中的一个全局函数，用于创建断言。可以使用 `expect` 函数和一系列的匹配器（例如 `toBe`、`toEqual` 等）来测试值是否满足某些条件。

`fireEvent` 对象可用于触发测试的组件中的事件：

> 比如测试以下 `Toggle` 组件：
>
> ~~~tsx
> import { useCallback, useState } from 'react';
> 
> function Toggle() {
> 
>     const [status, setStatus] = useState(false);
> 
>     const clickHandler = useCallback(() => {
>         setStatus((prevStatus) => !prevStatus);
>     }, []);
> 
>     return (
>         <div>
>             <button onClick={clickHandler}>切换</button>
>             <p>{status ? 'open' : 'close' }</p>
>         </div>
>     );
> }
> 
> export default Toggle;
> ~~~
>
> 该组件中点击按钮会切换页面上的 `p` 元素中的 `"open"` 和 `"close"` 文本 。
>
> 可以使用 `fireEvent` 方法在测试用例中触发组件中的事件：
>
> ~~~tsx
> // 导入 @testing-library/react 库的 render 和 fireEvent 方法
> import { render, fireEvent } from '@testing-library/react';
> // 导入 Toggle 组件
> import Toggle from './Toggle';
> 
> // 定义一个名为 'toggle' 的测试用例
> test('toggle', () => {
>   // 使用 render 方法渲染 Toggle 组件，并获取其容器元素
>   const { container } = render(<Toggle/>);
> 
>   // 断言容器元素中的 'p' 元素的文本内容是否为 'close'
>   expect(container.querySelector('p')?.textContent).toBe('close');
> 
>   // 触发容器元素中的 'button' 元素的点击事件
>   fireEvent.click(container.querySelector('button')!)
> 
>   // 断言容器元素中的 'p' 元素的文本内容是否为 'open'
>   expect(container.querySelector('p')?.textContent).toBe('open');
> })
> ~~~

`waitFor()` 方法可用于测试异步内容：

> 比如要测试三秒后 `p` 元素的文本内容是否为 `"open"`:
>
> ~~~tsx
> await waitFor(() => expect(container.querySelector('p')?.textContent).toBe('open'), {
>     timeout: 3000
> });
> ~~~

`act()` 方法用于包裹对组件或 `Hook` 的操作：

> ~~~tsx
> import { render, fireEvent, waitFor } from '@testing-library/react';
> import { act } from 'react-dom/test-utils';
> import Toggle from './Toggle';
> 
> test('toggle', async () => {
>   const { container } = render(<Toggle/>);
>   expect(container.querySelector('p')?.textContent).toBe('close');
> 
>   act(() => {
>     fireEvent.click(container.querySelector('button')!)
>   })
> 
>   await waitFor(() => expect(container.querySelector('p')?.textContent).toBe('open'), {
>     timeout: 3000
>   });
> })
> ~~~
>
> 这样使得测试中的操作更接近在浏览器中的操作。但是像 `render`、`fireEvent` 这样的函数已经默认使用了 `act()`，因此不需要像上面示例那样显式调用 `act()`。
>
> 需要使用 `act()` 的地方见下面的 `renderHook()` 示例。

`renderHook()` 方法用于测试 `Hook`：

> `renderHook()` 传入一个【调用该 `Hook` 的函数】并进行调用后，会执行该 `Hook`，并得到 `RenderHookResult`。
>
> 通过 `RenderHookResult.result.current` 可以得到该 `Hook` 运行后的返回值。接着就可以操作这些 `hook` 的返回值来进行测试。
>
> 例如对自带的 `useState` 进行一个简单的单测示例：
>
> ~~~tsx
> import { renderHook } from "@testing-library/react";
> import { act } from "react-dom/test-utils";
> test("test useState", () => {
>   // 使用 renderHook 函数来模拟一个 Hook，这里模拟的是 useState Hook
>   const view = renderHook(() => useState(1));
> 
>   // 验证初始状态是否为 1
>   expect(view.result.current[0]).toBe(1);
> 
>   // 使用 act 函数来模拟用户操作，这里模拟的是更新状态的操作
>   act(() => {
>     view.result.current1;
>   });
>   
>   // 验证状态是否已经被成功更新为 2
>   expect(view.result.current[0]).toBe(2);
> });
> ~~~

