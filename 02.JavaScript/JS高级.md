## 执行上下文

- window和每一个函数都有自己的执行环境
  - window的执行环境叫做全局执行上下文环境，简称全局环境
  - 函数的执行环境叫做函数执行上下文

- 代码执行前，程序先执行，去创建能够让代码执行的环境（执行上下文）
  - 收集变量，生成变量对象（预解析）
  - 确定this指向
  - 形成自己的作用域链
- 执行环境准备完成后，压入栈内，开始执行代码
- 程序为了管理执行上下文（确保程序的执行顺序）所创建的一个栈数据结构，被称为执行上下文栈
- 预解析（变量提升）
  - 先解析函数：函数重名覆盖
  - 再解析变量：变量重名忽略
- 执行上下文过程
  1. 创建上下文阶段
     - 收集变量（函数提升、var声明的变量名提升，如果函数执行上下文，则还会收集形参），形成变量对象（预解析）
     - 确定this指向（确定执行者）
     - 创建自身执行上下文的作用域链
  2. 执行上下文压栈
  3. 执行上下文阶段
     - 为变量真正赋值
     - 顺着作用域链查找要使用的变量或者函数执行

## 终极原型链

- Object的显式原型对象portotype，是对象，因此是由Object创建的

  因此Object.prototype是Object自己的实例

  因此Object.prototype.\_\_proto\_\_，应该指向Object.prototype

  为了避免陷入死循环，JS将Object.prototype.\_\_proto\_\_设置为空对象null，作为原型链的终点

- Function构造函数是函数，因此是由Function自己创建出来的，因此它的隐式原型\_\_proto\_\_指向它自己的显式原型portotype

  即Function.\_\_proto\_\_ === Functioin.prototype

- 总结

  - prototype对象是构造函数独有的属性

  - 只要是实例，都有\_\_proto\_\_属性（null没有）

  -  Object是所有对象的构造函数，所以也是所有原型对象的构造函数，所有原型对象也是Object的实例对象
    - 所以JS将Object.prototype.\_\_proto\_\_设置为null，避免Object.prototype之上有无限的原型链

  - Function是所有函数的构造函数，所以也是构造函数的构造函数，所有构造函数也是Function的实例对象

    - Function.\_\_proto\_\_ === Functioin.prototype

    - Obect.\_\_proto\_\_ === Functioin.prototype

## 面向对象

- 面向对象语言三大特征：封装、继承、多态

- JS中的类

  - JS中目前不存在真正的类

  - 在es5中，可以将构造函数理解为类
  - 在es6中，出现了新的关键字class，但它也不是真正的类，底层依旧是通过封装构造函数来实现的，本质是一个语法糖

- 使用es5方式实现子类继承

  ~~~javascript
  // 创建一个Person构造函数
  function Person (name, age) {
      this.name = name
      this.age = age
  }
  Person.prototype.sleep = function () {
      console.log('I can sleep')
  }
  // 创建一个Teenager构造函数，作为Person的子类
  function Teenager (name, age) {
      Person.call(this, name, age) // 使用call()方法，执行Person()，并将Person中的this指向Teenager,使得继承父类的属性
  }
  Teenager.prototype = new Person() // 当Teenager的实例查找方法时，会沿着原型链查找Teenager.prototype，将其指向一个新建的Person的实例，相当于Teenager.prototype.__proto__ === Person.prototype，进而到Person.prototype中查找，实现原型的继承
  Teenager.prototype.constructor = Teenager // 因为Teenager.prototype被一个新建的实例对象覆盖，其constructor属性也被覆盖，需要使其重新指向Teenager
  let t1 = new Teenager('小明', 15)
  console.log(t1)
  console.log(t1.sleep())
  console.log(t1.__proto__.constructor)
  // 总结
  // - 子类要继承父类属性时，使用构造函数继承，将父类构造函数在子类构造函数中使用call()执行，并重新指向this
  // - 子类要继承父类方法时，使用原型继承，将子类的原型指向一个新建的父类的实例，并将子类原型中的constructor对象重新指向给子类
  ~~~

## 事件循环机制

- 进程与线程

  - 进程

    程序的一次执行，它占有一片独有的内存空间

  - 线程

    CPU的基本调度单位，是程序执行的一个完整流程

  - 一个进程中一般至少有一个运行的线程：主线程

  - 一个进程中可以同时运行多个线程

  - 一个进程内的数据可以供其中的多个线程直接共享

  - 多个进程之间的数据不能直接共享

  - 浏览器的运行都是多线程的，部分浏览器是但进程，部分浏览器是多进程

- JS是单线程的

  - 代码的分类
    - 初始化代码（同步代码）
    - 回调代码
  - JS引擎执行代码的基本流程
    - 先执行初始化代码，包含一些特别的代码
      - 设置定时器
      - 绑定监听
      - 发送ajax请求
    - 在某个时刻执行回调代码
      - 事件
      - 定时器
      - ajax
      - 生命周期回调函数

- 事件循环模型

  - 代码分类
    - 初始化执行代码（同步代码）
      - 设置定时器
      - 绑定监听
      - 发送ajax请求
    - 回调代码
      - 事件
      - 定时器
      - ajax
      - 生命周期回调函数
  - JS引擎执行代码的基本流程
    - 先执行初始化代码，包含一些特别的代码
    - 在之后的某个时刻执行回调代码
  - 模型的2个重要组成部分
    - 事件管理模块
    - 回调队列
  - 模型的运转流程
    - 执行初始化代码，将事件回调函数交给对应模块管理
    - 当事件发生时，事件管理模块会将回调函数及其数据添加到回调队列中
    - 只有到初始化代码执行完后，才会遍历读取回调队列中的回调函数执行

- Web Workers模拟多线程

## 闭包

- 闭包产生条件

  - 函数存在嵌套关系
  - 内部函数引用外部函数的局部变量
  - 外部函数被调用
  - 补充：对于chrome和部分浏览器，会对内部函数优化，如果内部函数不被调用或者引用，则不会产生闭包（实际使用都会引用内部函数，因此对于使用方式无影响）

- 闭包是什么（面试题）

  - 闭包是一个引用关系，该引用关系存在于内部函数中，引用的是外部函数的变量对象

- 常见的闭包

  - 将函数作为另一个函数的返回值
  - 将函数作为实参传递给另一个函数调用
  - 使用闭包实现私有方法操作独立的私有属性

- 闭包的作用

  - 延长外部函数变量对象的生命周期（本质是因为存在引用关系，使得外部函数的执行环境不能被销毁）
  - 让函数外部可以操作函数内部的数据

- 闭包的生命周期

  1. 当外部函数被调用时，闭包产生
     - 外部函数每当被调用一次，就会产生一个闭包
     - 不同的变量引用了内部函数，会产生独立的闭包
  2. 只要内部函数还被引用，闭包就一直存在
  3. 当内部函数不再被引用时，闭包就被销毁

- 闭包的缺点和结局

  - 内存泄漏：内存无法释放
  - 内存溢出：内存被撑爆
  - 解决方式：将对内部函数的引用清除

- 自定义模块

  ~~~javascript
  function myMoney() {
      let money = 15000
      function getMoney() {
          return money
      }
      function addMoney(a) {
          money += a
      }
      return{
          getMoney: getMoney,
          addMoney: addMoney
      }
  }
  const money = myMoney()
  money.addMoney(3000)
  console.log(money.getMoney())
  ~~~

  ~~~javascript
  (function () {
      let money = 15000
      window.getMoney = function () {
            return money
      }
      window.addMoney = function (a) {
          money += a
      }
  })();
  addMoney(3000)
  console.log(getMoney())
  ~~~
