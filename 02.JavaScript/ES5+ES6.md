# ES5

## 严格模式(strict mode)

- ES5运行除了正常模式（又称混杂模式），还有第二种运行模式
- 严格模式，就是使JavaScript在更严格的语法条件下运行
- 作用
  - 消除JavaScript语法的一些不合理、不严谨之处，减少一些怪异行为
  - 消除代码运行的一些不安全之处，保证代码安全运行（保护数据）
  - 为未来新版本的JavaScript做铺垫
- 严格模式的使用
  - 在全局或函数的第一条语句定义为：'use strict'
- 语法和行为改变
  - 不允许使用未声明的变量
  - 禁止自定义的函数中的this指向window，将指向undefined
  - 创建eval作用域
    - eval()是一个函数，接收字符串参数，会对字符串进行JS语法解析并运行
  - 对象不能有重复的同名属性
  - 函数不允许有同名形参
  - 新增一些保留字，如：imolements interface private protected public

## Object扩展

- Object.create(prototype, descriptors)

  - 该方法可以以指定对象的原型，创建新的对象返回，同时可以对新的对象设置属性，并对属性值进行描述

  - ~~~javascript
    Object.create(原型对象, {
        属性名:{
            value: 属性值,
            writable: 布尔值, // 属性值是否可以修改，默认为false
            configurable: 布尔值, // 属性是否可以被删除，默认为false
            enumerable: 布尔值, // 属性是否能被for in遍历，默认为false
        },
        属性名2:{
            get: function(){
                函数体
                return 属性值
            }, // 当获取当前属性时，get函数（又称getter）作为回调函数被自动触发，函数返回值可作为该属性的属性值，可用于设置动态属性
            set: function(value){
                函数体
            } // 当为当前属性赋值时，set函数（又称setter）作为回调函数被自动触发，可设置形参来接收赋值的内容
            // get与set中的this指代使用Object.create()方法新建的这个对象
            // get、set属性与value属性不能同时设置
            // set可与get结合使用，利用set中传入的value，给get中的变量赋值，以实现修改属性值的作用
    })
    ~~~

- Object.defineProperties(object, descriptors)

  - 该方法可以直接在一个对象上定义新的属性或修改现有属性
  - Object.defineProperties(要修改的对象, 属性描述对象)
  - 属性描述的方式与Object.create()方法相同
  - get与set中的this指代要修改的对象

- Object.defineProperty(object, prop, descriptors)

  - 用于定义或修改对象的单个属性
  - prop参数为指定的属性名
  - descriptors参数为属性描述对象，且不用在该对象内指定属性名，直接配置单个描述对象即可
  - 用法同Object.defineProperties

- Object.getOwnPropertyDescriptors(对象)
  - 查看对象的属性描述
  
- Object.keys(对象)

  - 返回一个由一个给定对象的自身可枚举属性组成的【数组】，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致

## 指定函数的this的方法

- call()
  - 使用call()方法可以调用函数
  - call()的第一个参数可以指定函数的this
  - 如需传递函数本身的参数，可将其跟在call()的第一个参数后面
- apply()
  - apply()和call()作用相同
  - apply()的实参需要通过数组来传递

```javascript
let obj = {a:1}
function fn (a, b) {
    console.log(this,a, b)
}

fn.call(obj, 1, 2) // {a:1} 1 2
fn.apply(obj, [1, 2]) // {a:1} 1 2
```

- bind()

  - 当一个函数调用bind()方法时，该方法会返回一个新的函数对象
  - 返回的函数对象功能和原来的函数一样，不同点在于新函数的this绑定了bind()方法的第一个参数

  ```javascript
  let obj = {a:1}
  function fn (a, b) {
      console.log(this,a, b)
  }
  let fn2 = fn.bind(obj, 1, 2)
  fn2() // {a:1} 1 2
  ```

# ES6

## let关键字与块级作用域

- 用来声明变量

  - 不允许重复声明
  - 块级作用域
  - 不存在变量提升

- 块级作用域案例

  ~~~javascript
  // 使用块级作用域时
  for (let i = 0; i<3; i++){
      setTimeout(function(){
          console.log('let:'+i)
      },1000)
  }// 1s后输出结果为'let:0'，'let:1'，'let:2'
  // 使用let定义的i存在于for循环的块级作用域内，打印i时会从块级作用域内寻找i的值
  
  // 使用全局作用域时
  for (var i = 0; i<3; i++){
      setTimeout(function(){
          console.log('var:'+i)
      },1000)
  }// 1s后输出结果为三次‘var:3’
  // 使用var定义的i存在于全局作用域内，打印i时会从全局作用域寻找i的值，此时全局作用域下i变量已被改变
  
  // ES5模拟块级作用域
  for (var i = 0; i<3; i++){
      ;(function(i){
      setTimeout(function(){
          console.log('模拟:'+i)
      },1000)
      })(i);
  }// 1s后输出结果为'模拟:0'，'模拟:1'，'模拟:2'
  // 使用var定义的i存在于全局作用域内，将遍历过程中每一次i的值作为参数传入一个立即执行函数中，在该函数中打印的参数存在于函数作用域中，不会受全局作用域中i的变化的影响
  ~~~

## const关键字

- 用来声明常量
  - 声明一定要赋初始值
  - 不允许重复声明
  - 不允许修改值
    - 如果是复杂数据类型，则不允许修改指向的地址，数据的内容可以修改
    - 可以使用const声明复杂数据类型，以防止指向地址被误赋值
  - 块级作用域
  - 名称一般使用大写（潜规则）

## 变量的解构赋值

### 数组解构

- 数组解构时将数组的单元值快速批量赋值给一系列变量的简洁语法

- 基本写法

  ```javascript
  const [a, b, c] = [100, 60, 80]
  consolr.log(a) // 100
  consolr.log(b) // 60
  consolr.log(b) // 80
  ```

- 数组解构可以使用剩余参数

- 应用：变量值交换

  ```javascript
  let a = 1
  let b = 2
  ;[a, b] = [b, a] // 当数组在一行开头时，会被认为与上一行代码在同一行，因此需与之前代码之间加分号
  console.log(a) // 2
  console.log(b) // 1
  ```

- 设置默认值

  ```javascript
  const  [a=1, b=2, c=3] = [4, 5]
  console.log(a) // 4
  console.log(b) // 5
  console.log(c) // 3
  // 若没有给c设置默认值，则c的值会变成undefined
  ```

- 按需导入赋值

  ```javascript
  const [a, , c] = [1, 2, 3]
  console.log(a) // 1
  console.log(c) // 3
  ```

- 多维数组

  ```javascript
  const arr = [1, 2, [3, 4]]
  console.log(arr[0]) // 1
  console.log(arr[1]) // 2
  console.log(arr[2]) // [3, 4]
  console.log(arr[2][0]) // 3
  console.log(arr[2][1]) // 4
  ```

- 多维数组解构

  ```javascript
  const [a, b, [c, d]] = [1, 2, [3, 4]]
  console.log(a) // 1
  console.log(b) // 2
  console.log(c) // 3
  console.log(d) // 4
  ```

### 对象解构

- 对象解构是将对象属性和方法快速批量赋值给一系列变量的简洁语法

- 基本写法

  ```javascript
  const {a, b} = {a: 1, b: 2} // 相当于 const a = obj.a, b = obj.b
  ```

  - 对象属性的值被赋值给与属性名相同的变量
  - 注意解构的变量名不要和当前作用域内的变量名冲突
  - 解构的变量名在对象中找不到对应属性名时，值为undefined

- 对象解构的变量名重新改名

  ```javascript
  const a = 1 //当要解构的对象的属性名在定义域内有同名变量时，可以改变对象解构的变量名
  const {a: A, b} = {a: 1, b:2}
  console.log(A) // 1
  ```

- 数组对象的解构

  ```javascript
  const obj = [
  {
        uname: '小明'，
        age: 18
   }
  ]
  const [{uname, age}] = obj
  console.log(uname) // 小明
  console.log(age) // 18
  
  ```

- 多级对象解构

  ```javascript
  const pig = {
      name: '佩奇',
      family: {
          mother: '猪妈妈',
          father: '猪爸爸',
          sister: '乔治'
      },
      age: 6
  }
  const{name, family: {mother, father, sister}, age} = pig
  ```

  ```javascript
  const msg = {
      "code": 200
      "msg": "获取新闻列表成功"
      "data": [
      {
          "id": 1,
          "title": "新闻标题1"
          "count": 58
      },
      {
          "id": 2,
          "title": "新闻标题2"
          "count": 56
      }
      ]
  }
  
  // 打印msg对象中的data对象
  function render({data}) {
      consolr.log(data)
  }
  render(msg)
  // 送入msg对象到函数中，利用函数的形参做对象解构
  
  //以下是简化前的写法
  const [{data}] = arr // msg传给arr，实现对象解构
  function render(arr) {
      consolr.log(arr)
  }
  render(msg)
  ```

## 模板字面量（`Template literals`）

- `ECMAScript 6` 新增了使用模板字面量定义字符串的能力

- 模板字面量在一对反引号 `` ` 中定义字符串

  - 该字符串中可以出现换行符
  - 可以使用 `${expression}` 的形式传入 `JS` 表达式进行字符串插值

- 标签函数（`tag function`）与带标签的模板

  - **带标签的模板**是**模板字面量**的一种更高级的形式，允许使用标签函数解析模板字面量

  - 标签函数本身是一个常规函数，通过前缀到模板字面量来应用自定义行为

  - 示例

    ~~~javascript
    const person = "Mike"
    const age = 28
    
    function myTag(strings, personExp, ageExp) {
      const str0 = strings[0]; // "That "
      const str1 = strings[1]; // " is a "
      const str2 = strings[2]; // "."
    
      const ageStr = ageExp > 99 ? "centenarian" : "youngster"
    
      // 可以返回使用模板字面量构建的字符串
      // 也可以返回其它任何内容作为带标签的模板解析后的值
      return `${str0}${personExp}${str1}${ageStr}${str2}`;
    }
    
    const output = myTag`That ${person} is a ${age}.`;
    
    console.log(output) // That Mike is a youngster.
    ~~~

- 原始字符串

  - 使用 `String.raw` 作为标签函数可以使得模板字面量可以直接获取原始的内容（如换行符或 `Unicode` 字符），而不是被转换后的字符（但如果使用了换行或 `Unicode` 字符本身，则不会被转换为转义序列，主打一个所见即所得）
  - 另外，也可以通过标签函数的第一个参数，即字符串数组的 `raw` 属性取得每个字符串的原始内容

## 简化对象写法

- ES6允许在打括号里直接写入变量和函数，作为对象的属性和方法

  ~~~javascript
  let a = 1
  let b = 2
  function c(){
      console.log(3)
  }
  const obj = {
      a,
      b,
      c,
      d(){
          console.log(4)
      }
  }
  ~~~

## 箭头函数

- 更简短的函数写法

- 适用于那些本来需要匿名函数的地方

- 基本写法

  ```javascript
  const fn = (形参) => {函数体}
  ```

- 简写情况

  - 不写小括号：当形参有且只有一个时
  - 不写大括号：当代码体只有一条语句时，且语句的执行结果会作为函数返回值

  ```javascript
  // 当只有一个形参时，可以省略小括号（没有形参时可以写空小括号）
  const fn = x => {
      console.log(x)
  }
  fn(1)
  ```

  ```javascript
  // 当只有一行函数体时，可以省略大括号
  const fn = x => console.log(x)
  fn(1)
  ```

  ```javascript
  // 当只有一行函数体时，无需写return可以直接返回值
  const fn = x => x + x // 相当于 return x + x
  ```

  ```javascript
  // 作为匿名函数使用
  const form = document.querySelector('form')
  form.addEventListener('click', e => e.preventDefault())
  ```

  ```javascript
  // 直接返回一个对象
  const fn = (name) => ({uname: name}) // 大括号表示这是一个对象，为避免大括号与函数体大括号混淆，在外包裹一个小括号
  console.log(fn('小明')) 
  ```

- 箭头函数没有arguments动态参数，但是有剩余参数

- 箭头函数不创建this，会从自己作用域链的上一层沿用this，且无法被call()等方法改变this

  ```javascript
  const obj = {
      traceThis: () => console(this) // 此处的this沿用了obj的this
  } 
  obj.traceThis() // this指向obj的调用者window
  ```

  ```javascript
  function fn() {
      console.log(this)
  }
  let fn3 = () => console.log(this);
  
  let obj = {
      fn: fn,
      fn2: function () {
          console.log(this);
  
          function fn3() {
              console.log(this)
          }
  
          let fn4 = () => console.log(this)
  
          fn3()
          fn4()
      }
  }
  obj.fn() // this指向obj
  obj.fn2() // fn2中的this指向obj，fn3中的this指向window,fn4中的this沿用了fn2的this指向了obj
  // 虽然fn3是在由obj调用的fn2方法中被定义和调用，但实际上时fn2方法中的代码让window调用了fn3，因此fn3的this指向window
  ```


  - 在DOM事件回调函数中若需要使用this，不建议使用箭头函数
  - 有时可以灵活利用箭头函数的this指向
  - 可用that、self、_this等变量名来保存当前this的指向

## 参数

### 参数默认值

- 声明函数时若为形参赋值，该值即为参数的默认值

- 参数未自定义默认值时，默认值为undefined

- 调用函数时没有传入对应实参时，参数默认值作为实参传入函数

  ~~~javascript
    // 设置参数默认值
    function sayHi(name="小明", age=18) {
      document.write(`<p>大家好，我叫${name}，我今年${age}岁了。</p>`);
    }
    // 调用函数
    sayHi();
    sayHi('小红');
    sayHi('小刚', 21);
  ~~~

- 结合解构赋值使用

  ~~~javascript
  function fn({ a = 4, b, c, d = 5 }) {
      console.log(a, b, c, d) // 结果为1, 2, 3, 5
  }
  fn({
      a: 1,
      b: 2,
      c: 3
  })
  ~~~

### 剩余参数与展开运算符

- 用以接收剩余未被接收的参数
- 剩余参数必须是最后一个形参
- 剩余参数接收的实参形成一个真数组

```javascript
function getSum(a, b, ...others) {
    console.log(others) // 打印结果为[3, 4]
}
getSum(1, 2, 3, 4)
```

- 展开运算符(spread)

  - 展开运算符(...)可以展开实现了iterator接口的数据

    ~~~javascript
    // 1.求数组最大值
    const arr1 = [1, 2, 3] // ...arr等价于1, 2, 3
    console.log(Math.max(...arr1)) // 打印结果为3
    
    // 2.合并数组
    const arr2 = [4, 5, 6]
    const arr3 = [...arr1,...arr2] // arr3 === [1, 2, 3, 4, 5, 6]
    
    // 3.合并对象
    const obj1 = {
        a: 1,
        b: 2
    }
    const obj2 = {
        c: 3
    }
    const obj3 = {...obj1, ...obj2} // 如果出现同名属性，则后覆盖前
    
    // 将伪数组转换为真数组 
    const divs = [...document.querySelectorAll('div')]
    ~~~

  - 结合数组解构

    ~~~javascript
    ;[a, ...b] = [1, 2, 3]
    console.log(b) // 打印结果为[2, 3]
    ~~~

## Symbol

- ES6引入的一种原始数据类型，是JS的第七种数据类型

- 表示独一无二的值

- 是一种类似于字符串的数据类型

- 特点

  - Symbol的值是唯一的，用来解决命名冲突的问题
  - Symbol值不能与其他数据进行运算

- 创建Symbol数据类型

  ~~~javascript
  // 创建Symbol
  let s1 = Symbol()
  console.log(s1, typeof s1) // 输出结果为：Symbol() 'symbol'
  
  // 添加表示的Symbol
  let s2 = Symbol('s2')
  let s2_2 = Symbol('s2')
  console.log(s2 === s2_2) // 输出结果为false
  
  // 使用Symbol for定义
  let s3 = Symbol.for('s3')
  let s3_2 = Symbol.for('s3') 
  console.log(s3 === s3_2) // 输出结果为true
  ~~~

- Symbol类型唯一合理的用法是用变量存储symbol的值，然后使用存储的值作为属性名创建对象属性，以防止属性重名

  ~~~javascript
  let method = {
      up: Symbol('up'),
      down: Symbol('down')
  } // 将up和down方法存储Symbol数据类型
  
  let game = {
      up: 'up',
      down: 'down',
      [method.up]:function () {
          console.log('up')
      } // 将method.up的值作为属性名，给game添加属性
  } 
  
  game[method.down] = function () {
      console.log('down') //将method.down的值作为属性名，给game添加属性
  }
  
  game[method.up]()
  game[method.down]() // 调用属性
  ~~~

- Symbol内置值

  - ES6提供了11个内置的Symbol值，指向语言内部使用的方法
  - 使用方法：在对象内添加名为[<u>Symbol内置值</u>]的属性

  |       Symbol内置值        |                             说明                             |
  | :-----------------------: | :----------------------------------------------------------: |
  |    Symbol.hasInstance     | 当其它对象使用instanceof运算符时，判断是否为该对象的实例时，会调用这个方法 |
  | Symbol.isConcatSpreadable | 对象的该属性赋值一个布尔值，表示该对象用于Array.prototype.concat()时，是否可以展开 |
  |    Symbol.unscopables     |    该对象指定了使用with关键字时，哪些属性会被with环境排除    |
  |       Symbol.match        | 当执行str.match(myObject)时，如果该属性存在，会调用它，返回该方法的返回值 |
  |      Symbol.replace       | 当对象被str.replace(myObject)方法调用时，会返回该方法的返回值 |
  |       Symbol.search       | 当对象被str.search(myObject)方法调用时，会返回该方法的返回值 |
  |       Symbol.split        | 当对象被str.split(myObject)方法调用时，会返回该方法的返回值  |
  |      Symbol.iterator      | 对象镜像for...of循环时，会调用该方法，返回该对象的默认遍历器 |
  |    Symbol.toPrimitive     | 该对象被转换为原始类型的值时，会调用该方法，返回该对象对应的原始类型值 |
  |    Symbol.toStringTag     |       在该对象上调用toString方法时，返回该方法的返回值       |
  |      Symbol.species       |                 创建衍生对象时，会使用该属性                 |

## 迭代器(Iterator)

- 迭代器是一种接口，为各种不同的数据结构提供同一的访问机制，任何数据结构只要部署了Iterator接口，就可以完成遍历操作

- ES6创造了一种新的遍历命令for...of，Iterator接口主要供for...of消费

- 原生具备iterator接口的数据，可直接用for...of遍历，可使用展开运算符展开

  - Array
  - Arguments
  - Set
  - Map
  - String
  - TypeArray
  - NodeList

- 工作原理

  - 创建一个指针对象，指向当前数据结构的起始位置
  - 第一次调用对象的next方法，指针自动指向数据结构的第一个成员
  - 接下来不断调用next方法，指针一直往后移动，直到指向最后一个成员
  - 每次调用next方法会返回一个包含value和done属性的对象
  - done的属性值为布尔值，当done的值为false时遍历结束

- 配置迭代器

  ~~~javascript
  const obj = {
    a: 1,
    arr: [1, 2, 3]
  }
  // 需求：使用for...of遍历obj中的arr
  
  // 创建obj的遍历器，即obj[Symbol.iterator]()的返回对象
  obj[Symbol.iterator] = function () {
    _this = this
    // 下面的这个return的对象就是遍历器
    return {
        i: 0,
        // 遍历器会不断调用自身的next方法，每次调用都会返回一个包含value和done属性的对象
        next() {
            // 使第一次调用next方法时i为0，每次调用next方法时i自增1，在调用次数超过了要遍历的数组的长度时，将false传入返回对象的done属性
            if (this.i < _this.arr.length) {
                return { value: _this.arr[this.i++], done: false }
            } else {
                return { done: true }
            }
        }
    }
  }
  // 之后便可以用for...of方法遍历obj中的arr
  for (let v of obj) {
    console.log(v);
  }
  ~~~
  

## Set

- ES6提供的数据结构Set（集合）

- 类似于数组，但成员的值都是唯一的

- 集合实现了iterator接口，可以使用扩展运算符展开，可以使用for...of进行遍历

- Set的属性和方法

  - size
    - 返回集合的元素个数
  - add()
    - 增加一个新元素，返回当前集合
  - delete()
    - 删除指定元素，成功则返回true，否则返回false
  - has()
    - 检测集合只能够是否包含某个元素，返回布尔值
  - clear()
    - 清空集合，返回undefined

- 使用Set处理数组

  - 数组去重

    ~~~javascript
    const arr = [1, 2, 3, 4, 2, 1, 4, 2]
    const result = [...new Set(arr)]
    console.log(result)
    ~~~

  - 求交集

    ~~~javascript
    const arr1 = [1,2,3,4,5]
    const arr2 = [3,4,5,6,7]
    const result = [...new Set(arr1)].filter(item => new Set(arr2).has(item))
    console.log(result)
    ~~~

  - 求并集

    ~~~javascript
    const arr1 = [1,2,3,4,5]
    const arr2 = [3,4,5,6,7]
    const result = [...new Set([...arr1,...arr2])]
    console.log(result)
    ~~~

  - 求补集

    ~~~javascript
    const arr1 = [1, 2, 3, 4, 5]
    const arr2 = [3, 4, 5, 6, 7]
    const result = [...new Set(arr1)].filter(item=>!new Set(arr2).has(item))
    console.log(result)
    ~~~

## Map

- ES6提供的数据结构Map
- 类似于对象，也是键值对的集合
- 键的范围不限于字符串，各种类型的值（包括对象）都可以当作键
- Map实现了iterator接口，可以使用扩展运算符展开，可以使用for...of进行遍历

- Map的属性和方法

  - size
    - 返回Map的元素个数
  - set()
    - 添加一个新元素，返回当前Map
  - get()
    - 返回键名对象的键值
  - delete()
    - 删除某个键，成功则返回true，否则返回false
  - has()
    - 检测Map中是否包含某个元素，返回布尔值
  - clear()
    - 清空Map，返回undefined

- 创建map

  ~~~javascript
  // 创建一个空map
  let m = new Map()
  // 创建一个非空map
  let m2 = new Map([
      ['name','小明'],
      [[1,2],[2,3]]
  ])
  ~~~

## class(类)

- ES6提供了更接近传统语言的写法，引入了Class(类)这个概念，作为对象的模板

- 通过class关键字，可以定义类

- 基本上，ES6的class可以看作是一个语法糖，它的绝大部分功能，ES5都可以实现，新的class写法只是让对象的原型写法更清晰，更新面向对象编程的语法

- 关键字

  - class
    - 声明类
  - consturctor
    - 定义构造函数初始化
  - extends
    - 继承父类
      - 父类方法可以重写
  - super
    - 调用父级构造方法（创建子类并定义构造器时必须调用super()）
  - static
    - 定义静态方法和属性
  - get/set
    - 类似于Object扩展写法

- 示例

  ~~~javascript
  // 父类
  class Phone {
      // 定义构造器
      constructor(brand, color, price) {
          this.brand = brand
          this.color = color
          this.price = price
      }
      // 添加方法
      call() {
          console.log('call')
      }
  }
  
  // 子类
  class SmartPhone extends Phone {
      // 定义构造器
      constructor(brand, color, price, screen, pixel) {
          // 调用父级构造器
          super(brand, color, price) // 创建子类并定义构造器时必须在使用this之前调用super()
          this.screen = screen
          this.pixel = pixel
      }
      // 添加子类方法
      photo() {
          console.log('photo')
      }
      game() {
          console.log('game')
      }
      // 方法重写
      call() {
          console.log('vedio call')
      }
      // 静态方法（可以被SmartPhone本身调用，不会继承给实例对象）
      static run() {
          console.log('run')
      }
      static connect() {
          console.log('connect')
      }
  }
  
  // 实例化对象
  const E71 = new Phone('Nokia', 'grey', 2000)
  const iphone20 = new SmartPhone('apple', 'white', 6000, '5.5inch', '1200w')
  
  // 调用子类方法
  iphone20.game()
  // 调用重写方法
  iphone20.call()
  // 调用静态方法
  SmartPhone.run()
  ~~~

- 定义类时可以写的内容
  
  - 构造器函数
    - 定义实例的属性和方法，可接收参数，并在创建实例时传入值
  - 赋值语句
    - 定义实例的属性和方法，但在定义完类之后以无法改变
  - 定义原型方法
    - 类的原型上的方法，可被实例调用
  - 定义静态方法
    - 类本身的方法，可通过类自身调用
  
- 注意：
  
  - ES6的类中的方法会自动开启严格模式，当这些方法交给window调用时，this会指向undefined
  - 定义类时，如果在构造器外直接写赋值语句，相当于给这个类的实例对象本身直接追加属性

## 数值扩展

- 二进制和八进制
  - ES6提供了二进制和八进制的新写法，分别用前缀0b和0o表示
- Number.isFinite(数字)
  - 检查一个数值是否为有限的
  - 同全局方法isFinite()
- Number.isInteger(数字)
  - 判断一个数值是否为整数
- Number.isNAN()
  - 检查一个值是否为NaN
- Number.parseInt()/Number.parseFloat()
  - ES6将全局方法parseInt()和parseFloat()移植到Number对象上面，使用不变
- Math.trunc(数字)
  - 去除一个数的小数部分，返回整数部分
  - 与Math.floor()的区别在于数字为负数时

## Object扩展

- Object.is(值1, 值2)
  - 判断两个值是否全等
  - 与[=== ]的区别：Object.is(NaN, NaN)的结果为true
- Object.assign(目标对象, ...源对象)
  - 将所有可枚举(Object.prototype.propertyIsEnumerable(属性名)返回ture)的自有属性(Object.prototype.hasOwnProperty(属性名)返回true)从一个或多个源对象复制到目标对象，同名属性会覆盖，返回修改后的对象
- Object.prototype.toString.call(数据)
  - 返回表示数据类型的字符串
- 直接设置对象原型
  1. 给对象的\_\_proto\_\_直接赋值
  2. Object.setPrototypeOf(对象, 指定新的原型对象)

## 深拷贝

### JSON实现深拷贝

- JSON.stringify(JS对象)
  - 将JS对象转化为JSON格式字符串
- JSON.parse(JSON字符串)
  - 将JSON格式字符串转化为JS对象
- JSON方法转换可以实现JS对象的属性的深拷贝，但无法克隆JS对象的方法

### 递归深拷贝

~~~javascript
function deepCopy(data) {
    // 判断data的数据结构类型是对象还是数组，创建容器
    function dataType(data) {
        return Object.prototype.toString.call(data).slice(8, -1)
    }
    let container
    if (dataType(data) === 'Object') {
        container = {}
    }
    if (dataType(data) === 'Array') {
        container = []
    }
    // 遍历数据
    for (let i in data) {
        if (dataType(data[i]) === 'Object' || dataType(data[i]) === 'Array') {
            container[i] = deepCopy(data[i])
        } else {
            container[i] = data[i]
        }
    }
    return container
}
~~~
