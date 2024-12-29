- `React.createElement()`

  - `React.createElement()` 方法是 `React` 库中用于创建和返回表示 `React` 元素的方法。这个方法接收三个参数：

    1. `type`：这是一个字符串或者一个 `React` 组件，它指定了要创建的元素或组件类型。如果是字符串，它表示原生 `DOM` 标签名称，例如 `'div'`、`'span'` 等。如果是 `React` 组件，它可以是函数式组件或者类组件。
    2. `props`：这是一个对象，包含了要传递给组件或元素的属性。属性可以包括事件处理函数、样式对象或者自定义属性等。如果没有属性需要传递，可以设置为 `null`。
    3. `children`：这是一个可选参数，表示子元素或子组件。可以单个传递，也可以作为数组传递。`children` 可以是文本、`React` 元素、`React` 组件或者它们的组合。

  - 下面是一个简单的例子：

    ```javascript
    const element = React.createElement(
      'div',
      { className: 'example' },
      'Hello, world!'
    );
    ```

    - 这个例子创建了一个带有类名 `"example"` 的 `div` 元素，并包含文本 `"Hello, world!"`。你还可以嵌套 `React.createElement()` 来创建具有子元素的结构。
    - `element` 是一个 `React` 元素，也就是 `React` 中的虚拟 `DOM`

- 如果 `JSX` 中我封装了一个函数组件 `MyComponent`，那么

  ~~~jsx
  const a = <MyComponent/>
  const b = React.createElement(MyComponent)
  ~~~
  
  - `a` 和 `b` 都是 `React` 元素，即虚拟 `DOM`
  - `a` 和 `b` 是内容是完全一样的两个 `js` 对象（当然 `a === b` 为 `false`），即这两种创建 `React` 元素的方式效果是等价的（以下都使用 `a` 指代创建出来的 `React` 元素）
  - `a.type` 指向 `MyComponent`
  - 当 `a` 被创建出来时，`MyComponent` 并没有被调用，因此组件中的数据，包括组件中的元素都没有被创建，只是用函数描述了它们
  - 如果 `a` 最终被传入了根组件，根组件被传入到 `ReactDOM.render` 中调用时，才会将 `a` 中定义的数据和元素都创造出来