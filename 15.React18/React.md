## `React` 开发依赖

- 三个必须依赖
  - `react`
    - 包含 `react web` 和 `react-native` 所共同拥有的核心代码
  - `react-dom`
    - 针对 `react web` 和 `react-native` 会进行不同的操作
      - `web` 端
        - 将 `jsx` 最终渲染成真实 `DOM`，显示在浏览器中
      - `native` 端
        - 将 `jsx` 最终渲染成原生控件（如 `Android` 中的 `Button`，`IOS` 中的 `UIButton`）
  - `babel` （如果使用 `jsx` 编写 `react` 代码）
    - `babel` 可以将 `jsx` 转换成 `React.createElement`
    - 如果自己使用 `React.createElement` 编写代码，也可以不用 `babel`

## 基本使用

- 直接在 `html` 中写 `React`

  ~~~jsx
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  
  <body>
    <div id="root"></div>
  
    <!--  CDN 方式引入依赖  -->
    <script src="https://unpkg.com/react@18.2.0/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18.2.0/umd/react-dom.development.js" crossorigin></script>
    <script src=" https://unpkg.com/babel-standalone@6.26.0/babel.min.js"></script>
  
    <!-- 使用 jsx 语法时，需设置 type 属性启用 babel -->
    <!-- babel 转换后的代码默认使用严格模式 -->
    <script type="text/babel">
      /* 将 jsx 内容渲染到 root 获取到的元素中 */
      /*  在 React18 中，一个页面可以创建多个 root  */
  
      const messages = {
        current: 0,
        list: ['Hello World', 'Hello React', 'Hello JSX']
      }
  
      function switchMessage() {
        const { list } = messages
        messages.current = (messages.current !== list.length - 1) ? messages.current + 1 : 0
        render()
      }
  
      /* 通过将 dom 传入 ReactDOM.createRoot 调用得到 root 对象 */
      const root = ReactDOM.createRoot(document.querySelector('#root'))
  
      /* 封装一个 render 函数，每次调用 render 都会重新渲染 root */
      function render() {
        /* 将 jsx 传入 root.render 调用以渲染 jsx */
        root.render((
          <div>
            <h2>{messages.list[messages.current]}</h2>
            <button onClick={switchMessage}>switch</button>
          </div>
        ))
      }
      render()
    </script>
  
  </body>
  
  </html>
  ~~~

### `ReactDOM` 相关 `api`

- `ReactDOM.createRoot()`
  - 接收一个 `html` 元素，渲染的内容将挂载在该元素上
  - 返回一个 `React` 根（`root`）
  - `Rreact 18` 中可以创建多个 `root`
- `root.render()`
  - 接收要渲染的根组件
  - 将组件内容渲染到对应的元素上

### 不可变数据

- `React` 中所有的 `state` 都视为不可直接修改的
- 不要直接修改 `state` 中的对象，修改对象时，要为它创建一个新的版本，再替换这个对象

### 事件绑定

- `React` 中的事件监听使用的属性名采用小驼峰命名，它并不是原生 `DOM` 中的事件监听

#### 事件绑定中的 this 指向

- `React` 事件触发时，调用的回调函数中 `this` 指向 `undefined`

  - 因为 `React` 内部调用回调函数时，无法确定 `this` 应该指向谁

  - 在使用类式组件时，需要自行将 `this` 绑定给组件实例
    - 方案一：使用 `bind` 显式绑定
    
    - 方案二：使用 `Instance Class Fields` （实例类字段）语法
    
      - 例
    
        ~~~jsx
        class App extends React.Component {
          foo = () => {
            console.log(this)
          }
          
          render() {
            return (
              <div>
                <button onClick={this.foo}>show this</button>
              </div>
            )
          }
        }
        ~~~
        
      - 使用 `Instance Class Fields` 语法声明的箭头函数没有自己的 `this`，但在创建组件实例时声明的该箭头函数中能访问到的 `this` 将永远指向它所处的词法环境，即组件实例
      
      - 但这样做会使得每当创建一个新的组件实例时（组件复用时），使用 `Instance Class Fields` 语法声明的箭头函数都会被重新创建
      
    - 方案三：直接绑定一个箭头函数
    
      - 例
    
        ~~~jsx
        class App extends React.Component {
          foo() {
            console.log(this)
          }
          
          render() {
            return (
              <div>
                <button onClick={() => this.foo()}>show this</button>
              </div>
            )
          }
        }
        ~~~

#### 事件参数传递

- 事件触发时，`React` 会将 `event` 对象作为第一个参数传入事件函数中

  - 该 `event` 对象是 `React` 对原生 `event` 对象包裹后的对象

- 如果需要在绑定事件函数时传递其它参数，可以使用不同方法

  - 如果采用上述方案一或方案三，则可以方便地在绑定事件函数时传递其它参数

  - 但这两种方案接收参数的顺序不同

  - 例

    ~~~jsx
    class App extends React.Component {
      foo1(arg1, arg2, e) {
        console.log(this, e, arg1, arg2)
      }
    
      foo2(e, arg1, arg2) {
        console.log(this, e, arg1, arg2)
      }
    
      render() {
        return (
          <div>
            <button onClick={this.foo1.bind(this, '参数1', '参数2')}>show this</button>
            <button onClick={(e) => this.foo2(e, '参数1', '参数2')}>show this</button>
          </div>
        )
      }
    }
    ~~~

### 条件渲染

- `React` 中可以很灵活地在 `render` 方法中使用 `JSX` 代码实现条件渲染
- 常见的实现方式有
  - 条件判断语句
  - 三元运算
  - 逻辑与运算
  - 控制元素的 `style` 属性中的 `display` 属性的值是否为 `none`，以实现类似 `Vue` 中 `v-show` 指令的效果

### 列表渲染

- `React` 中可以很灵活地在 `JSX` 扩展语法的 `{}` 中嵌入包含 `ReactElement` 的数组来实现列表渲染
- `React` 列表渲染中最常用的数组方法是 `Array.prototype.map`
- 列表和列表项不局限于 `ol` / `ul` 和 `li` 元素
- `JSX` 中使用 `{}` 嵌入包含 `ReactElement` 的数组时，`React` 会将其识别为列表结构，为了提高 `diff` 算法效率，列表中的每一项元素需要加上 `key` 属性，列表项的 `key` 值需要是唯一不重复的，否则 `React` 会在控制台发出警告

## 声明式编程

- `React` 通过虚拟 `Virtual DOM` 实现声明式编程
- `Virtual DOM` 是一种编程理念
  - `UI` 以 `JS` 对象的方式保存在内存中
  - 可以通过 `ReactDOM.render` 让虚拟 `DOM` 和真实 `DOM` 同步起来，这个过程叫做协调（`Reconciliation`）
  - 这种编程方式赋予了 `React` 声明式的 `API`
    - 开发者只需告诉 `React` 希望让 `UI` 是什么状态，而不需要自行操作 `DOM` 
    - `React` 来确保 `DOM` 和这些状态是匹配的

## 组件化

- `React` 中的组件按不同方式可以分成很多类型
  - 根据组件定义方式，可以分为
    - 类式组件（`Class Component`）
    - 函数式组件（`Functional Component`）
  - 根据组件内部是否有状态需要维护，可以分为
    - 无状态组件（`Stateless Component`）
    - 有状态组件（`Stateful Component`）
  - 根据组件的职责，可以分为
    - 展示型组件（`Presentational Component`）
    - 容器型组件（`Container Component`）
- 不同组件的关注点
  - 函数式组件、无状态组件、展示型组件更关注 `UI` 展示
  - 类式组件、有状态组件、容器型组件更关注数据逻辑
  - 但这些组件概念并非一一对应，而是互相重叠的

### 类式组件

- 如何封装一个类式组件
  - 定义一个类
    - 类名即组件名
    - `jsx` 中，小写的标签会被认为是 `HTML` 元素，因此组件名必须大写
    - 该类继承自 `React.Component`
  - 在类中需实现当前组件的 `render` 方法
    - `render` 方法中返回 `jsx` 内容
    - `render` 方法最终会被 `React` 调用，其中的 `this` 被绑定成了类的实例（组件实例）
  - 组件方法
    - 组件方法被事件触发时，`this` 默认指向 `undefined`，需自行将其绑定为组件实例
  - 动态数据
    - 动态数据保存在 `this.state` 对象中
    - 若要更新动态数据，需调用 `this.setState(newState)` 方法
  
- `render` 方法
  
  - 当 `render` 被调用时，它会检查 `this.props` 和 `this.state` 的变化并返回以下类型之一：
    - `ReactElement`
    - 数组或 `fragments`
      - 使得 `render` 方法可以返回多个元素
    - `Portals`
      - 可以渲染子节点到不同的 `DOM` 子树中
    - `string` 或 `number` 类型
      - 它们在 `DOM` 中会被渲染为文本节点
    - `null`、`undefined`、`boolean` 类型
      - 什么都不渲染
  
