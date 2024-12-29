# React

## 简介

#### React概述

- React是一个将数据渲染为HTML视图的开源JavaScript库
- 由Facebook开发

#### React特点

- 声明式编码
- 组件化编码
- 可借助React Native编写原生应用
- 高效的Diffing算法

#### React高效的原因

- 使用虚拟DOM，操作页面真实DOM的情况较少
- DOM Diffing算法，最小化页面重绘

#### 练习用相关JS库

- react.development.min.js
  - React的核心库，提供React对象
- react-dom.development.min.js
  - 操作DOM的react扩展库，提供ReactDom对象
- babel.min.js
  - 解析JSX语法代码转为JS代码的库
  - 将JSX代码写在html的script标签内，标签type属性为"text/babel"，以使用babel编译该标签内容
  - 经过babel编译之后的JS代码会开启JS严格模式

## 虚拟DOM

- React提供了一些API来创建虚拟DOM，其本质是一般JS对象

- 虚拟DOM对象最终都会被React转换为真实的DOM

- 创建虚拟DOM的两种方式

  1. 使用React提供的API

     React.createElement(标签名, 标签属性对象, 标签体)

  2. 使用JSX语法

- 渲染虚拟DOM

  - React 17 及之前的版本

    - ReactDOM.render(virtualDOM, containerDOM)

  - React 18版本

    - ReactDOM.createRoot(containerDOM).render(virtualDOM)

  - 作用：将虚拟DOM元素渲染到页面中的真实DOM容器中显示

  - 参数

    - virtualDOM

      虚拟DOM对象

    - containerDOM

      用来包含虚拟DOM元素的真实DOM元素对象，一般是一个div

## JSX语法

- 全称：JavaScript XML

- react定义的一种类似于XML的JS扩展语法

- 用来简化创建虚拟DOM

  - 示例：`const VDOM = <h1>Hello JSX</h1>>`

