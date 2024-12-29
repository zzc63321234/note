# DOM事件基础

## 1. 事件

- 事件

  > 事件是在编程时系统内发生的动作或者发生的事情
  >
  > 比如用户在网页上单击一个按钮

- 事件监听

  > 检测是否有事件发生，一旦有事件触发，就立即调用一个函数做出响应，也称“注册事件”或“绑定事件”

- 事件监听用法

  > 语法：
  >
  > > <u>EventTarget</u>.addEventListener('<u>EventType</u>', <u>function</u> )
  > >
  > > <u>事件目标</u>.addEventListener('<u>事件类型</u>'，<u>函数</u>)
  >
  > 用法：
  >
  > > <u>EventTarget</u>.addEventListener( )方法将指定的监听器注册到EventTarget（事件目标）上，当该对象触发指定的事件时，指定的<u>回调函数（见2.2 回调函数）</u>就会被执行
  > >
  > > 事件目标可以是从网页获取的DOM元素
  >
  > 例：
  >
  > ```javascript
  > // 获取DOM元素
  > let btn = document.querySelector('button')
  > // 事件监听
  > btn.addEventListener('click',function(){
  >     alert('被点击了')
  > })
  > ```

- 事件监听三要素

  1. EventTarget（事件目标）：监听哪个DOM元素触发的事件
  2. EventType（事件类型）：事件的类型，如鼠标点击（'click'）、鼠标经过（'mouseover'）等
  3. 事件调用的函数：被监听的事件触发后，由事件目标调用的函数

- 事件类型

  1. 鼠标事件——鼠标触发

     > click 鼠标点击
     >
     > mouseenter 鼠标经过
     >
     > mouseleave 鼠标离开

  2. 焦点事件——表单获得光标

     > focus 获得焦点
     >
     > blur 失去焦点

  3. 键盘事件——键盘触发

     > Keydown 键盘按下触发
     >
     > Keyup 键盘抬起触发

  4. 文本事件——表单输入触发

     > input 用户输入事件


## 2. 高阶函数

- 高阶函数可以被简单理解为函数的高级应用，JavaScript中函数可以被当成【值】来对待，基于这个特性实现函数的高级应用
- 【值】就是JavaScript中的数据，比如数值、字符串、布尔值、对象等

### 2.1 函数表达式

- 语法：

  > let <u>variables</u> = function (<u>params</u>) {<u>satements</u>}
  >
  > let <u>变量</u> = function(传参) {函数体}

  例：

  ~~~javascript
  let counter = function (x,y) {
      return x + y
  }
  let result = counter(5,10)
  ~~~

### 2.2 回调函数

- 如果函数A被作为实参被传入到外部的函数B中，并在函数B中被调用，则函数A被称为回调函数

- 例：

  ~~~javascript
  function fn() {
      console.log('我是回调函数')
  }
  // fn传递给了setInterval，fn是回调函数
  setInterval(fn,1000)
  ~~~

  ~~~javascript
  // 事件监听中的匿名函数参数是回调函数
  box.addEventListener('click',function () {
      console.log('我是回调函数')
  })
  ~~~

## 3. 环境变量

- 环境对象指的是函数内部的特殊变量**this**，它代表当前函数运行时所处的环境
- 函数的调用方式不同，**this**的指代对象也不同
- 【谁调用，**this**就是谁】是判断**this**指向的粗略规则
- 直接调用函数，其实相当于是window函数，所以**this**指代window
- 间歇函数调用的代码运行在与所在函数完全分离的执行环境上，因此间歇代码中的**this**会指向window，若要让间歇函数中的this指向正确的值，方法见[这里](https://blog.csdn.net/caseywei/article/details/100039884)