- `setState`
  
  - `setState` 是从 `React.Component` 中继承过来的
  - 定义在 `Component.prototype.setState` 中
  - 接收参数
  
    - `stateUpdate`
      - 用于修改组件状态的对象或函数
        - 对象
          - 一个包含更新状态属性的键值对的对象
        - 函数
          - 接收两个参数
            - 组件状态 `state` 和组件属性 `props`
            - `state` 参数接收到的对象并不等同于 `this.state`
              - 在同步代码中多次调用 `this.setState` 时，`state` 参数接收到的对象已经包含了之前的 `this.setState` 的更新
              - 而当这些所有的 `this.setState` 都执行完后，最终合并后的状态才会更新给 `this.state`，接着会执行 `render` 函数
          - 返回一个包含更新状态属性的键值对的对象
          - 注意，当组件处于 `<React.StrictMode>` 标签下，且处于开发环境下时，该函数被调用时会被调用两次，用于检查是否产生副作用
    - `callback`（可选）
      - 当前状态已更新且组件重新渲染后，会调用这个回调函数
  - `setState` 方法默认是异步的
    - `setState` 如果在 `React` 定义的函数（如 `React` 的事件回调，`React` 的生命周期函数等）中被调用，则会在这些函数中进行异步的批处理
      - 如果每次调用 `setState` 都调用一次 `render` 函数，则会使得生成虚拟 `DOM`，进行 `diff` 算法，转换真实 `DOM` 等操作频繁进行
        - 因此每次调用 `setState` 时，会将此次改动放入一个队列中，当同步代码执行完毕后，将这些改动合并，并调用 `render` 函数
      - 另外，`this.state` 的值也会在 `render` 函数即将调用前才被更新
        - 如果将 `this.state` 中的数据赋值给了子组件的 `props`，那么只有当 `render` 函数调用后，子组件的 `props` 才会更新
        - 因此 `this.state` 的更新和 `render` 函数的调用保持同步，使得子组件的 `props` 中的数据的值和其来源的值保持一致，避免一些不同步造成的问题
    - 在 `React 17` 及之前的版本中，如果在一个不是由 `React` 定义的函数中（如 `setTimeout` 或原生 `DOM` 事件回调）中调用 `setState`，它会在该函数内触发同步更新，并且也会同步调用 `render` 函数，并不会异步进行批处理
    - 在 `React 18` 中，引入了新的更新调度机制，无论在什么情况下（除了在 `flushSync` 中）调用 `setState`，都会异步进行批处理
    - 如果希望 `setState` 的更新同步执行，可以从 `react-dom` 库中获取 `flushSync`  函数，在该函数中调用 `setState`
    
  
- 类式组件的基本实现

  ~~~jsx
  class App extends React.Component {
    // 组件数据
    constructor() {
      super()
      // 动态数据必须放在 this.state 中
      this.state = {
        messages: {
          current: 0,
          list: ['Hello World', 'Hello React', 'Hello JSX']
        }
      }
      this.switchMessage = this.switchMessage.bind(this)
    }
    // 组件方法
    switchMessage() {
      const { messages } = this.state
      const { list } = messages
      messages.current = (messages.current !== list.length - 1) ? messages.current + 1 : 0
      this.setState({
        messages: messages
      })
    }
    // render 方法
    render() {
      const { messages } = this.state
      return (
        <div>
          <h2>{messages.list[messages.current]}</h2>
          <button onClick={this.switchMessage}>switch</button>
        </div>
      )
    }
  }
  ~~~

#### 生命周期

- 生命周期是一个抽象的概念，用以描述组件处于什么阶段

- `React` 检测到组件进入了哪个阶段，就会对组件内部实现的某些函数进行回调，这些函数就是生命周期函数

- 一般说到 `React` 生命周期，主要指的是类式组件的生命周期，因为函数式组件在没有 `hooks` 自身是没有生命周期函数的

- 类式组件生命周期流程

  ![](./images/类式组件生命周期流程.png)

  ![](./images/类式组件生命周期流程(2).webp)

- 常用的生命周期函数

  - `constructor`

    - 就是类的构造器，在类实例化成组件时被调用

    - 如果不需要初始化 `state` 则不需要实现 `constructor` 方法

      - 如果没有在类式组件中定义 `constructor`，`React` 会隐式提供一个 `constructor`，类似于以下形式

        ~~~js
        constructor(...args) {
          super(...args)
        }
        ~~~

  - `componentDidMount`

    - 当组件被挂载（插入 `DOM` 树中）立即被调用
    - 通常进行的操作
      - 依赖于 `DOM` 的操作
      - 发送网络请求
      - 添加一些订阅

  - `componentDidUpdate`

    - 当组件更新后立即被调用，首次渲染时不会执行
    - 接收参数
      - `preProps`
        - 更新之前的 `props`
        - 可以与 `this.props` 进行比较来确定发生了什么改变
      - `preState`
        - 更新之前的 `state`
        - 可以与 `this.state` 进行比较来确定发生了什么改变
      - `snapshot`（可选）
        - 如果实现了 `getSnapshotBeforeUpdate` 方法，那么接收它的返回值

  - `componentWillUnmount`

    - 在组件被卸载之前被调用
    - 在此方法中执行清理操作，如
      - 清除 `timer`
      - 取消网络请求
      - 取消订阅

- 不常用的生命周期函数

  - `getDerivedStateFromProps`
    - 用于将新的 `props` 映射到组件内部的 `state` 中
    - 当组件第一次被挂载，以及组件每次更新前被调用
    - 接收参数
      - `nextProps`
        - 组件即将用来渲染的下一个 `props`
      - `prevState`
        - 之前的 `state`
    - 该方法需返回一个对象
      - 该对象会被合并到当前的 `state` 中
      - 如果返回 `null` 表示无需更新 `state`
  - `shouldComponentUpdata`
    - `Updating` 阶段，`React` 会在 `render` 方法执行之前调用它
    - 用于确定是否跳过 `render`，即不进行重新渲染
    - 接收参数
      - `nextProps`
        - 组件即将用来渲染的下一个 `props`
        - 可以与 `this.props` 进行比较来确定发生了什么改变
      - `nextState`
        - 组件即将渲染的下一个 `state`
        - 可以与 `this.state` 进行比较来确定发生了什么改变
      - `nextContext`
        - 组件即将渲染的下一个 `context`
        - 可以与 `this.context` 进行比较来确定发生了什么改变
        - 仅当你指定了 `static contextType`（更新的）或 `static contextTypes`（旧版）时才可用
      - 返回值
        - 如果希望重新渲染则返回 `true`
        - 如果希望跳过重新渲染则返回 `false`
  - `getSnapshotBeforeUpdate`
    - `Updating` 阶段，在 `render` 方法被执行后，真实 `DOM` 更新前被调用
    - 用于在组件完成更新前创建一个快照，捕获一些更新前的信息（如滚动位置）
    - 接收的前两个参数与 `componentDidUpdate` 相同
    - 返回值
      - 会作为 `componentDidUpate` 的第三个参数
      - 如果不需要传递数据，需返回 `null`

### 函数式组件

- 函数式组件是用函数定义的，这个函数返回的内容等同于类式组件中的 `render` 方法返回的内容
- 函数式组件的特点（没有 `hooks` 时）
  - 本身没有生命周期函数
  - `this` 关键字不能指向组件实例（因为没有组件实例）
  - 没有内部状态

### 组件间通信

#### `props` 传递

- 父组件传递给子组件数据

  - 父组件通过给子组件标签添加属性以传递 `props` 数据

  - 子组件通过 `props` 参数获取父组件传递的数据

    - `props` 参数会以对象的形式传递给

      - 类式组件中的 `constructor` 方法的第一个参数

        - `props` 在父类 `React.Component` 中已经定义，所以需要在 `constructor` 中使用 `super(props)` 

        - 尽管在 `constructor` 中执行 `this.props = props` 以替代 `super(props)` 也可以完成 `props` 映射，但并不符合 `React` 的最佳实践

        - 如果没有在类式组件中定义 `constructor`，`React` 会隐式提供一个 `constructor`，类似于以下形式

          ~~~js
          constructor(...args) {
            super(...args)
          }
          ~~~

      - 函数式组件中的第一个参数

- 子组件传递给父组件数据

  - 父组件传递给子组件一个回调函数
  - 子组件通过 `props` 接收这个回调函数，传入参数并调用

- 使用 `Spread Attributes` 方式传递 `props`

  ~~~jsx
  // 如果存在组件 MyComponent，存在一个对象 obj
  <MyComponent { ...obj }/> // 相当于将 obj 中的键值对作为 props 传入 MyComponent 中
  ~~~