- 语法规则

  - 遇到<开头的代码，以标签的语法解析

    - 如果是html同名标签，则会转换为html同名元素，其它标签需要特别解析

    - 如果标签名是大写字母开头，则会被识别为组件
  
    - 最外层只能有一个根标签
    - 标签必须闭合，如果是单标签，则需要在标签名末尾补上斜杠
    - 标签中的'calss'需用'className'代替

  - 遇到{开头的代码，以JS语法解析，{}内的内容必须为一个JS表达式
  
    - 标签style属性中的内联样式需以JS对象的形式引入，注意属性名不能有横杠，需转换为小驼峰
    - 标签体中{}内的函数表达式的值不可以是Object、NaN
  - 需要换行时，为方便阅读，根标签外可以被()包围，但不可以被{}直接包围

## React面向组件编程

#### React开发者调试工具

- 浏览器chrome商店，安装React Developer Tools

#### 组件概念

- 组件用来实现局部功能效果的代码和资源的集合（html、css、JS、image等）
- 使用组件可以复用编码，简化项目编码，提高运行效率 
- 当应用以多组件的方式实现，则可称它为组件化的应用

#### 函数式组件与类式组件

##### 使用函数定义组件

~~~jsx
// 定义组件
function Test1() {
    console.log(this) // 由于babel编译的代码启用了严格模式，此处this的值为undefined
    return <h2>这是一个用函数定义的简单组件</h2>
}
// 渲染到页面
ReactDOM.createRoot(document.querySelector('#test1')).render(<Test1/>) // React 18版本
~~~

- React渲染<Test1/>标签时，会去寻找Test1组件定义的位置
- 当Test1是用函数定义时，React调用Test1并获取其返回的虚拟DOM

##### 使用类定义组件

~~~jsx
// 定义组件
class Test2 extends React.Component {
    render() {
        console.log(this) // 仅当通过React来使用Test2实例对象调用render时，this指向该实例对象，如果被window调用，则this为undefined
        return <h2>这是一个用类定义的复杂组件</h2>
    }
}
// 渲染到页面
ReactDOM.createRoot(document.querySelector('#test2')).render(<Test2/>)
~~~

- React渲染<Test2/>标签时，会去寻找Test2组件定义的位置
- 当Test2是React.Component的子类时，React会创建一个Test2的实例对象，通过该实例对象调用Test2原型上的render方法，获取其返回的虚拟DOM

#### 组件实例的三大属性

##### state

- 理解

  - state是组件对象最重要的属性，值为一个可以包含多个key-value组合的对象
  - 组件被称为“状态机”，通过更新组件的state来更新对应的页面显示（重新渲染组件）

- 使用示例

  - 当组件方法changeWeather被事件绑定触发时，this指向undefined，需将其指向组件的实例对象，使得在该方法中可以操作实例对象的state属性

  ~~~jsx
  // 使用bind()创建一个this绑定给组件实例对象的方法，将其赋值给组件实例对象的changeWeather
  class Weather extends React.Component {
      constructor(props) {
          super(props)
          this.state = {isHot: true} // 初始化状态
          this.changeWeather = this.changeWeather.bind(this)
          // - 构造器中的this指向组件实例对象
          // - 实例对象本身不存在changeWeather方法，所以该语句是为实例对象本身创建了一个changeWeather方法
          // - 赋值符号右边的this.changeWeather调用bind()时，由于此时实例对象还没有changeWeather方法，因此是让this.__protot__.changeWeather调用了bind()，将新创建的方法的this指向实例对象后，赋值给了this.changeWeather，因此组件实例获得了一个this指向自己的changeWeather方法
      }
      changeWeather() {
          this.setState({isHot: !this.state.isHot})
          // 组件实例的setSate方法中，可传入一个对象，对象中的属性会追加或覆盖到组件实例的state属性的对象中，随即会重新渲染一次组件
          // 每渲染一次组件，React就会创建一个新的组件实例，因此使用同一个类式组件得到的虚拟DOM之间是独立的
      }
      render() {
          return <h2 onClick={this.changeWeather}>今天南京{this.state.isHot ? '热' : '不太热'}</h2>
      }
  }
  ReactDOM.createRoot(document.querySelector('#weather')).render(<Weather/>)
  ~~~

  ~~~jsx
  // 使用箭头函数在构造器中定义组件原型的changeWeather方法
  class Weather extends React.Component {
      constructor(props) {
          super(props)
          this.state = {isHot: true} // 初始化状态
          this.changeWeather = () => {this.setState({isHot: !this.state.isHot})}
          // 箭头函数中的this，就是构造器里的this
      }
      render() {
          return <h2 onClick={this.changeWeather}>今天南京{this.state.isHot ? '热' : '不太热'}</h2>
      }
  }
  ReactDOM.createRoot(document.querySelector('#weather')).render(<Weather/>)
  ~~~

  ~~~jsx
  // 简写方式
  class Weather extends React.Component {
      state = {isHot: true}
      changeWeather = () => {this.setState({isHot: !this.state.isHot})}
      // state属性和changeWeather属性直接给组件实例对象自身
      // 箭头函数内的this和该层的this相同，指向组件实例对象
      render() {
          return <h2 onClick={this.changeWeather}>今天南京{this.state.isHot ? '热' : '不太热'}</h2>
      }
  }
  ReactDOM.createRoot(document.querySelector('#weather')).render(<Weather/>)
  ~~~
  
  - 使用setState()方法可以修改state中的内容，不建议通过setState()以外的方式修改state

##### props

- 理解

  - 每个组件实例对象都会有props(properties的简写)属性
  - 组件标签的所有属性都保存在porps中
    - 可以通过组件标签的属性，从组件外向组件内传递变化的数据

- 使用示例

  - 基本使用

  ~~~jsx
  // 两种不同的通过标签属性向组件传递数据的方式
  class Person extends React.Component{
      render(){
          const {name, age} = this.props
          return (
              <ul>
                  <li>姓名: {name}</li>
                  <li>年龄: {age}</li>
              </ul>
          )
      }
  }
  
  // 1. 直接将属性键值对传入标签中
  ReactDOM.createRoot(document.querySelector('#person1'))
      .render(<Person name="张三" age="30"/>)
  
  // 2. 将一个外部对象传入标签中
  const obj = {
      name: '小明',
      age: 18
  }
  
  ReactDOM.createRoot(document.querySelector('#person2'))
      .render(<Person {...obj}/>)
  // 在babel+React环境下，允许在JSX标签的{}内，使用展开运算符号展开一个对象，注意这和ES8语法中的克隆对象不是一回事
  ~~~

  - 限制props中的属性值类型，与设置默认属性值

  ~~~jsx
  // 如果要限制props中的属性值类型，在React 15.5版本以上需要引入prop-types.js
  class Person extends React.Component{
      
      // 在Person的静态属性protoTypes中设置props中的属性值限制类型
      static protoTypes = {
          name: PropTypes.string.isRequired, // 如果该属性必须要传入，则跟上.isRequired，否则属性值为undefined时也被允许
          age: PropTypes.number,
          address: PropTypes.string
      } // 如果传入的值的类型与配置不同，则会报错
      
      // 在Person的静态属性defaultProps中设置默认属性
      static defaultProps = {
          address: '中国'
          // 如果<Person/>标签中不传入address属性，则props中address默认属性值为'中国'
      }
      
       render(){
          const {name, age, address} = this.props
          return (
              <ul>
                  <li>姓名: {name}</li>
                  <li>年龄: {age}</li>
                  <li>地址: {address}</li>
              </ul>
          )
      }
  }
  
  ReactDOM.createRoot(document.querySelector('#person1'))
      .render(<Person name="张三" age="30"/>)
  ~~~

- 函数式组件中的props

  - 函数式组件在定义时接收的第一个参数，与类 式组件的props属性作用相同
  - 函数式组件中也可以限制props中的属性值类型，设置默认属性值，但无法像类式组件那样，使用static语法在定义组件的同时设置

##### refs

- 理解

  - 组件内的标签可以定义ref属性来标识自己

- 字符串形式的ref

  ~~~jsx
  <input ref="input" type="text"/>
  ~~~

  - 该标签节点会被存储到组件实例对象的refs属性的input属性中

- 回调形式的ref

  ~~~jsx
  <input ref={c => this.input = c} type="text">
  ~~~

  - {}内的表达式为一个箭头函数，传入的参数为当前节点currentNode
  - 该标签节点会被存储到组件实例对象的input属性中

- createRef创建ref容器

  ~~~jsx
  myRef = React.createRef()
  ~~~

  ~~~jsx
  <input ref={this.myRef} type="text">
  ~~~

  - 使用React.createRef()创建一个容器，赋值给组件实例对象的myRef属性
  - 该标签节点会被存储到组件实例对象的myRef.current中

#### React中的事件处理

- 通过onXxx属性指定事件处理函数
  - React使用的是自定义（合成）事件，不是使用原生的DOM事件 —— 为了更好的兼容性
  - React中的事件是通过事件委托的方式处理的（委托给组件的根元素） —— 为了提高效率
- onXXX属性绑定的回调函数的第一个参数为事件对象(event)，通过event.target可以得到发生事件的DOM元素对象
  - 由于ref使用过多会影响性能，在能使用事件对象获取DOM的时候尽量避免使用ref

#### 受控组件与函数柯里化

- 受控组件，是将组件中输入类的DOM（如input），随着用户的输入，将输入值维护到state中

  - 示例

    - 基本写法

      ~~~jsx
      // 需求：点击提交按钮后，将输入框的内容打印到控制台
      class Form extends React.Component {
          
          state = {} // 初始化state，否则组件实例没有state属性时调用state会报错
          
          handleSubmit = (e) => {
              e.preventDefault() // 阻止事件默认行为（跳转页面），React中return false无法阻止
              console.log(this.state.uname, this.state.pwd)
          } // 表单提交时触发
          
          saveUname = (e) => {
              this.setState({ uname: e.target.value }) // 将uname输入框的输入内容保存到state的uname属性
          } // 当uname输入框内容发生改变时触发
          
          savePwd = (e) => {
              this.setState({ pwd: e.target.value }) // 将pwd输入框的输入内容保存到state的pwd属性
          } // 当pwd输入框内容发生改变时触发
          
          render() {
              return (
                  <form onSubmit={this.handleSubmit}>
                      <input onChange={this.saveUname} type="text" placeholder="输入用户名" /><br />
                      <input onChange={this.savePwd} type="password" placeholder="输入密码" /><br />
                      <button>提交</button>
                  </form>
              )
          }
          
      }
      
      ReactDOM.createRoot(document.querySelector('#form')).render(<Form />)
      ~~~

    - 简化写法

      ~~~jsx
      class Form extends React.Component {
          
          state = {}
          
          handleSubmit = (e) => {
              e.preventDefault()
              console.log(this.state.uname, this.state.pwd)
          }
          
          saveFormData = (e) => {
              this.setState({ [e.target.name]: e.target.value }) // 将输入框的name属性和输入内容作为键值对保存到state
          } // 任何一个输入框内容发生改变时触发
          
          render() {
              return (
                  <form onSubmit={this.handleSubmit}>
                      <input onChange={this.saveFormData} name="uname" type="text" placeholder="输入用户名" /><br />
                      <input onChange={this.saveFormData} name="pwd" type="password" placeholder="输入密码" /><br />
                      <button>提交</button>
                  </form>
              )
          }
          
      }
      
      ReactDOM.createRoot(document.querySelector('#form')).render(<Form />)
      ~~~

- 高阶函数

  - 如果一个函数符合下面两个规范中的任何一个，那该函数就是高阶函数

  - 1. 该函数接收的参数是一个函数（回调函数是这种情况）
    2. 该函数被调用后的返回值是一个函数（函数柯里化就是使用了这种高阶函数）

  - 常见的高阶函数有：Promise、setTimeout、arr.forEach()

  - 函数柯里化

    - 通过函数调用继续返回函数的方式，实现多次接收参数，最后统一处理的函数编码形式

    - 简单示例：使用柯里化写法定义的求和函数

      ~~~javascript
      function sum(a){
          return (b) => {
              return (c) => {
                  return a + b + c
              }
          }
      }
      const result = sum(1)(2)(3)
      console.log(result)
      ~~~

    - 在定义受控组件时使用柯里化的函数

      ~~~jsx
      class Form extends React.Component {
          
          state = {}
          
          handleSubmit = (e) => {
              e.preventDefault()
              console.log(this.state.uname, this.state.pwd)
          }
          
          saveFormData = (type) => { // 将saveFormData柯里化的目是为了在绑定事件监听时定义一个type参数，将它传递给事件监听的回调函数
              return (e) => { // 返回的函数作为事件监听的回调函数，并可以使用type参数
                  this.setState({ [type]: e.target.value })
              }
          } // saveFormData在组件被渲染时就会被调用，并返回一个函数，作为事件监听的回调函数
          
          render() {
              return (
                  <form onSubmit={this.handleSubmit}>
                      <input onChange={this.saveFormData('uname')} type="text" placeholder="输入用户名" /><br />
                      <input onChange={this.saveFormData('pwd')} type="password" placeholder="输入密码" /><br />
                      <button>提交</button>
                  </form>
              )
          }
          
      }
      
      ReactDOM.createRoot(document.querySelector('#form')).render(<Form />)
      ~~~

#### 类式组件中的构造器

- 类式组件中的构造器完全可以省略
- 如果在类式组件中写了构造器，则必须在定义构造器时调用super
- 如果调用super时不传props，则在构造器中无法通过this.props访问props

#### 类式组件的生命周期

##### 理解

- 组件生命周期中存在一些特定阶段
- React组件中包含一系列钩子函数（hooks，生命周期回调函数），会在特定的时刻调用
- 定义组件时，可以在钩子函数中做特定的工作

##### 生命周期流程图（旧）

- React 16版本

![](.\images\react生命周期(旧).png)

##### 生命周期的三个阶段（旧）

1. 初始化阶段：由ReactDOM.render()触发，即组件的初次渲染

   - constructor()
   - <font color=red>componentWillMount() -- 官方已不推荐使用</font>
     - 可接收的第一个参数为props，第二个参数为state
   - render() -- 必需
- <font color=green>componentDidMount() -- 常用</font>
  - 生命周期内只会在组件挂载之后调用一次
  - 适用于对组件做一些初始化设置
2. 更新阶段：由组件内部this.setState()或父组件重新render触发

   - <font color=red>componentWillRecieveProps() -- 官方已不推荐使用</font>
     - 可接收的第一个参数为props，第二个参数为state
   - shouldComponentUpdate()
     - 可接收的第一个参数为props，第二个参数为state
     - 除强制更新之外，如果shouldComponentUpdate返回值为true，则可以更新，如果返回值为false，则阻止更新
   - <font color=red>componentWillUpdate() -- 官方已不推荐使用</font>
     - 可接收的第一个参数为props，第二个参数为state
   - render() -- 必需
   - <font color=green>componentDidUpdate() -- 常用</font>
     - 可接收的第一个参数为props，第二个参数为preState（此次更新前的state）
3. 卸载组件：由ReactDOM.unmountComponentAtNode()触发

   - <font color=green>componentWillUnmount() -- 常用</font>

##### 生命周期示例（旧）

~~~jsx
// 父组件
class Behavior extends React.Component {

    constructor() {
        console.log('--- constructor ---')
        super()
        this.state = { merits: 0 }
    } // 组件将要被挂载前，首先调用constructor

    loseMerits = () => {
        this.setState({ merits: this.state.merits - 1 })
    } // 更新state的回调函数

    gainMerits = () => {
        this.setState({ merits: this.state.merits + 1 })
    } // 更新state的回调函数

    unmount = () => {
        ReactDOM.unmountComponentAtNode(document.querySelector('#test'))
    } // 卸载组件的回调函数

    force = () => {
        this.forceUpdate() // 强制更新容器
    } // 强制更新的回调函数

    componentWillMount() {
        console.log('--- componentWillMount ---')
    } // 组件将要被挂载前，且consructor被调用后，调用componentWillMount

    componentDidMount() { // 常用
        console.log('--- componenDidlMount ---')
    } // 组件被挂载后调用componentDidMount

    componentWillUnmount() { // 常用
        console.log('--- componenWillUnmount ---')
    } // 组件被卸载前调用componentWillUnmount

    shouldComponentUpdate() {
        console.log('--- shouldComponentUpdate ---')
        return true
    } // state更新后，先调用shouldComponentUpdate，如果返回值为true，则调用render更新容器，如果返回值为false，则不会容器不会更新

    componentWillUpdate() {
        console.log('--- componentWillUpdate ---')
    } // 将要更新容器前，调用componentWillUpdate

    componentDidUpdate() {
        console.log('--- componentDidUpdate ---')
    } // 更新容器完成后，调用componentDidUpdate

    render() {
        console.log('--- render ---')
        return (
            <div>
                <Record {...this.state} />
                <button onClick={this.loseMerits}>看到了一个地狱笑话，笑出了声</button>                   <br /><br />
                <button onClick={this.gainMerits}>这是一个电子木鱼</button>
                <br /><br />
                <button onClick={this.unmount}>丢掉功德簿（卸载组件）</button>
                <br /><br />
                <button onClick={this.force}>刷新功德簿（强制更新容器）</button>
                <br /><br />
            </div>
        )
    } // 组件被挂载时，以及之后每次调用setState()后调用render

}

// 子组件
class Record extends React.Component {

    componentWillReceiveProps() {
        console.log('--- componentWillReceiveProps ---')
    } // 当子组件的props被更新后，即父组件除挂载以外调用render后，子组件调用componentWillReceiveProps

    render() {
        return <h2>功德：{this.props.merits}</h2>
    }
}

ReactDOM.render(<Behavior />, document.querySelector('#merits_record'))
~~~

##### 生命周期流程图（新）

- React 17及之后版本

![](.\images\react生命周期(新).png)

##### 生命周期的三个阶段（新）

1. 初始化阶段：由ReactDOM.render()触发，即组件的初次渲染

   - constructor()
   - <font color=blue>getDerivedStateFromProps() -- 新增</font>
     - 需使用static定义
     - 可接收的第一个参数为props，第二个参数为state
     - 需return一个对象作为state的更新，并且将更新的属性锁住，如返回值为null则不会更新
     - 目前在开发中使用场景极为罕见
   - render() -- 必需
   - <font color=green>componentDidMount() -- 常用</font>
     - 生命周期内只会在组件挂载之后调用一次
     - 适用于对组件做一些初始化设置
2. 更新阶段：由组件内部this.setState()或父组件重新render触发

   - <font color=blue>getDerivedStateFromProps() -- 新增</font>
     - 可接收的第一个参数为props，第二个参数为state
     - ……
   - shouldComponentUpdate()
     - 可接收的第一个参数为props，第二个参数为state
     - 除强制更新之外，如果shouldComponentUpdate返回值为true，则可以更新，如果返回值为false，则阻止更新
   - render() -- 必需
   - <font color=blue>getSnapshotBeforeUpdate() -- 新增</font>
     - 可接收的第一个参数为props，第二个参数为prevState（此次更新前的state）
     - 需return一个值作为componentDidUpdate的第三个参数
     - getSnapshotBeforeUpdate的调用在render之后，在React更新真实DOM之前，可以在这个钩子中将更新前的DOM状态保存快照，传递给DOM更新后的componentDidUpdate
   - <font color=green>componentDidUpdate() -- 常用</font>
     - 可接收的第一个参数为props，第二个参数为prevState（此次更新前的state），第三个参数为snapshot（getSnapshotBeforeUpdate()的返回值）
3. 卸载组件：由ReactDOM.unmountComponentAtNode()触发

   - <font color=green>componentWillUnmount() -- 常用</font>
     - 补充：React 18版本之后，卸载组件时需将渲染组件的容器存储到变量（如：root）中，再使用`root.unmount()`卸载容器中的组件

#### Diffing算法

##### key

- 组件标签中key属性的作用
  - 简单理解：key是虚拟DOM对象的标识，在更新显示时key起着极其重要的作用
  - 当state中的数据发生变化时，React会根据【新数据】生成【新虚拟DOM】，随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较，规则如下
    1. 【旧虚拟DOM】中找到了与【新虚拟DOM】相同的key
       - 如果虚拟DOM中内容没变，直接使用之前的真实DOM
       - 如果虚拟DOM中的内容变了，则生成新的真实DOM，替换页面中旧的真实DOM
    2. 【旧虚拟DOM】中未找到与【新虚拟DOM】相同的key
       - 根据数据创建新的真实DOM，渲染到页面中
- 用index作为key可能会引发的问题
  - 若对数据进行逆序添加、删除等破坏顺序的操作，则会改变数据的index，使得虚拟DOM的key发生改变
    - 会产生没有必要的真实DOM的更新，降低渲染效率
    - 如果结构中包含输入类的DOM，则会使DOM错误更新，引发bug
- 开发中如何指定key
  - 使用每条数据的唯一标识作为key
  - 如果仅用于简单的展示数据，则也可以使用index

## React应用（基于React脚手架）

#### React脚手架

##### 简介

- React脚手架是用来帮助程序员快速创建一个基于React库的模板项目
  - 包含了一些需要的配置（语法检查、JSX编译、devServer等）
  - 下载好了所有的相关依赖
  - 可以直接运行一个简单效果
- React提供了一个用于创建React项目的脚手架库：create-react-app
- 项目的整体技术架构为：React + Webpack + ES6 + eslint
- 使用脚手架开发项目的特点：模块化，组件化，工程化

##### 使用create-react-app创建React应用

1. 全局安装  `yarn global add create-react-app`
2. 在想要创建项目的目录下使用命令 `create-react-app 项目名称`
3. 进入项目目录，使用命令 `yarn start` 启动项目

##### React脚手架项目结构

> - public --- 静态资源目录
>     - favicon.icon --- 网站页签图标
>     - <font color="red">index.html --- 主页面</font>
>     - logo192.png --- logo图
>     - logo512.png --- logo图
>     - manifest.json --- 应用加壳的配置文件
>     - robots.txt --- 爬虫协议文件
>     
> - src --- 源码目录
>     
>     - components --- 组件目录
>       - pages --- 路由组件目录
>     
>     - App.css --- App组件的样式
>     - <font color="red">App.js --- App组件</font>
>     - App.jest.js --- 用于给App做测试
>     - index.css --- 样式
>     - <font color="red">index.js --- 入口文件</font>
>     - logo.svg --- logo矢量图
>     - reportWebVitals --- 页面性能分析文件，需要web-vitals库的支持
>     - setupTests.js --- 组件单元测试的文件，需要jest-dom库的支持

- 脚手架中，引入模块时，js和jsx后缀可以省略

#### 注意事项

- React脚手架搭建的项目中，没有将window作为全局代码运行环境，而是将一些window自带方法定义给了React全局运行环境，但confirm没有被定义，如要在React中调用，需写成window.confirm()

#### 消息订阅与发布

- 工具库：PubSubJS

- 安装

  ~~~shell
  yarn add pubsub-js
  ~~~

- 引入

  ~~~javascript
  import PubSub from 'pubsub-js'
  ~~~

- 使用

  ~~~javascript
  const token = PubSub.subscribe('my topic', (msg, data) => {
      console.log(msg, data)
  }) // 当有名为'my topic'的订阅发布时，接收该订阅，执行回调函数，回调函数中msg为订阅的名称，此处为'my topic'，data为订阅的数据，如果不需要用到msg，可以用 _ 来占位
  
  PubSub.publish('my topic', 'hello world') // 发布名称为'my topic'的订阅，数据为'hello world'
  
  PubSub.unsubscribe(token) // 取消token的订阅
  
  PubSub.clearAllSubscriptions() // 清除所有订阅
  
  // 取消指定订阅
  PubSub.subscribe('a', myFunc1)
  PubSub.subscribe('a.b', myFunc2)
  PubSub.subscribe('a.b.c', myFunc3)
  PubSub.unsubscribe('a.b') // 取消'a.b'以及'a.b.c'的订阅，'a'的订阅未被取消
  ~~~

## React路由

### 概念

#### SPA(singel page web application)

- 即单页Web应用
- 整个应用只有一个完整的页面
- 点击页面中的链接不会刷新页面，只会做页面的局部更新 
- 数据都需要通过ajax请求获取，并在前端异步展现

#### 路由的理解

##### 什么是路由

- 一个路由就是一个映射关系(key: value)
- key为路径，value可能是function或component

#### 路由分类

##### 后端路由

- 理解：value是function，用来处理客户端提交的请求
- 注册路由：router.get(path, function(req, res))
- 工作过程：当node接收到一个请求时，根据请求路径找到匹配的路由，调用路由中的函数来处理请求，返回响应数据

##### 前端路由

- 理解：value时component，用于展示页面内容
- 注册路由：<Route path="./test" component={Test}>
- 工作过程：当浏览器的path变为/test时，当前路由组件会变成Test组件

### react-router-dom

#### 简介

- React的一个插件库
- 专门用来实现一个SPA应用

#### 相关API

##### 内置组件

###### BrowserRouter & HashRouter

- 路由管理需要在BrowserRouter或HashRouter内才能进行，建议包裹最外侧的APP组件
- BrowserBRouter修改URL本身
- HashRouter是纯前端路由。只修改URL中的哈希值，URL中的哈希值不会发送到后端服务器

###### Link

- 路由链接，用于路径切换

- 用法类似原生html中的<a></a>标签，可以使用css设置样式

- 示例

  ~~~jsx
  <Link to="/about">About</Link> // 切换到about路径
  <Link to="/home" replace={true}>Home</Link> // 切换到home路径
  ~~~

- 可选属性：
  - replace
    - 默认为false，此时路由跳转模式为push模式，会将要跳转的url压入路由history的栈顶
    - 值为true时，路由跳转模式为replace模式，会将要跳转的url替换路由history栈顶的url
    - 当值为true时也可以省略属性值，只写replace

###### NavLink

- 导航路由链接，是Link的上层封装，可是实现路由链接的高亮

- 如果一个NavLink上的路径被应用，则该NavLink会自动被添加一个active类名

- 可通过设置属性activeClassName指定路径被应用时的类名

- 示例

  ~~~jsx
  <NavLink activeClassName="on" to="/about">About</Link> // 切换到about路径
  <NavLink activeClassName="on" to="/home">Home</Link> // 切换到home路径
  ~~~

- 可选属性：
  - replace

###### Route

- 用于根据路径显示对应的组件

- 示例

  ~~~jsx
  // 旧版
  <Route path="/about" component={About} /> // 当路径为about时，显示About组件
  <Route path="/home" component={Home} /> // 当路径为home时，显示Home组件
      
  // v6版本
  <Routes>
      <Route path="/about" element={<About />} />
      <Route path="/homne" exact={true} element={<Home />} />
  </Routes>
  ~~~

- 属性
  - element
    - 放要渲染的路由组件或Navigate组件
  - exact（v5及之前版本）
    - 默认为false，模糊匹配，地址栏url的路径只要从根目录开始按顺序完全包含path属性的路径就能匹配成功
    - 设置为true时为精准匹配，地址栏url的路径必须与path属性的路径完全相同时才能匹配成功

###### Switch（v5及之前版本）

- 使用switch包裹Route组件标签后，switch中的一个路径只会匹配到唯一的Route
- 在v6版本中被Routes替代

###### Routes（v6版本）

- Route组件必须被Routes标签包裹才能使用
- 替代Switch

###### Redirect（v5及之前版本）

- 一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由

- 示例

  ~~~jsx
  <Redirect to="/home" /> // 当所有路由都无法匹配时，切换到home路径
  ~~~

###### Navigate（v6版本）

- 替代Redirect

- 示例

  ~~~jsx
   <Route path="*" element={<Navigate to="home" replace={true}/>} />
  ~~~

- 可选属性

  - replace

###### withRouter（v5及之前版本）

- 调用withRouter(非路由组件)，可将非路由组件转换为路由组件返回

##### Hook(v6版本)

###### useParams

###### useSearchParams

###### useLocation

##### 路由组件props中的对象（v5及之前版本）

###### history对象

> go: f go(n)
>
> goBack: f goBack()
>
> goForward: f goForward()
>
> push: f push(path, state)
>
> replace: f replace(path, state)

###### location对象

> pathname: '/home'
>
> search: ""
>
> state: null

###### match对象

> isExact: true
>
> params: {}
>
> path: "/home"
>
> url: "/home"

#### 模糊匹配与精确匹配

- 在v5及之前版本中，默认为模糊匹配，使用精确匹配需在Route标签中设置exact属性为true
- 在v6版本中，默认为精确匹配，使用模糊匹配需在Route标签中的路径后加上"/*"通配符

#### 嵌套路由注意点

- 嵌套路由中，父组件路由的路径需要使用模糊匹配，否则当前路由匹配子组件时，父组件无法渲染

- 在v5及之前版本中，子组件路由的路径中需写上父组件的路径作为前缀

  在v6版本中，子组件路由的路径默认会带上父组件的路径作为前缀，无需自己写

#### 解决public目录下的资源路径出错问题

- 出错原因：使用了多级路径，导致请求public资源时，如果使用的是相对路径，会沿用多级路径

- 三种方法
  1. 引入public资源时使用绝对路径，即路径前不加'.'
  2. 引入public资源时，在路径前加'%PUBLIC_URL%'，效果为，在项目路径下的public目录下按照该路径寻找资源，仅适用于React脚手架
  3. 使用HashRouter，使得多级路径不发送到后端服务器，不会影响到public资源的请求

#### 路由中携带参数

##### params参数

- 路径中携带params参数
- 在Route组件中的path属性中定义params参数
- 在路由组件内访问params参数

  - v5及之前版本
    - params参数在this.props.match.params中
  - v6版本
    - 从react-router-dom中获取useParams
    - 调用useParams函数，得到一个对象，params参数包含在该对象中

##### search参数

- 路径中携带查询字符串

- 不需要再Route中定义

- 在路由组件内访问search参数

  - v5及之前版本

    - search参数在this.props.location.search中

  - v6版本

    - 从react-router-dom中获取useSearchParams

    - 使用示例

      ~~~jsx
      // 当前路径为 /foo?id=12
      // Foo是一个函数式组件
      function Foo() {
        const [searchParams, setSearchParams] = useSearchParams()
        console.log(searchParams.get("id")) // 12
        setSearchParams({
          name: "foo",
        }) // 改写search参数为：/foo?name=foo
        return <div>foo</div>;
      }
      ~~~

##### state参数

- 传递state参数

  - v5及之前版本

    ~~~jsx
    <Link to={{pathname:'/test', state:{a:1, b:2}}}>foo</Link>
    ~~~

  - v6版本

    ~~~jsx
    <Link to="/test" state={{a:1, b:2}}>foo</Link>
    ~~~

    

- 不需要再Route中定义

- 在路由组件内访问state参数

  - v5及之前版本

    - state参数在this.props.location.state中

  - v6版本

    - 从react-router-dom中获取useLocation

    - 使用示例

      ~~~jsx
      function Foo() {
        const {state:{a, b}} = useLocation()
        return <div>foo</div>
      }
      ~~~

- state参数不会出现在url中

#### 编程式路由导航

- 不同于使用Link或NavLink组件进行路由跳转，通过JS代码编写路由跳转的命令就是编程式路由导航

- v5及之前版本

  - 通过路由组件的porps中的history对象中提供的方法实现编程式路由导航

    - this.props.history.push(path, state)

      push方式跳转到指定路径

    - this.props.history.replace(path, state)

      replace方式跳转到指定路径

    - this.props.history.goBack()

      在历史记录中后退一步

    - this.props.history.goForward()

      在历史记录中前进一步

    - this.props.history.go(n)

      在历史记录中跳转n步，n为正数时前进，n为负数时后退

- v6版本

  - 使用useNavigate钩子函数生成的navigate函数实现编程式路由导航

  - 示例

    ~~~jsx
    function Foo(){
        const navigate = useNavigate();
        return (
            // 当前路径： /a/b，history中上一步的路径: /a
            <div onClick={() => navigate('/b')}>跳转到/b</div>
            <div onClick={() => navigate('a11')}>跳转到/a/b/a11</div>
            <div onClick={() => navigate('../a2')}>跳转到/a/a2</div>
            <div onClick={() => navigate(-1)}>跳转到/a</div>
            <div onClick={() => navigate('/login', {replace, state:{a:1}})}>replace方式跳转到/login，并传入state参数</div>
        )
    }
    ~~~



## React中的Hook

### 简介

- Hook是React 18.8.0版本中增加的新特性/新语法
- Hook可以在函数组件中使用state以及其它的React特性
- React设计React Hooks的原因：
  - 尽量使用纯函数组件，如果需要外部功能和副作用时，就用钩子把外部代码钩进来
- 常见的几个Hook
  - State Hook: React.useState()
  - Effect Hook: React.useEffect()
  - Ref Hook: React.useRef()

### State Hook

- State Hook让函数组件也可以有state状态，并进行状态数据的读写操作
- 语法：const [xxx, setXxx] = React.useState(initValue)
  - initValue是第一次初始化指定的值，在内部作缓存，如果已存在缓存，则不会生效（使得状态值更新，组件重新渲染时再次执行该代码，状态值不会被再次初始化）
  - xxx属性保存状态值
  - setXxx即设置状态值的方法，有两种写法
    - setXxx(newStateValue)：参数为非函数值，直接指定新的状态值
      - 该写法在setInterval的回调中会失效
    - setXxx(stateValue => newStateValue)：参数为函数，该函数可以接收原本的状态值，并在函数体中返回新的状态值
      - 推荐使用这种写法
- 如一个组件需要多个状态，需多次调用useState()

### Effect Hook 

- Effect Hook可以在函数组件中执行副作用操作（模拟类式组件只能够的生命周期钩子）

- React中的副作用操作：

  - 发送ajax请求
  - 设置订阅/启动定时器
  - 手动更改真实DOM

- 语法和说明：

  ~~~javascript
  React.useEffect(() => {
      // 可以在此执行带副作用的操作
      return () => {
          // 在组件卸载前之前，做一些收尾工作，如清除定时器、取消订阅等
      }
  }, [stateValue])
  ~~~

  - Effect Hook可以模拟类式组件中的三种生命周期钩子
    - 调用时传入两个参数，第一个是一个函数，第二个是一个数组（可选）
    - 第一个参数可以在组件挂载以及监听stateValue发送变化时执行函数体内容，可以模拟类式组件中的componentDidMount或componentDidUpdate
      - 当第二个参数为空数组时，即不监听任何stateValue，相当于componentDidMount
      - 当第二个参数数组内包含stateValue时，则监听对应的状态值，发生变化时执行第一个参数的函数体内容
      - 当不传入第二个参数时，则监听所有的状态值，相当于componentDidUpdate
    - 当第一个参数返回一个函数时，返回的函数相当于componentWillUnmount

### Ref Hook

- React.useRef()与类式组件中使用的React.createRef()在功能和用法基本上是一样的

- [useRef 与 createRef 的区别](https://zhuanlan.zhihu.com/p/110247813)



## redux

### 简介

#### rudux是什么

- `redux`是一个专门用于做状态管理的JS库（并不是`react`插件库）
- `redux`可以用在`react`,`angular`,`vue`等项目中，但基本是与`react`配合使用
- `redux`可以集中式管理react应用中多个组件共享的状态

#### 什么情况下需要使用redux

- 某个组件的状态，需要让其它组件可以随时拿到（共享）
- 一个组件需要改变另一个组件的状态（通信）

#### redux工作流程

![](./images/redux原理图.png)

### 三个核心概念

#### Action

- 动作的对象
- 包含2个属性
  1. `type`
     - 标识属性，值为字符串，唯一，必要属性
  2. `data`
     - 数据属性，值可以是任意类型，可选属性

#### Reducer

- `reducer`是函数

- 用于初始化状态、加工状态
- 加工时，根据旧的`state`和`action`，产生新的`state`的纯函数

#### Store

- 将`state`、`action`、`reducer`联系在一起的对象

- 得到`store`的方式

  - `createStore(reducer,applyMiddleware(中间件))`
    - 参数
      - `reducer`
        - `reducer`函数，或使用`combineReducers`创建的`reducer`集合对象
      - `applyMiddleware()`
        - 配置要使用的中间件

- `store`的功能

  - `store.getState()`

    得到`state`

  - `store.dispatch(action)`

    分发`action`，给`reducer`调用

  - `store.subscribe(callback)`

    注册监听，当`reducer`产生了新的`state`时，自动调用，一般用于重新渲染App组件

### redux核心API

#### createstore(reducer)

- 从`redux`库中引入
- 作用：创建包含指定`reducer`的`store`对象

#### store对象

- `rudex`最核心的管理对象
- 由`createstore()`创建
- 内部维护者
  - `state`
  - `reducer`
- 核心方法
  - `store.getState()`
    - 从`store`中获取`state`
  - `store.dispatch(action)`
    - 将`action`分发给`reducer`
  - `store.subscribe(callback)`
    - 当`store`中的`state`发生变化时，调用`callback`

#### applyMiddleware(middleware)

- 作用：应用基于`redux`的中间件
- 用法：作为`createStore()`的第二个参数使用

#### combineReducers()

- 作用：合并多个`reducer`函数，得到一个对象
- 用法：`combineReducers({key1: reducer1, key2: reducer2})`

### redux异步编程

- `redux`默认是不能进行异步处理的
- 某些时候应用中需要执行异步任务（`ajax`，`定时器`），如果异步操作不想在组件中进行，可交给`action`
- `redux`可以借助异步中间件进行异步处理
  - 安装`redux-thunk`
  - 在`store`中配置
    - 从`redux`中引入`applyMiddleware`
    - 从`redux-thunk`中引入`thunk`
    - `export default createStore(reducer,apllyMiddleware(thunk))`

### redux求和案例

- src

  - redux

    - constant.js

      ~~~javascript
      // 常量模块，配置着所有action中的type值
      
      export const INCREASE = 'increase'
      export const DECREASE = 'decrease'
      ~~~

    - store.js

      ~~~javascript
      // 用来创建store对象
      
      import { createStore, applyMiddleware } from 'redux'
      import countReducer from './count_reducer'
      import thunk from 'redux-thunk'
      
      export default createStore(countReducer, applyMiddleware(thunk))
      ~~~

    - count_reducer.js

      ~~~javascript
      // 存放reducer函数
      
      import { INCREASE, DECREASE } from './constant'
      // reducer函数的返回值就是对应组件的redux状态
      export default function countReducer(preState = 0, action) {
         const { type, data } = action
         switch (type) {
            case INCREASE:
               return preState + data
            case DECREASE:
               return preState - data
            default: // 当reducer初始化时会被调用一次，此时没有type，会触发default，此时返回preState的默认值即可
               return preState
         }
      }
      ~~~
  
    - count_action
  
      ~~~javascript
      // count_action.js用于创建Count组件相关的action
      
      // 从constant.js中引入type的值
      import { INCREASE, DECREASE } from './constant'
      
      // 封装不同的创建action的函数，并将返回的action其暴露
      
      // 暴露同步action
      // 创建同步action的函数指定action的type，data由Count组件传入
      export const createIncreaseAction = data => ({ type: INCREASE, data })
      export const createDecreaseAction = data => ({ type: DECREASE, data })
      
      // 暴露异步action
      export const createIncreaseAsyncAction = (data) => {
         // 返回的回调函数被调用时，dispatch方法作为传参被传入，无需另外引入
         return (dispatch) => {
            setTimeout(() => {
               //创建异步action的函数内部，往往会调用同步action，并接收Count组件传入的data
               dispatch(createIncreaseAction(data))
            }, 500)
         }
      }
      ~~~
  
  - components
  
    - count
  
      - index.jsx
  
        ~~~javascript
        import React, { Component } from 'react'
        import store from '../../redux/store'
        import { createIncreaseAction, createDecreaseAction, createIncreaseAsyncAction } from '../../redux/count_action'
        // import './index.css'
        export default class Count extends Component {
        
           apply = (e) => {
              // 获取选框中的值
              const value = +this.selectNum.value
              // 根据按下的不同按钮，让store创建不同的action并分发给reducer函数
              switch (e.target.innerText) {
                 case '+':
                    store.dispatch(createIncreaseAction(value))
                    break
                 case '-':
                    store.dispatch(createDecreaseAction(value))
                    break
                 case 'increase if odd':
                    if (store.getState() % 2 === 0) break
                    store.dispatch(createIncreaseAction(value))
                    break
                    case 'increase async':
                    store.dispatch(createIncreaseAsyncAction(value))
                    break
                 default:
                    break
              }
           }
        
           render() {
              return (
                 <div>
                    <h3>count: {store.getState()}</h3><br />
                    <select ref={c => { this.selectNum = c }}>
                       <option>1</option>
                       <option>2</option>
                       <option>3</option>
                       <option>4</option>
                       <option>5</option>
                       <option>6</option>
                       <option>7</option>
                       <option>8</option>
                       <option>9</option>
                    </select>&nbsp;
                    <button onClick={this.apply} >+</button>&nbsp;
                    <button onClick={this.apply}>-</button>&nbsp;
                    <button onClick={this.apply}>increase if odd</button>&nbsp;
                    <button onClick={this.apply}>increase async</button>&nbsp;
                 </div>
              )
           }
        
        }
        ~~~
  
  - App.js
  
  - index.js
  
    > 在index.js中需要使用store.subscribe()来监听store中state的更新，并重新渲染App组件
    >
    > store.subscribe(() => {
    >    <App />
    > })

### redux-react

#### 简介

- 一个react插件库
- 专门用来简化react应用中使用redux

#### redux-react中的组件类型

![](./images/react-redux模型图.png)

##### UI组件

- 只负责UI的呈现，不带有任何业务逻辑
- 通过props接收数据（一般数据和函数）
- 不使用redux的API
- 一般保存在compoonents文件夹下

##### 容器组件

- 负责管理数据和业务逻辑，不负责UI的呈现
- 使用redux的API
- 一般保存在container文件夹下，也可以和UI组件写在同一个文件中

#### 相关API

- Provider

  - Provider组件可包裹在App组件外，将store作为props传入，使得App中的所有组件都可以得到state数据
  - Provider还可以监听store中state的更新，并重新渲染组件（代替store.subscribe()的功能）

  ~~~jsx
  // 在index.js中引入
  import {Provider} form 'react-redux'
  ~~~

  ~~~jsx
  <Provider store={store}></Provider>
  ~~~

- connect

  - connect函数用于包装UI组件生成容器组件

  ~~~jsx
  // 在要创建容器组件的文件中引入
  import {connect} from 'react-redux'
  ~~~

  ~~~jsx
  // 伪代码
  connect(
      mapStateToProps, // 映射状态
      mapDispatchToProps // 映射操作状态的方法
  )(UI组件)
  ~~~

- mapStateToProps

  - mapStateToProps将redux中的state对象转换为UI组件的标签属性

  ~~~jsx
  const mapStateToprops = state => ({key: state})
  ~~~

- mapDispatchToProps

  - mapDispatchToProps将分发action的函数转换为UI组件的标签属性

  ~~~jsx
  const mapDispatchToProps = dispatch => {
      return {
          increase: data => dispatch(createIncreaseAction(data)) // UI组件调用this.porps.increase()时传入data的实参
      }
  }
  
  ~~~

#### 用react-redux改写求和案例

- index.js

  - 从react-redux中引入Provider
  - 使用Provider组件标签包裹App组件标签
  - Provider标签属性中传入store

  ~~~jsx
  import React from 'react'
  import ReactDOM from 'react-dom/client'
  import App from './App'
  import store from './redux/store'
  import { Provider } from 'react-redux'
  
  const root = ReactDOM.createRoot(document.getElementById('root'))
  root.render(
     <Provider store={store}>
        <App />
     </Provider>
  )
  ~~~

- Count组件

  - 从react-redux中引入connect
  - 原本的Count组件作为Count的UI组件，不对外暴露
  - 使用connect函数包装UI组件生成容器组件，向外暴露
  - 在mapDispatchToProps函数中创建分发action的函数，使它们被传入UI组件的props
  - 在UI组件中通过this.props调用对应的分发action的函数

  ~~~jsx
  import React, { Component } from 'react'
  import store from '../../redux/store'
  import { createIncreaseAction, createDecreaseAction, createIncreaseAsyncAction } from '../../redux/count_action'
  import { connect } from 'react-redux'
  // import './index.css'
  
  class Count extends Component {
  
     apply = (e) => {
        // 获取选框中的值
        const value = +this.selectNum.value
        // 获取分发action的函数
        const { increase, decrease, increaseAsync } = this.props
        // 根据不同的按钮，让store创建不同的action并分发给reducer函数
        switch (e.target.innerText) {
           case '+':
              increase(value)
              break
           case '-':
              decrease(value)
              break
           case 'increase if odd':
              if (this.props.count % 2 === 0) break
              increase(value)
              break
           case 'increase async':
              increaseAsync(value)
              break
           default:
              break
        }
     }
  
     render() {
        return (
           <div>
              <h3>count: {this.props.count}</h3><br />
              <select ref={c => { this.selectNum = c }}>
                 <option>1</option>
                 <option>2</option>
                 <option>3</option>
                 <option>4</option>
                 <option>5</option>
                 <option>6</option>
                 <option>7</option>
                 <option>8</option>
                 <option>9</option>
              </select>&nbsp;
              <button onClick={this.apply} >+</button>&nbsp;
              <button onClick={this.apply}>-</button>&nbsp;
              <button onClick={this.apply}>increase if odd</button>&nbsp;
              <button onClick={this.apply}>increase async</button>&nbsp;
           </div>
        )
     }
  
  }
  
  const mapStateToProps = state => ({ count: state })
  
  // mapDispatchToProps的详细写法
  /* const mapDispatchToProps = dispatch => (
     {
        increase: data => store.dispatch(createIncreaseAction(data)),
        decrease: data => store.dispatch(cr eateDecreaseAction(data)),
        increaseAsync: data => store.dispatch(createIncreaseAsyncAction(data))
     }
  ) */
  
  // mapDispatchToProps的精简写法
  const mapDispatchToProps = {
     increase: createIncreaseAction,
     decrease: createDecreaseAction,
     increaseAsync: createIncreaseAsyncAction
  } // 当increase被调用，并被传递参数时，count_action中封装的createIncreaseAction函数就会接收该参数并被调用，得到返回的action对象，connect()的封装使得当mapDispatchToProps为一般对象时，可以直接实现对象中action的分发
  
  export default connect(
     mapStateToProps,
     mapDispatchToProps
  )(Count)
  ~~~


### 组件间共享状态

- store只需要一个文件，每个需要共享状态的组件需要一个对应的reducer和action

- 合并各个需要共享状态的组件的reducer

  - 在store.js中，从redux中引入combineReducers

  - ~~~javascript
    const reducers = combineReducers( // 调用combineReducers，传入reducer集合对象
       {
          count: countReducer,
          person: personReducer
       }
    )
    export default createStore(reducers, applyMiddleware(thunk)) // 将combineReducers调用的结果传入createStore()
    ~~~

- 在各个组件中接收redux中的state（比如count的state）

  - 在原生redux中，使用store.getState().count获取
  - 在react-redux中，在UI组件内使用this.props.count获取

### 使用redux调试工具

- 安装chrome浏览器插件Redux DevTools

- 项目安装依赖包redux-devtools-extension

- 在store.js中引入

  ~~~javascript
  import {composeWithDevTools} from 'redux-devtools-extension'
  ~~~

  - 使用方法：在createStore()的第二个参数中传入composeWithDevTools()，如果使用了redux中间件，则往composeWithDevTools()中传入applyMiddeleware(中间件)

- 配置完成后可在控制台使用Redux DevTools插件调试redux

### 纯函数概念

- 一类特别的函数: 只要是同样的输入(实参)，必定得到同样的输出(返回)
- 必须遵守以下一些约束
  - 不得在函数体内改写实参数据（数组不能使用push、pop等方法）
  - 不会产生任何副作用，例如网络请求，输入和输出设备（可能会造成err的情况）
  - 不能调用Date.now()或者Math.random()等不纯的方法
- redux的reducer函数必须时一个纯函数
