`Immer` 主要用于快速基于源数据生成一个新的数据，且不会改变源数据。

安装：

~~~bash
$ npm i immer
~~~

`Immer` 的要点在于 `produce` 的使用：

~~~typescript
 import produce from "immer"
 
 const baseState = [
     {
         title: "Learn TypeScript",
         done: true
     },
     {
         title: "Try Immer",
         done: false
     }
 ]
 ​
 const nextState = produce(baseState, draftState => {
     draftState.push({title: "Tweet about it"})
     draftState[1].done = true
 })
~~~

`draftState` 是为 `baseState` 创建的一个 `proxy` 对象，对于 `draftState` 的操作不会影响到 `baseState`。最终对 `draftState` 的操作会被映射到一个新的对象中，作为 `produce()` 的返回值返回。