- `PropTypes`

  - 在没有使用 `Flow` 或 `TypeScript` 的情况下对 `props` 的数据进行类型校验

  - 在 `React v15.5` 开始放入了 `prop-types` 库中，在此之前内置于 `React` 中，即 `React.PropTypes`

  - [使用 PropTypes 进行类型检查 – React (reactjs.org)](https://zh-hans.legacy.reactjs.org/docs/typechecking-with-proptypes.html)

  - 基本使用

    - 通过给组件的 `propTypes` 属性赋值一个对象定义类型检测

      ~~~javascript
      import {Component} from 'React'
      import PropTypes from 'prop-types'
      // ......
      // 假设之前已经定义了 MyComponent 类式组件
      // 之后通过为 MyComponent.propTypes 赋值一个对象以设置类型校验
      MyComponent.propTypes = {
        list: PropTypes.array.isRequired, // 表示 list 是一个数组，且必需传递
        name: PropTypes.string,
        age: PropTypes.number
      }
      ~~~

    - 通过给组件的 `defaultTypes` 属性赋值一个对象定义默认值

      ~~~javascript
      import {Component} from 'React'
      import PropTypes from 'prop-types'
      // ......
      // 假设之前已经定义了 MyComponent 类式组件
      // 之后通过为 MyComponent.propTypes 赋值一个对象以设置类型校验
      MyComponent.defaultProps = {
        name: 'Stanger',
      }
      ~~~

    - 在 `ES2022` 支持了类公共静态字段（`class public static fields`）后，可以使用该语法定义

      ~~~javascript
      import {Component} from 'React'
      import PropTypes from 'prop-types'
      
      class MyComponent extends Component {
        static defaultProps = {
          name: 'stranger'
        }
        static propTypes = {
          list: PropTypes.array.isRequired, 
          name: PropTypes.string,
          age: PropTypes.number
        }
        // ...
      }
      ~~~

    - 如果是函数式组件，也是一样的方式

      ~~~javascript
      import PropTypes from 'prop-types'
      // ......
      // 假设之前已经定义了 MyComponent 函数式组件
      // 之后通过为 MyComponent.propTypes 赋值一个对象以设置类型校验
      MyComponent.propTypes = {
        list: PropTypes.array.isRequired, 
        name: PropTypes.string,
        age: PropTypes.number
      }
      MyComponent.defaultProps = {
        name: 'Stanger',
      }
      ~~~

#### 插槽功能的实现

- 方案一：

  - 如果存在一个组件 `MyComponent`

    ~~~jsx
    // 单个插槽的情况
    
    const elCreatedByJSX = (
      <MyComponent>
        <div>这是一个插槽</div>
      </MyComponent>
    )
    
    const slot = React.createElement('div', null, '这是一个插槽')
    const elCreatedByJS = React.createElement(MyComponent, null, slot)
    
    // elCreatedByJSX 和 elCreatedByJS 是结构和内容完全等同的两个 React 元素
    
    console.log(elCreatedByJS.props.children === slot) // true
    // 在 MyComponent 组件中，通过 this.props.children 以获取到插槽内容
    ~~~

    ~~~jsx
    // 多个插槽的情况
    
    const elCreatedByJSX = (
      <MyComponent>
        <div>插槽1</div>
        <div>插槽2</div>
      </MyComponent>
    )
    
    const slot1 = React.createElement('div', null, '插槽1')
    const slot2 = React.createElement('div', null, '插槽2')
    const elCreatedByJS = React.createElement(MyComponent, null, [slot1, slot2])
    
    // elCreatedByJSX 和 elCreatedByJS 是结构和内容完全等同的两个 React 元素
    
    console.log(elCreatedByJSX.props.children[0] === slot1) // true
    console.log(elCreatedByJSX.props.children[1] === slot2) // true
    
    
    // 补充：
    // - 运行定义 elCreatedByJS 的代码时 React 会警告：Each child in a list should have a unique "key" prop
    // - 而运行定义 elCreatedByJSX 的代码时不会有该警告
    const elCreatedByJSX2 = (
      <MyComponent>
        {[<div>插槽1</div>, <div>插槽2</div>]}
      </MyComponent>
    )
    // - 运行定义 elCreatedByJSX2 的代码时也会有该警告
    // - 但是实际上 elCreatedByJSX、elCreatedByJSX2、elCreatedByJS 都是内容相同的 React 元素
    ~~~

  - 组件中的 `props.children` 接收到的是

    - 如果在组件标签的标签体中插入一个元素，那么接收到的是这个元素
    - 如果在组件标签的标签体中插入多个元素，那么接收到的是这些元素组成的数组
    - 可以通过 `propTypes` 限制 `children` 的类型为 `PropTypes.element`，以限制插槽只能有一个根标签

- 方案二：
  - 直接将元素通过 `props` 属性传递给子组件
  - 相比于方案一更灵活

#### `Context`

- `Context` 可以让父组件为它下面的整个组件树提供数据，且可以跨越多层级传递，不必显式地逐层传递

- 相关 `API`
  - `React.createContext(defaultValue)`
  
    - 参数
  
      - `defaultValue`
        - 默认数据
        - 当在没有被某个 `context.Provider` 包裹的组件中尝试获取该 `context` 的上下文数据时，获取到的就是 `defaultValue`
  
    - 返回值
  
      - 一个 `context` 对象
  
      - `context` 对象本身不包含任何信息，它只是用来给组件提供（`provide`）或 读取（`consume`）
  
      - `context` 对象的属性
  
        - `context.Provider`
  
          - 是一个组件，为被它包裹的组件提供上下文的值
  
          - `Provider` 组件接收一个 `value` 属性，传递给被包裹的组件，无论它们处于多深的层级都能读取到
  
            ~~~jsx
            <ThemeContext.Provider value={ theme }>
              <Button />
            </ThemeContext.Provider>
            ~~~
  
          - 当 `Provider` 的 `value` 值发生变化时，内部被包裹的组件都会重新渲染
  
        - `context.Consumer`
  
          - 是一个组件，用于读取上下文的值
  
          - 将一个函数作为 `props.children` 传入
  
            - 将距离最近的对应 `context.Provider.props.value` 的值作为第一个参数接收
            - 渲染时，该函数会被执行，返回值将被转换为 `React` 元素渲染
  
          - `React 18` 函数式组件中更推荐使用 `useContext()` 来读取上下文
  
          - 示例
  
            ~~~jsx
            function Button() {
              // 🟡 遗留方式 (不推荐)
              return (
                <ThemeContext.Consumer>
                  {theme => (
                    <button className={theme} />
                  )}
                </ThemeContext.Consumer>
              )
            }
            ~~~
  
            ~~~jsx
            function Button() {
              // ✅ 推荐方式
              const theme = useContext(ThemeContext)
              return <button className={theme} />
            }
            ~~~
  
  - `Class.contextType`
  
    - 如果想从类式组件中读取 `context`，可以用 `static contextType` 指定 
  
    - 之后便可以通过 `this.context` 读取
  
    - 例
  
      ~~~jsx
      class Button extends Component {
        static contextType = ThemeContext
      
        render() {
          const theme = this.context
          return (
            <button className={theme} />
          )
        }
      }
      ~~~
    
  - `useContext`
  
    - 该 `Hook` 用于在函数式组件中使用共享的 `Context`
  

### 性能优化

#### `React` 中 `UI` 的更新流程

- 流程
  - `props` 或 `state` 发生了改变后，调用 `render` 方法，创建一棵虚拟 `DOM` 树
  - `React` 判断新的虚拟 `DOM` 树和更新前的虚拟 `DOM` 树的差别
    - 如果进行完全比较，那么即使是最先进的算法，其复杂度为 **O(n<sup>3</sup>)**，`n` 是树中元素的数量
      - [editsurvey.dvi (ua.es)](https://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)
    - `React` 不对它们进行完全比较，将复杂度优化为了 **O(n)**
      - 同层节点之间相互比较，不会跨节点比较
      - 不同类型的节点，该节点及其子节点直接都被视为差异
      - 通过 `key` 来指定节点在不同的渲染下保持稳定
  - 计算出的差异更新到真实 `DOM` 中

#### `render` 函数调用

- 当数据更新时，对应组件的 `render` 函数被调用，该组件的所有子组件的 `render` 函数也会被调用（如果是函数式组件则是函数本身被调用，以下省略）
- 对于数据没有更新的子组件来说，白白执行了一次 `render` 函数，如何避免：
  - 类式组件
    - 可以通过 `shouldComponentUpdate` 方法控制 `render` 函数是否被调用
    - 对于类式组件，可以将类继承自 `React.PureComponent`
      - 继承自 `React.PureComponent` 的类式组件会自行判断是否需要调用 `render` 函数
      - `PureComponent` 会使用 `shallowEqual` 方法对组件中更新前后的 `state` 和 `props` 进行浅层比较
      - `shallowEqual` 接收两个对象作为参数，如果两个对象第一层属性以及属性值都相等（对于引用类型的属性，则是引用相同），则返回 `true`
      - 如果 `!shallowEauql(oldProps, newProps) || !shallowEauql(oldState, newState)` 为真，就会调用 `render` 函数
  - 函数式组件 
    - 可以使用高阶组件 `React.memo`
    - 将函数组件作为参数传入 `memo` 调用，返回的组件会在更新时自动判断组件是否需要重新执行，减少不必要的更新
    - 该方式使用的也是浅层比较

### `ref`

- `React` 的开发模式中一般不需要直接操作原生 `DOM`，但有一些情况需要
  - 管理焦点、文本选择或媒体播放
  - 触发强制动画
  - 集成第三方 `DOM` 的库
- 可以通过给 `React` 元素设置 `ref` 属性以获取原生 `DOM` 或组件实例对象，如果是 `html` 元素则会获取原生 `DOM`，如果是类式组件元素则会获取组件实例对象
- `React` 元素的 `ref` 属性可传入的内容有三种
  - 传入字符串（已过期）
    - 之后再类式组件的 `this.refs` 中通过传入的字符串为键，获取到对应的原生 `DOM`
  - 传入 `React.createRef()` 返回的对象，即 `ref` 对象
    - 将 `ref` 对象传入 `ref` 属性中
    - 之后可以通过该 `ref` 对象的 `current` 属性获取到对应的原生 `DOM`
  - 传入一个函数
    - 该函数接收一个参数
      - 当该 `React` 元素被挂载时，该函数接收当前元素的 `DOM` 为参数并执行
      - 当该 `React` 元素被卸载时，该函数接收 `null` 为参数并执行
      - 当该 `React` 元素被更新时，视为旧的被卸载，新的被挂载
    - 可以在该函数中操作原生 `DOM`
- 函数式组件中的 `ref` 转发 
  - `ref` 属性应用于函数式组件时，没有组件实例可以获取，但可以通过 `ref` 转发获得该函数式组件内某个元素的原生 `DOM`
  - 将函数式组件作为参数传入 `forwardRef` 高阶组件调用，返回得到可以转发 `ref` 的函数式组件
  - 此时该函数式组件可以接收第二个参数 `ref`
  - 将 `ref` 的值作用在函数式组件内部元素的 `ref` 属性上，那么父组件可以通过该 `ref` 获得该元素的 `DOM` 

### 受控组件

- `React` 中，受控组件（`Controlled Component`）是一种控制表单字段的方式

  - 受控组件把表单的状态数据保存在组件的 `state` 中，然后通过设置组件的 `state` 来控制表单字段的值
  - 通过这种方式控制取值的表单元素被称作受控组件
  - 一旦为表单元素设置了状态属性（如输入框的 `value` 属性，选框的 `checked` 属性），`React` 就会将该表单元素视为受控组件
    - 受控组件的状态会始终和状态属性中的数据绑定
    - 只有通过 `React` 更新状态属性中的数据，才能改变受控组件状态

- 一个基本的受控组件

  ~~~jsx
  import React, { PureComponent } from 'react'
  
  export class Test extends PureComponent {
    constructor() {
      super()
      this.state = {
        value: 111
      }
    }
    changeInput(e) {
      console.log(e.target.value)
      this.setState({ value: e.target.value })
    }
    render() {
      const { value } = this.state
      return (
        <div>
          <input type="text" value={value} onChange={(e) => { this.changeInput(e) }} />
        </div>
      )
    }
  }
  
  export default Test
  ~~~

- 不同表单元素常用的受控的方式

  | Element                     | Value property          | New value in the callback                                    |
  | :-------------------------- | :---------------------- | ------------------------------------------------------------ |
  | <input type = 'text' />     | value={string}          | e.target.value                                               |
  | <input type = 'checkbox' /> | checked={boolean}       | e.target.checked                                             |
  | <input type = 'radio' />    | checked={boolean}       | e.target.checked                                             |
  | <textarea />                | value={string}          | e.target.value                                               |
  | <select />                  | value={optionValue}     | e.target.value / e.target.selectedIndex / e.target.selectedOptions |
  | <select multiple />         | value={optionValueList} | e.target.value / e.target.selectedIndex / e.target.selectedOptions |

  - `select` 
    - `select` 标签作为受控组件时，不需要通过 `option` 标签的 `selected` 属性来控制哪一个被选中
    - 可以使用 `select` 标签的 `value` 属性，来指定对应的 `option` 的 `value` 属性
    - 如果是多选模式（设置了 `multiple` 属性），则 `value` 属性接收一个数组

- 非受控组件

  - 通过操作原生 `DOM` 节点来处理数据的表单元素称为非受控组件
  - 由于设置了状态属性的表单元素会被 `React` 视为受控组件，因此非受控组件设置默认值需要通过其它属性，如输入框通过 `defaultValue` 属性，选框通过 `checkedValue` 属性

### 高阶组件

- 高阶组件的定义
  - `Higher-Order Components`，简称 `HOC`
  - 高阶组件是参数为组件，返回值为新组件的函数
  - 高阶组件本身并不是组件
  
- 高阶组件本身并不是 `React API` 的一部分，而是基于 `React` 的组合特性而形成的设计模式

- 高阶组件的一些简单应用

  - 为组件添加额外的 `props`

    ~~~jsx
    function enhanceProps(OriginalComponent, otherProps) {
      return props => <OriginalComponent {...props} {...otherProps}>
    }
    ~~~

  - 将 `Context` 中的内容添加到组件的 `props` 中

    ~~~jsx
    function withContext(OriginalComponent, Context) {
      return props => {
        return (
          <Context.Consumer>
            {
              value = {
                return <OriginalComponent {...props} {...value}>
              }
            }
          </Context.Consumer>
        )
      }
    }
    ~~~

  - 根据条件渲染不同组件，以登录鉴权为例

    ~~~jsx
    function loginAuth(Page, LoginPage) {
      return props => {
        if (props.isLogin) {
          return <Page />
        } else {
          return <LoginPage />
        }
      }
    } 
    ~~~


### `Portals`

- `Portal` 提供了一种将子节点渲染到指定 `DOM` 节点的方案，该 `DOM` 节点可以在父组件外
- 类似于 `Vue` 中的 `Teleport`
- 基本使用
  - `ReactDOM.createPortal(child, container)`
    - 接收参数
      - `child`
        - 任何可渲染的 `React` 子元素，例如一个元素、字符串或 `fragment`
      - `container`
        - 一个 `DOM` 元素
    - 返回值是一个类型为 `ReactPortal` 的特殊 `React` 元素
    - 当 `ReactPortal` 被父组件渲染时，不会直接创建一个 `DOM` 节点，而是会让 `child` 在 `container` 中渲染

### `Fragment`

- `React.Fragement` 是一个内置组件，用于将子列表分组，而无需向 `DOM` 添加额外节点
- 类似于 `Vue` 中的 `template` 标签
- 也可以用一种短语法，以 `<></>` 的形式表示 `Fragment` 标签，但短语法形式无法为 `Fragment` 添加属性，因此也无法添加 `key` 属性

### `StrictMode`

- `React.StrictMode` 是一个内置组件，用于为后代元素触发额外的检查和警告
- `StrictMode` 不会向 `DOM` 添加额外节点
- `StrictMode` 仅在开发环境下运行，不会影响生产构建
- `React` 严格模式检查的内容
  - 检查意外的副作用
    - 一些函数，比如类式组件的 `constructor` ，以及 `setState` 方法中接收的函数会被调用两次，用于检查是否会产生副作用
  - 过期的 `API`，如：
    - 不安全的生命周期
    - 过时的 `ref API`
    - 废弃的 `findDOMNode` 方法
    - 过期的 `context API`

## `CSS` 方案

- `CSS` 原本的设计不适合组件化，适合组件化的 `CSS` 解决方案应符合：
  - 支持局部 `CSS`，使其拥有自己的作用域，而不污染其它组件内的元素
  - 支持动态 `CSS`，可以获取当前组件的状态，根据状态的变化生成不同的 `CSS` 样式
  - 支持所有的 `CSS` 特性，且编写简洁方便

### 内联样式方案

- 通过 `React` 元素的 `style` 属性，接收一个采用小驼峰命名为键名的 `JS` 对象以指定内联样式
- 优点
  - 样式不会影响到其它组件的元素
  - 可以动态获取当前 `state` 中的状态
- 缺点
  - 编辑器无法提供动态提示
  - 样式数量多时代码比较混乱
  - 无法使用伪类/伪元素等 `CSS` 特性

### `CSS Modules` 方案

- 如果直接 `import` 引入一个 `CSS` 文件，该文件中定义的样式会应用于全局，影响所有组件

- 可以使用 `css modules` 方案解决局部作用域问题

- `css modules` 方案可用于所有使用了类似于 `webpack` 配置的环境

- 在 `webpack` 环境中，配置 `webpack.config.js` 中的 `modules: true` 即可开启

- `React` 脚手架默认内置了 `css modules` 的配置

- 使用方式

  - 将 `*.css` / `*.less` / `*.scss` 等样式文件的文件名改为 `*.module.css` / `*.module.less` / `*.module.scss`

  - 在 `jsx` 中引入

    ~~~jsx
    import appStyle from "./App.module.css"
    ~~~

  - `App.module.css` 中指定的类名，会在打包时被重命名为一个不会重复的类名，使得不同模块中的类名不会冲突，以原本的类名作为键去访问 `appStyle` 中的属性，即可得到重命名后的类名

  - 如果 `App.module.css` 中为 `.title` 指定了样式，想要将该样式作用在一个 `div` 元素上，可以这么设置

    ~~~jsx
    <div className={appStyle.title}>这是一个标题</div>
    ~~~

- 缺点

  - 不支持动态 `CSS`

### `CSS-in-JS` 方案

- `CSS-in-JS` 是一种使用 `JS` 生成 `CSS` 样式的模式
- 该模式很契合 `All in JS` 的理念
- 比较流行的 `CSS-in-JS` 库
  - `styled-components`
  - `emotion`
  - `glamorous`

#### `styled-components`

- `styled-components` 主要设计用于在 `React` 中使用 `JS` 编写 `CSS`

- `styled-components` 通过**标签模板字面量**（`Tagged Template Literals`）的方式在模板字面量中编写 `CSS` 来生成样式

- 通过 `VSCode` 插件 `vscode-styled-components`，可以在模板字面量中编写 `CSS` 时实现代码高亮和代码提示

- 基本使用

  ~~~javascript
  import styled from 'styled-components'
  
  export const AppWrapper = styled.div`
    display: inline-block;
    .section {
      border: 1px solid red;
    }
  `
  ~~~

  - `AppWrapper` 是一个最终会被渲染为 `div` 元素的 `React` 组件（下面用 `styled` 组件指代），该组件的子元素都会应用模板字面量中定义的样式
  - `styled` 组件所生成的容器元素会被添加上一个不重复的类名，因此不会产生类名冲突
  - 模板字面量中的样式代码支持类似于 `CSS` 预处理器一样的样式嵌套

- 定义动态样式

  - 使用 `styled` 组件时，可以传递 `props`
  - 在定义 `styled` 组件的模板字面量中，使用 `${}` 插值语法可以传入 `JS` 表达式
  - 解析模板字面量时，如果插入的表达式是一个函数，那么该函数会接收 `props` 对象作为参数被调用，其返回值会作为插值嵌入模板字符串中

- 定义默认属性

  - 通过给 `styled` 组件添加 `attrs` 属性，以指定默认属性

  - 调用 `styled` 组件的 `attrs` 方法

    - 传入一个对象作为参数，该对象会作为 `attrs` 对象
    - 也可以传入一个函数，函数接收一个 `props` 对象作为参数，该函数的返回值会作为 `attrs` 对象
    - 返回一个新的 `styled` 组件，它会将传入的 `attrs` 对象合并到传递给模板字面量中插入的函数的 `props` 参数对象中

  - 当组件的 `props` 和 `attrs` 中有同名的键时，`attrs` 中的属性会覆盖掉 `props` 中的同名属性

  - `attrs` 中的键名

    - 如果 `attrs` 中的键名原本不存在于 `props` 中时，可能引发 `React` 报错或警告，可以使用 `$` 前缀的键名来规避
    - 而如果 `attrs` 中的键名在原本 `props` 中有重名时，访问 `props` 中的属性时可能出现死循环导致栈溢出

  - 当 `attrs` 中的属性是一个函数时

    - 它可以接收一个 `props` 对象作为参数
    - 当该属性传递给模板字面量时，会调用函数将其返回值传递

  - 定义默认属性示例（两种方式等同）

    ~~~javascript
    export const TestWrapper = styled.div.attrs(props => ({
      $color: props.color || 'blue'
    })
    )`
    .test{
      color:${props => props.$color}
      }
    `
    ~~~
    
    ~~~javascript
    export const TestWrapper = styled.div.attrs(
    {
      $color: props => props.color || 'blue'
    }
    )`
    .test{
      color:${props => props.$color}
      }
    `
    ~~~
  
- 样式继承

  - 示例

    ~~~javascript
    const ButtonWrapper = styled.button`
      padding: 8px 30px;
      border-radius: 5px;
    `
    
    const WarnButtonWrapper = styled(ButtonWrapper)`
      background-color: red;
      color: #fff;
    `
    
    // WarnButtonWrapper 的样式继承自 ButtonWrapper
    ~~~

- 设置主题

  - 示例

    ~~~jsx
    import { ThemeProvider } from 'styled-components'
    
    // ...
    // ThemeProvider 的 props 会提供给其子组件中的所有 styled 组件
    const el = (
      <ThemeProvider theme={{color: 'red', fontSize: '30px'}}>
        <App />
      </ThemeProvider>
    )
    // ...
    ~~~

    ~~~javascript
    import styled from 'styled-components'
    
    // SomeWrapper 被包裹在上面的 ThemeProvider 中，可以获取 ThemeProvider 的 props
    const SomeWrapper = styled.div`
      .section {
        color: ${props => props.theme.color};
        font-size: ${props => props.theme.fontSize}
      }
    `
    ~~~

## 过渡动画

### `react-transition-group`

- 可以使用 `React` 社区提供的库 `react-transition-group` 来实现过渡动画

- `react-transition-group` 主要包含四个组件

  - `Transition`
    - `Transition` 是一个不受平台限制的基础组件
    - 如果使用 `CSS` 实现过渡动画，则可以使用 `CSSTransition` 组件，它继承了 `Transition` 组件的所有特性，并且更好地适配 `CSS` 过渡动画
  - `CSSTransition`
    - 结合 `CSS` 实现过渡动画效果，在前端中常用
  - `SwitchTransition`
    - 两个组件显示和隐藏切换时，使用该组件
  - `TransitionGroup`
    - 将多个动画组件包裹其中，一半用于列表中元素的动画

#### `CSSTransition`

- `CSSTransition` 中只能由一个根元素

- `CSSTransition` 内的根元素会在需要进行过渡动画的不同阶段被添加上对应的类名，通过为这些类名设置 `CSS` 样式来实现动画效果，类名由 `classNames` 属性控制

- `CSSTransition` 组件常用属性

  - `in`

    - 该属性的值会转换成布尔值，用于触发进入或退出状态
    - 如果添加了 `unmountOnExit={true}`，那么该组件会在执行退出动画后被移除
    - 当 `in` 为 `true` 时，组件会触发进入状态，添加 `{name}-enter` 和 `{name}-enter-active` 类开始执行动画，动画执行完毕后，会移除这两个类，并添加 `{name}-enter-done` 类
    - 当 `in` 为 `false` 时，组件会触发退出状态，添加 `{name}-exit` 和 `{name}-exit-active` 类开始执行动画，动画执行完毕后，会移除这两个类，并添加 `{name}-exit-done` 类

  - `appear`

    - 默认为 `false`
    - 当 `appear` 为 `true` 时（同时也需要 `in` 为 `true`），组件会在初次挂载时执行触发过渡动画，添加 `{name}-appear` 和 `{name}-appear-active` 类开始执行动画，动画执行完毕后，会移除这两个类，并添加 `{name}-appear-done` 类 

  - `classNames`

    - 为 `CSSTransition` 组件添加一个基本类名

    - 注意它并不是 `React` 中的类名属性 `className`

    - 可以为该属性传入一个字符串或一个对象

    - 如果传入一个字符串，则该字符串会作用到不同阶段被添加的类名中

      - 以 `{name}-enter` 系列为例
        - `{name}-enter`
          - 在过渡开始时（即 `in` 属性从 `false` 变为 `true` 时）被添加到组件上，并在过渡完成时（默认是 `timeout` 毫秒后）被移除
          - 常用于定义过渡的初始状态
        - `{name}-enter-active`
          - 也是在过渡开始时被添加（即在 `{name}-enter` 被添加后），并在过渡完成时（默认是 `timeout` 毫秒后）被移除
          - 常用于定义过渡过程中的状态
        - `{name}-enter-done`
          - 在过渡完成后被添加，并在下一次进入过渡时被移除
          - 常用于定义过渡完成时的状态
          - 也可以不为该类指定 `CSS` 样式，那样就会使用组件默认样式作为过渡完成时的样式
      - `{name}-exit` 系列与 `{name}-appear` 系列同理
      - 注意
        - 由于 `{name}-{state}` 和 `{name}-{state}-active` 会在过渡阶段同时存在，需要注意 `CSS` 样式覆盖问题，`{name}-{state}-active`   样式需定义在 `{name}-{state}` 样式定义之后

    - 如果传入一个对象，则可以自定义 `CSSTransition` 不同阶段的类名

      ~~~jsx
      <CSSTransition
        classNames={{
          enter: 'my-enter',
          enterActive: 'my-enter-a',
          enterDone: 'my-enter-d',
          exit: 'my-exit',
          exitActive: 'my-exit-a',
          exitDone: 'my-exit-d',
          appear: 'my-appear',
          appearActive: 'my-appear-a',
          appearDone: 'my-appear-d',
        }}
        in={true}
        appear={true}
        timeout={1000}
      >
        ...
      </CSSTransition>
      ~~~

  - `timeout`

    - 过渡动画的时间，默认为 `0`

    - 可以接收一个 `number` 类型，单位为毫秒

    - 也可以接收一个对象，分别设置不同状态的过渡时长，如:

      `{ appear: 500, enter: 300, exit: 300 }`

  - `unmountOnExit`

    - 值为 `true` 时，组件会在执行退出动画后被移除

  - `nodeRef`

    - 对于 `CSSTransition` 组件内要执行动画的 `React` 元素（通常为根元素），将一个 `ref` 对象传入它的 `ref` 属性中，并将这个 `ref` 对象传入 `CSSTransition` 的 `nodeRef` 属性中
    - `CSSTransition` 组件可以通过这种方式来引用 `DOM` 节点
    - 而 `CSSTransition` 原本是通过 `findDOMNode` 方法来获取 `DOM` 节点的，这个方法在 `React` 中已不再推荐，因此可以通过 `nodeRef` 属性来替代

  - 钩子函数（事件监听）

    - `onEnter`
      - 进入动画之前被触发
    - `onEntering`
      - 进入动画时被触发
    - `onEntered`
      - 进入动画后触发
    - `onExit` / `onExiting` / `onExited`
    - 接受的参数
      - `node`
        - 接收执行动画的 `DOM` 节点
      - `isAppearing`
        - 接收一个布尔值
        - 只有 `Enter` 系列接能接收这个参数
        - 如果组件是在初始化挂载，则接收 `true`，否则接收 `false`
  
  - `key`
  
    - `CSSTransition` 的 `key` 属性用于设置一个根当前视图或组件有关的唯一值
    - 当 `key` 的值改变时，`React` 会认为这是一个全新的组件，使得 `CSSTransition` 触发入场和离场动画

#### `SwitchTransition`

- `SwitchTransition` 可以实现两个组件之间的切换动画
- `SwitchTransition` 包裹在 `CSSTransition` （或 `Trasition`，以下省略）外层，需要在 `CSSTransition` 中添加 `key` 属性，控制 `key` 属性的值以触发动画
- `SwitchTransition` 常见属性
  - `mode`
    - 指定动画进出场的方式，有两个可选值
      - `out-in`
        - 新元素等待旧元素完成 `transition` 后进入
        - 也就是说，旧元素首先进行退出动画，退出动画完成后，新元素进行进入动画
      - `in-out`
        - 新元素马上进入，然后等待旧元素完成 `transition`
        - 也就是说，新元素首先进行进入动画，然后旧元素进行退出动画

#### `TrasitionGroup`

- 当有一组动画时，需要将这些 `CSSTransition` 放入到一个 `TrasitionGroup` 中
- `TransitionGroup` 组件将所有子节点作为一组来处理
  - 子组件 `CSSTransition` 不需要通过 `in` 属性来管理状态，而是交给 `TransitionGroup` 管理
    - 当新的子组件添加到 `TransitionGroup` 中时，它会开始 `enter` 状态
    - 当子组件从 `TransitionGroup` 中移除时，它会开始 `exit` 状态
  - 每个子组件必须有一个唯一值的 `key` 属性
  - 常用属性
    - `component`
      - 指定使用什么元素来作为自己的外部元素进行渲染
      - 默认使用 `div` 元素
      - 如果传入 `null`，则只渲染子元素，不添加包围的元素
    - `appear`
      - 初始装载时是否需要执行动画
    - `enter`
      - 默认是 `true`
      - 意为当节点被添加时，会获得 `entering` 和 `entered` 状态
    - `exit`
      - 默认是 `true`
      - 意为当节点被移除时，会获得 `exiting` 和 `exited` 状态

###  `react-flip-move`

- `react-flip-move` 用于在 `React` 组件中添加美学的过渡动画

- 当列表项改变时(例如，新的列表项被添加，或者现有的列表项被删除或重新排序)，`react-flip-move` 会自动应用过渡动画

- 由于列表的改变造成的元素变化不是由样式引起的，所以无法只通过设置 `transition` 样式属性产生过渡动画，但可以借助 `FLIP` 实现

- `FLIP` 是一个动画技术的缩写，代表，`First`, `Last`, `Invert`, `Play`

  - `Fitst`
    - 在动画还没有开始的时候，获取元素的位置和尺寸
  - `Last`
    - 在元素的最终状态下获取元素的位置和尺寸
  -  `Invert`
    - 将元素从最终状态 `Invert` 到初始状态
    - 例如，如果一个元素的最终状态是向右移动了 `200` 像素，那么就使用 `transform` 样式属性将元素向左移动 `200` 像素，使之看起来与 `First` 阶段一致
  - `Play`
    - 移除 `Invert` 阶段添加的样式，使元素过渡到最终状态

- `FlipMove`

  - `react-flip-move` 提供了一个 `FlipMove` 组件

  - `FlipMove` 组件可以当作一个容器，用以包裹希望应用 `Flip` 动画的子元素

  - 常用属性

    - `typeName`

      - 默认情况下 `FlipMove` 会创建一个 `div` 元素包裹子元素
      - `typeName` 用于改变容器元素类型
      - 传入 `null` 可以不使用容器元素，但这种情况下，`typeName` 所在的父元素的样式不能有 `padding`，且 `position` 的类型不能是 `static`（只能是 `relative`，`absolute`，`fixed`，`sticky` 中的一种）

    - `easing`

      - 动画速度曲线
      - 值可以是任何有效的CSS3计时功能，如 `linear`，`ease-in`，`cubic-bezier(1,0,0,1)`
      - 默认值是 `ease-in-out`

    - `duration`

      - 动画完成所需时间
      - `number` 类型，单位是毫秒，默认为 `350`

    - `delay`

      - 动画开始前等待的时间
      - `number` 类型，单位是毫秒，默认为 `0`

    - `staggerDurationBy`

      - 例如有三个元素同时进行移动，`duration` 为 `200`，`staggerDurationBy` 为 `20`，那么：
        - 第一个元素的动画会持续 `200ms`
        - 第二个元素的动画会持续 `220ms`
        - 第三个元素的动画会持续 `240ms`
      - `number` 类型，单位是毫秒，默认为 `0`

    - `staggerDelayBy`

      - 例如有三个元素同时进行移动，`delay` 为 `200`，`staggerDelayBy` 为 `20`，那么：
        - 第一个元素会在 `200ms` 后开始动画
        - 第二个元素会在 `220ms` 后开始动画
        - 第三个元素会在 `240ms` 后开始动画
      - `number` 类型，单位是毫秒，默认为 `0`

    - `appearAnimation`

    - `enterAnimation`

      - 设置新的 `item` 刚加入 `DOM` 时的动画

      - 值

        - 传入字符串类型，可以直接使用预设的效果，可选值：

          - `elevator`（默认值）
          - `fade`
          - `accordionVertical`
          - `accordionHorizontal`
          - `none`

        - 传入布尔值类型

          - 传入 `false` 则禁用进入的动画
          - 传入 `true` 则使用默认动画 `elevator`

        - 传入对象类型，指定具体的样式

          - 对象中包含两个键：`from` 和 `to`

          - 例

            ~~~jsx
            const customEnterAnimation = {
              from: { transform: 'scale(0.5, 1)' },
              to:   { transform: 'scale(1, 1)' }
            }
            
            <FlipMove enterAnimation={customEnterAnimation}>
              {renderChildren()}
            </FlipMove>
            ~~~

    - `appearAnimation`

      - 设置组件刚挂载时 `item` 的初始动画
      - 使用方式同 `enterAnimation` 属性

    - `leaveAnimation`

      - 设置 `item` 离开 `DOM` 时的动画
      - 使用方式同 `enterAnimation` 属性

    - `maintainContainerHeight`

      - 接收布尔值
      - 如果值为 `true`，则在动画完成之前，保持原本的容器高度

    - `verticalAlignment`

      - 接收字符串
        - `top`（默认）
        - `bottom`
      - 告诉 `react-flip-move` 容器是顶部对齐还是底部对其的，以避免动画中产生的问题

    - 钩子函数

      - `onStart`
        - 动画开始时，每个子元素都会执行一次其中的函数
        - 回调函数接收两个参数
          - `childElement`
            - 该子元素对应的 `React` 元素
          - `domNode`
            - 该子元素对应的真实 `DOM` 元素
      - `onFinish`
        - 动画结束时，每个子元素都会执行一次其中的函数
        - 回调函数接收的参数同 `onStart`
      - `onStartAll`
        - 动画开始时，整个列表执行一次其中的函数
        - `childElements`
          - 所有子元素对应的 `React` 元素组成的数组
        - `domNode`
          - 所有子元素对应的真实 `DOM` 元素组成的数组
      - `onFinishAll`
        - 动画结束时，整个列表执行一次其中的函数
        - 回调函数接收的参数同 `onStartAll`

    - `disableAllAnimations`

      - 接收布尔值，如为 `true`，则禁用所有动画

    - `getPosition`

      - 接收一个函数
        - 该回调函数接收一个 `DOM` 节点作为参数
        - 需要返回值一个 `DOMRect` 对象，即使用 `Element.getBoundingClientRect()` 创建的对象
      - 当 `FlipMove` 所在的容器使用 `CSS` 进行缩放时， `react-flip-move` 将无法准确获取缩放后的位置信息，此时可以使用 `getPosition` 来修正位置信息

- 注意多次触发动画会造成卡顿，需使用防抖/节流

## `class` 管理

- `React` 可以在 `JSX` 中通过 `JS` 的方式灵活地管理 `class`，也可以使用第三方库管理 `class`，如 `classnames` 库

- `classnames` 的基本使用

  - `classnames` 库提供了一个 `classnames` 函数

  - `classnames` 函数可以接收任意数量的参数，参数类型可以是字符串或对象

  - `classnames` 函数将返回类名字符串

  - 如果一个参数是字符串，如 `'foo'`，那么它在 `classnames`  函数中的作用等同于一个对象 `{ foo: true }`

  - 对象参数中的属性值为布尔值，如为 `true`，则表示该键名会包含在返回的类名中，如为 `false` 则表示不会被包含

  - 如果参数是非值，则该参数会被无视

  - `classnames` 调用情况示例

    ~~~javascript
    import classNames from 'classnames'
    
    classNames('foo', 'bar') // => 'foo bar'
    classNames('foo', { bar: true }) // => 'foo bar'
    classNames({ 'foo-bar': true }) // => 'foo-bar'
    classNames({ 'foo-bar': false }) // => ''
    classNames({ foo: true }, { bar: true }) // => 'foo bar'
    classNames({ foo: true, bar: true }) // => 'foo bar'
    
    // lots of arguments of various types
    classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'
    
    // other falsy values are just ignored
    classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, '') // => 'bar 1'
    ~~~

## `Hooks`

- `Hook` 是 `React 16.8` 之后加入的特性，使得函数式组件可以使用 `state`、生命周期等其它的 `React` 特性
- 没有 `Hook` 时类式组件比起函数式组件的优势
  - 可以定义 `state` 以保存组件内部状态
  - 可以在对应的生命周期中完成逻辑
  - 组件更新时只重新执行 `render` 函数和对应的生命周期函数，而函数式组件需要重新执行整个函数
- 类式组件的缺陷
  - 相比较简介的函数组件，类式组件需要更多的引导代码（如构造函数和生命周期方法）
  - 事件处理函数需要手动绑定 `this`，或在直接绑定的箭头函数中调用，但会使得直接绑定的箭头函数在每次渲染时都会重新被创建
  - 编写逻辑复用时常需要用到高阶组件，使得组件树变得混乱
- `Hook` 可以弥补函数式组件对比类式组件的劣势，保留了函数式组件的简洁性
- `Hook` 本质上就是 `JS` 函数，可以用来钩入（`hook into`）`React` 的一些特性
- `React Hooks` 只能在 `React` 函数式组件的最外层作用域中使用，或在自定义 `Hook` 中的最外层作用域中使用

### `State Hook`

- `State Hook` 用以在函数式组件中实现 `state` 功能

#### `useState`

- `useState` 用于在函数式组件中创建或使用可被保留的状态
- 接收 `initialState` 作为唯一参数
  - 接收一个初始状态值，可以是任意类型
  - `initialState` 仅在第一次渲染时被用到，作为状态值被返回
    - 当状态改变使得组件重新渲染时，`useState` 会无视 `initialState` 而返回当前最新的 `state`
    - 因此该 `Hook` 的命名是 `useState` 而不是 `createState`
- 返回一个数组，包含两个元素
  - 第一个元素是当前状态值
  - 第二个元素是一个 `set` 函数，用于设置新的状态值
    - `set` 函数接收 `nextState` 作为唯一参数
      - 如果 `nextState` 接收一个非函数的值，则调用后 `nextState` 将会作为新的状态值更新
        - 当新值与旧值不同时，`React` 会重新渲染组件
        - 与类组件的 `this.setState` 不同，这里传入的状态值会完全替换掉旧的状态值
      - 如果 `nextState` 接收一个函数
        - 该函数将被视为更新函数，必须是纯函数
        - 该函数接收当前 `state` 作为唯一参数
        - 返回新值

#### `useReducer`

- `useReducer` 允许使用以 `reducer` 的方式维护 `state`

- 接收参数

  - `reducer`
    - 用于更新 `state` 的纯函数，类似于 `Redux` 中的 `reducer`
    - 接收 `state` 和 `action` 为参数，`state` 和 `action` 可以是任意值
    - 返回值是更新后的 `state`
  - `initialArg`
    - 用于初始化 `state` 的任意值
  - `init`（可选）
    - 用于计算初始值的函数
    - 如果定义了，则使用 `init(initialArg)` 的执行结果作为初始值，否则使用 `initialArg` 作为初始值

- 返回一个数组，包含两个元素，分别是：

  - 当前的 `state`

  - `dispatch` 方法

    - 传入 `action` 作为参数调用，用于更新 `state` 并触发组件的重新渲染


### `Effect Hook`

- `Effect Hook` 可以在函数式组件中实现类似于类式组件中的生命周期的功能
- 类似于网络请求、手动更新 `DOM`、一些事件的监听等，这些都是 `React` 更新 `DOM` 的一些副作用（`Side Effects`），对于完成这些功能的 `Hook` 被称之为 `Effect Hook`

#### `useEffect`

- 通过 `useEffect` 可以告诉 `React` 需要渲染后执行某些操作

- 接收参数

  - `setup`
    - 接收一个函数，用以处理 `Effect`
    - `setup` 函数可以返回一个清理（`cleanup`）函数
    - 执行时机
      - 当组件被添加到 `DOM` 中时，`React` 将执行 `setup` 函数
      - 当依赖项变更重新渲染后
        - `React` 先会使用旧的依赖项运行 `cleanup` 函数（如果提供了 `cleanup`）
          - `cleanup` 利用了闭包特性，能访问到重新渲染前的 `setup` 函数的上下文环境，因此可以使用旧的依赖项运行
        - 之后使用新的依赖项运行 `setup` 函数
      - 当组件从 `DOM` 中移除后，`React` 将执行 `cleanup` 函数
  - `dependencies`（可选）
    - 接收一个数组
    - 数组中的元素是 `setup` 函数的依赖项
    - 依赖列表必须具有确切数量的项，且必须像 `[dep1, dep2, dep3]` 这样内联编写（否则 `ESLint` 规则将无法准确推断是否正确使用了该 `Hook`）
    - 只要数组中的任何值发生变化，`setup` 函数就会再次执行
      - `React` 将使用 `Object.is` 来比较每个依赖项和它先前的值，如果出现了返回 `false` 的情况，就会执行 `setup` 函数
    - 如果传入一个空数组，那么 `setup` 函数会在每次组件重新渲染后都不会执行
    - 如果不传入该参数，那么 `setup` 函数会在每次组件重新渲染后都会执行

- 一个函数式组件中可以使用多个 `useEffect`，如果传入不同的 `dependencies`，它们的执行时机也会不同

- 使用示例

  - 基本使用

    ~~~jsx
    import React, { useEffect } from 'react'
    
    function ExampleComponent() {
      const [count, setCount] = React.useState(0)
    
      useEffect(() => {
        document.title = `You clicked ${count} times`
      }, [count])
    
      return (
        <div>
          <p>You clicked {count} times</p>
          <button onClick={() => setCount(count + 1)}>
            Click me
          </button>
        </div>
      )
    }
    ~~~

#### `useLayoutEffect`

- `useLayoutEffect` 和 `useEffect` 非常相似，区别是：
  - `useEffect` 会在渲染内容更新到 `DOM` 上之后执行（有时也会在更新之前执行），如果 `useEffect` 中执行的内容导致了页面重新渲染，可能会导致页面出现闪动。
  - `useLayoutEffect` 会在渲染内容更新到 `DOM` 上之前执行，会阻塞 `DOM` 更新。不会造成页面闪动，但是如果执行的任务过长会阻塞渲染。

### 性能 `Hook`

#### `useCallback`

- `useCallback` 允许在多次渲染中缓存函数
- 接收参数
  - `fn`
    - 接收想要缓存的函数
  - `dependencies`
    - 接收一个数组
    - 数组中的元素是有 `fn` 中的依赖项
    - 依赖列表必须具有确切数量的项，且必须像 `[dep1, dep2, dep3]` 这样编写
    - 同 `useEffect` 接收的 `dependencies` 参数
- 返回值
  - 组件初次渲染时，`useCallback` 返回传入的 `fn` 函数
  - 当组件重新渲染时
    - 如果 `dependencies` 中的依赖项没有发生改变，则返回之前缓存的 `fn` 函数
      - 注意，由于闭包机制，之前缓存的 `fn` 函数能捕获的外层作用域是之前函数式组件执行时创建的
      - 如果 `dependencies` 接收一个空数组，则永远返回初次渲染时传入的 `fn` 函数
    - 如果 `denpendencies` 中的依赖项发生改变，则返回最新传入的 `fn` 函数
- 性能优化
  - 利用 `useCallback` 进行的函数缓存本身不会对性能有优化，因为无论是使用新的 `fn` 还是之前缓存的 `fn`，新的 `fn` 都会被定义
  - `useCallback` 用于性能优化的场景主要有：
    - 如果在父组件中定义一个函数，将其作为 `prop` 传递给一个实现了避免非必要渲染的子组件（如继承自 `PureComponent` 的类式组件或使用 `memo` 高阶函数包装后的函数式组件），那么：
      - 如果该函数是直接定义的，那么每当父组件重新渲染，该函数都会被重新定义，子组件会认为 `prop` 发生了变化也重新渲染（无论是否需要重新渲染）
      - 如果该函数是使用 `useCallback` 定义的，那么当依赖项没有改变，而父组件重新渲染时，作为子组件 `props` 传递的该函数仍是之前定义的那个，不会因此使得子组件也必须重新渲染
    - 如果将函数式组件中定义的函数作为依赖项作用于 `useEffect`、`useMemo` 等 `Hook`，那么 `useCallback` 可以确保只有当依赖项改变时，这个函数的引用才会改变，从而避免不必要的副作用执行

#### `useMemo`

- `useMemo` 在每次渲染时能够缓存计算的结果
- 接收参数
  - `calculateValue`
    - 要缓存计算值的函数
    - 它应该是一个不接收任何参数的纯函数，且可以返回任意类型的值
  - `dependencies`
    - 接收一个数组，数组中包含依赖项
- 返回值
  - 初次渲染时，`useMemo` 返回 `calculateValue` 的调用结果
  - 重新渲染时，如果依赖项没有发生改变，则返回上次缓存的值，否则再次调用 `calculateValue` 返回最新结果
- 这种缓存返回值的方式也叫做**记忆化**（`memoization`）
- 性能优化的场景
  - `useMemo(() => fn, deps)` 在技术上等价于 `useCallback(fn, deps)`
  - 当组件中存在大量计算操作时，可在重新渲染时避免不必要的重复计算
  - 当父组件给子组件传递对象类型的数据作为 `prop` 时，重新渲染时若对象中的内容没有改变，使用 `useMemo` 可以避免该对象被重新创建而使得子组件被重新渲染

#### `useTransition`

- `useTransition` 用于启动一个过渡任务，用于告诉 `React` 某部分任务的更新优先级较低，可以稍后再更新

- `useTransition` 调用后返回一个数组，包含两个元素

  - `isPending`
    - 一个布尔值，表示是否存在待处理的 `transition`
    - 当被 `startTransition` 包装的状态更新还没完成时，它的值为 `true`，更新完成后，它的值将变成 `false`
  - `startTransition` 函数
    - 接收一个回调函数作为参数，回调函数中引发一些状态更新
    - `startTransition` 调用后，这个回调函数会被加入异步队列准备执行，回调执行期间引发的所有状态更新都会被 `React` 标记为 `transition`，它们是非阻塞的，`React` 会在不阻塞用户交互的情况下渲染它们

- 使用示例

  ~~~jsx
  import { useTransition } from 'react'
  
  function SomeComponent() {
    const [startTransition, isPending] = useTransition()
    
    const handleClick = () => {
      startTransition(() => {
        /* 这里可以引发一些可能会阻塞渲染的更新 */
      })
    }
    
    if(isPending) {
      return <div>Loading...</div>
    } 
  
    return (
      <button onClick={handleClick}>Whatever</button>
    )
  }
  ~~~

#### `useDeferredValue`

- `useDeferredValue` 用于延迟更新 `UI` 的某些部分
- 接收 `value` 作为唯一参数
  - `value` 是希望延迟渲染的值，可以是任何类型
  - 这个值会被 `useDeferredValue` 追踪，当这个值改变时，`React` 会尝试重新渲染组件
- 返回值
  - 返回一个延迟版的输入值的副本
  - 在组件初次渲染时，`useDeferredValue` 返回的就是传入的 `value` 的值的副本
  - 当 `value` 被改变，组件更新时，如果 `React` 有其它正在处理的更新，那么 `useDefferredValue` 仍会返回之前的副本，并启动一个重新渲染的任务（在该重新渲染中，`useDefferredValue` 将会返回最新值的副本），如果重新渲染任务发起前 `value` 被再次改变，则该任务会被取消，并重新发起一个重新渲染任务
  - 举例：如果用户在输入框中的输入速度比接收延迟值的图表重新渲染的速度快，那么图表只会在用户停止输入后重新渲染

### `Ref Hook`

#### `useRef`

- `useRef` 可以用于返回一个引用对象，主要特性有：
  - `useRef()` 在每次组件重新渲染时返回的都是同一个对象
  - 引用的数据如果被修改，不会触发重新渲染
  - 可以用来获取 `DOM` 对象
- 接收参数
  - `initialValue`
    - `ref` 对象的 `current` 属性的初始值
    - 可以是任意类型的值
    - 该参数仅在首次渲染时被使用
- 返回值
  - 返回一个只有一个 `current` 属性的 `ref` 对象
  - `current` 属性的值最初是 `initialValue` 接收的值，之后可以将其设置为其它值
  - 改变 `current` 属性不会触发组件更新
  - 如果将 `ref` 对象传递给一个 `React` 元素的 `ref` 属性，那么 `React` 会将其 `DOM` 对象设置为 `current` 属性的值，如果该 `React` 元素是一个函数式组件（需是由 `forwardRef` 高阶组件转换过的），则可以进行 `ref` 转发
  - 在之后的每重新渲染中，`useRef` 返回的始终是第一次返回的那个对象

#### `useImperativeHandle`

- `useImperativeHandle` 能在 `ref` 转发中在子组件中自定义由 `ref` 暴露出来的句柄
- 接收参数
  - `ref`
    - 父组件传递来的 `ref`
    - 也就是 `forwardRef` 包装后的子组件接收的第二个参数的值
  - `createHandle`
    - 接收一个没有参数的函数
    - 该函数返回一个对象，该对象中包含想要暴露的 `ref` 的句柄，通常会返回一个包含想要暴露的方法的对象
    - 可以理解为，父组件中定义的 `ref` 的 `current` 属性接收到的就是该函数返回的对象，也就是说可以在该函数中自定义返回想转发给父组件 `ref` 的内容
  - `dependencies`（可选）
    - 依赖项发生变化时，`createHandle` 函数会被重新执行并更新引用
- 该 `Hook` 用于转发内容给父组件的 `ref`，函数本身只会返回 `undefined`
- 在转发子组件的 `DOM` 时，可以只将 `DOM` 中特定的方法转发给父组件，比如想要暴露一个 `input` 的 `DOM` 节点，但不想父组件对它进行 `focus` 以外的操作，则可以只将 `focus` 方法暴露
- 如果是在 `tsx` 中使用 `useImperativeHandle`， 且转发的值的类型不是元素类型，那么需要注意将 `createHandle` 中返回的值的类型作为 `forwardRef` 的第一个泛型参数。
- 如果 `createHandle` 中也可以返回元素的引用，这样这个引用获取到的元素即可以在本组件中使用，也可以同时转发给父组件。

### 自定义 `Hook`

- 自定义 `Hook` 本质上是一种函数代码逻辑的抽取，严格意义上来说并不算 `React` 的特性

- 自定义 `Hook` 的命名必须以 `use` 开头，否则内部无法使用其它 `Hook`

- 自定义 `Hook` 示例

  - 获取滚动条位置

    ~~~javascript
    function useScrollPosition() {
      const [scrollPosition, setScrollPosition] = useState(0)
      
      useEffect(() => {
        const handleScroll = () => {
          setScrollPosition(window.scrollY)
        }
        document.addEventListener('scroll', handleScroll)
        return () => {
          document.removeEventListener('scroll', handleScroll)
        }
      }, [])
      
      return scrollPosition
    }
    ~~~

## `React` 脚手架

- `React` 的官方脚手架是 `create-react-app`，基于 `webpack`

- 使用 `create-react-app` 创建 `React` 项目

  ~~~bash
  npm i create-react-app -g
  create-react-app project_name
  ~~~

  - 项目名称不能包含大写字母

- 目录结构

  ~~~json
  ├─ README.md
  ├─ package.json
  ├─ public
  │  ├─ favicon.ico
  │  ├─ index.html
  │  ├─ logo192.png // 被在 manifest.json 中使用
  │  ├─ logo512.png // 被在 manifest.json 中使用
  │  ├─ manifest.json // 和 Web app 配置相关
  │  └─ robots.txt // 指定搜索引擎可以或者无法爬取哪些文件
  ├─ src
  │  ├─ App.css // App 样式
  │  ├─ App.js
  │  ├─ App.test.js
  │  ├─ index.css // 全局样式
  │  ├─ index.js
  │  ├─ logo.svg // create-react-app 默认启动页的 logo
  │  ├─ serviceworker.js // 默认的注册 PWA 相关的代码
  │  └─ setupTests.js // 测试初始化文件
  ├─ .gitignore
  └─ package-lock.json
  ~~~
  
- `PWA`

  - 全称 `Progressive Web App`，即渐进式 `Web` 应用
  - 一个 `PWA` 应用首先是一个网页，可以通过 `Web` 技术编写出一个网页应用
  - 随后添加上 `App Manifest` 和 `Service Worker` 来实现 `PWA` 的安装和离线等功能
  - 这种 `Web` 存在的形式，也称之为 `Web App`
  - `PWA` 可以让 `Web App` 做到
    - 添加至主屏幕，点击主屏幕图标可以实现启动动画以及隐藏地址栏
    - 离线缓存功能
    - 消息推送
    - 一系列类似 `Native App` 相关的功能
