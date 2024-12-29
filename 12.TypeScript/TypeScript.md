## `TypeScript` 作用

- 错误出现的越早越好
  - 能在写代码的时候发现错误，就不要在代码编译时再发现
  - 能在代码编译期间发现错误，就不要在代码运行期间再发现
  - 能在开发阶段发现错误，就不要在测试阶段发现错误，能在测试阶段发现错误，就不要在上线后发现错误
- 为了弥补 `JavaScript` 类型约束上的缺陷，`2014` 年，微软推出了 `TypeScript 1.0` 版本
- `TypeScript` 是拥有类型的 `JavaScript` 超集，它可以编译成普通、干净、完整的 `JavaScript` 代码

## `TypeScript` 的编译环境

- `typescript` 编译

  ~~~shell
  # 安装
  npm i typescript -g
  
  # 查看版本
  tsc --version
  
  # 初始化 ts 配置文件 tsconfig.json
  tsc --int
  
  # 将 ts 文件编译成 js 文件
  tsc test.js
  ~~~

- 自动编译 `TS` 的环境

  - 方式一：`webpack`
    - [TypeScript(二)使用Webpack搭建环境 (qq.com)](https://mp.weixin.qq.com/s/wnL1l-ERjTDykWM76l4Ajw)
  - 方式二：使用 `ts-node`

- `ts-node`

  - 安装对应依赖

    ~~~shell
    npm i ts-node tslib @types/node -g
    ~~~

  - 通过 `ts-node` 运行 `TS` 代码

    ~~~shell
    ts-node test.ts
    ~~~

## `TypeScript` 基础特性

### 类型注解和类型推导

- 在 `TS` 中定义变量需要指定标识符的类型

- 完整的声明格式

  - `声明关键字 标识符: 数据类型 = 赋值;`

    ~~~typescript
    const message: string = 'Hello World'
    ~~~

  - 声明了类型后 `TS` 会进行类型检测，声明的类型可以称之为**类型注解**(`Type Annotation`)

- 变量的类型推导/推断(`Type infer`)
  - 如果在声明时不进行类型注解，那么 `TS` 会在给变量赋值时自动推导出对应的变量类型
  - 如果使用 `let` 进行类型推导，推导出来的是**通用类型**
  - 如果使用 `const` 进行类型推导，若是简单数据类型的值则推导出来的是**字面量类型**
  
- 函数中的类型注解和类型推导
  
  - `TS` 允许指定函数的参数和返回值的类型
  
    ~~~typescript
    function greet(name: string, age: number): string {
        return '我是' + name + '，今年' + number + '岁。'
    }
    // 通常参数需要类型注解，因为它无法自行推导，而返回值可以自行推导
    // 当编写工具库时，为了方便理解，可以手动为返回值进行类型注解
    ~~~
  
  - 匿名函数的类型注解
  
    ~~~typescript
    const nums: number[] = [1, 2, 3]
    nums.forEach((item, index, arr) => {
        ......
    })
    // 此处匿名函数不需要进行类型注解，TS 会根据 forEach 函数的类型以及数组的类型推断出参数的类型，这个过程称之为上下文类型(contextual typing)
    ~~~

### 类型

#### 数组类型

~~~typescript
const nums: number[] = [1, 2, 3]
const nums2: Array<number> = [1, 2, 3]
// nums 和 num2 中只能添加 number 类型的数据
~~~

#### 对象类型

- 基本写法

  ~~~typescript
  const info: {name: string, age: number} = {
      name: 'zzc6332',
      age: 18
  }
  ~~~

- 使用类型别名定义对象类型

  ~~~typescript
  type InfoType = {
      name: string // 换行时可以省略逗号或分号
      age: number
      height?: string // 可选类型写法，等同于该类型是指定类型和 undefined 的联合类型
      // 在函数的形参中也可使用可选类型
  }
  
  const info: InfoType = {   
      name: 'zzc6332',
      age: 18
  }
  ~~~
  
  - 采用了一个对象类型作为 `info` 的类型
  - 属性之间可以使用 `,` 或 `;` 分隔，最后一个分隔符是可选的
  - 属性名后加上 `?` 代表这是一个可选类型(`Optional Properties`)
  - 在属性名前加上 `readonly` 代表这是一个只读属性(`Readonly Properties`)
  - 每个属性的类型可以指定，如果不指定，则默认为 `any` 类型

#### `any` 类型

- 当无法确定一个变量的类型时，可以使用 `any` 类型
- 可以给一个 `any` 类型的变量赋值任何的值
- 可以为一个 `any` 类型的变量进行任何操作，包括获取不存在的属性和方法

#### `unknown` 类型

- 也是用于描述一个不确定类型的变量

- 可以给一个 `unknown` 类型的变量赋值任何的值

- 直接对一个 `unknown` 类型变量的值进行任何操作都是非法的

- 如果需要对一个`unknown` 类型变量的值进行操作，需要先进行类型缩小操作

  ~~~typescript
  let foo: unknown = 'aaa'
  
  if (typeof foo === 'string'){ // 校验变量的类型就是类型缩小操作
      console.log(foo.length)
  }
  ~~~

#### `void` 类型

- 当一个函数没有返回值时，那么它的返回值就是 `void` 类型

- 补充

  - 如果没有显式指定一个函数的返回类型，且显式返回了 `undefined`，那么它的返回值是 `undefined` 类型

  - 可以将 `undefined` 赋值给 `void` 类型

  - `undefined` 值可以的 `undefined` 类型，也可以是 `void` 类型

  - 一般情况下不需要显式指定函数的返回值为 `void` 类型，此时如果将函数的返回值赋值给一个变量

    ~~~typescript
    function foo1() {}
    function foo2() {
      return undefined
    }
    
    const a = foo1() // a 是 void 类型
    const b = foo2() // b 是 undefined 类型
    ~~~

  - 当基于上下文的类型推导推导出返回类型为 `void` 时，并不强制函数一定不能返回内容

    ~~~typescript
    const nums: number[] = [1, 2, 3]
    nums.forEach((item, index, arr) => {
        return 123 // 虽然该匿名函数的推导出的返回值类型是 void，但返回内容时不会报错
    })
    ~~~

- `void` 类型的常用场景

  ~~~typescript
  type FooType = () => void // 表示 FooType 类型是一个返回值为 void 的函数
  
  const foo: Footype = () = {
      ......
  }
  ~~~

#### `never` 类型 

- `never` 类型表示一个值不会再被观察到

- 示例

  ~~~typescript
  // 类型缩小时产生的 never，当 TS 确定在联合类型中已经没有可能是其中的类型的时候出现
  let x = 1
  if (typeof x === 'string'){
    x // 此处 x 为 never 类型
  }
  ~~~

  ~~~typescript
  let arr = [] // arr 的类型为 never[]
  ~~~

  ~~~tsx
  // 当指定一个函数返回值为 never 时，表示这个函数一定会丢一个异常或陷入死循环，不能具有可访问的重点
  
  function foo1(msg: string): never {
    while(true){
      console.log(msg)
    }
  }
  
  function foo2(msg: string): never {
    throw new Error(msg)
  }
  ~~~

- 应用场景

  ~~~typescript
  function handleMessage(message: string | number | boolean) {
    switch (typeof message) {
      case "string":
        console.log(message.length)
        break
      case "number":
        console.log(message)
        break
      default:
        const check: never = message // 编辑器提示：不能将类型“boolean”分配给“never”
        // 因为 check 被指定为了 never，不能将 never 分配给除了 never 的任何类型，因此必须用 case 将 message 所有可能的类型都处理掉
    }
  }
  ~~~

#### `tuple` 类型

- `tuple` 是元组类型

- 元组类型类似数组，区别

  - 数组中通常建议存放相同类型的元素，否则取出 `item` 时无法确定类型
  - 元组中每个元素有自己特定的类型，取出 `item` 时可以确定对应的类型

- 例

  ~~~typescript
  // 数组类型
  const info1: (string | number)[] = ['zzc6332', 18]
  const item1 = info1[0] // 无法确定类型，只知道是 stirng | number
  
  // 元组类型
  const info2: [string, number] = ['zzc6332', 18]
  const item2 = info2[0] // 能确定是 string 类型
  ~~~


### 组合类型

- `TS` 的类型系统允许使用多种运算符，从现有类型中构建新类型

#### 联合类型(`Union Types`)

- 联合类型是由两个或者多个其他类型组成的类型，表示可以是这些类型中的任何一个值

- 联合类型中的每一个类型被称之为`联合成员（union's members）`

- 联合类型使用 `|` 分隔

- 例

  ~~~typescript
  function foo(message: number | string) {
    // 使用时必须要缩小类型
    if (typeof message === "string") {
      console.log(message.length)
    }
  }
  ~~~

#### 交叉类型(`Intersection Types`)

- 交叉类型表示需要满足多个类型的条件

- 交叉类型使用 `&` 分隔

- 例

  ~~~typescript
  interface IInfo {
    name: string
    age: number
  }
  
  interface ICoder {
    coding: () => void
  }
  
  const info: IInfo & ICoder = {
    name: 'zzc6332',
    age: 18,
    coding: () => {
      console.log("coding")
    }
  }
  ~~~

- 如果交叉类型存在冲突而不可能被满足，则会出现 `never`

### 类型别名(`type`)

- 通过给对象类型或联合类型起别名，使得它们可以多次复用

- 例

  ~~~typescript
  type IDType = number | string
  function printID(id: IDType) {
      console.log(id)
  }
  ~~~

  ~~~typescript
  type InfoType = {
      name: string
      age: number
      height?: string
  }
  
  const info: InfoType = {   
      name: 'zzc6332',
      age: 18
  }
  ~~~

### 接口(`interface`)

#### 接口声明

- 可以通过 `interface` 声明对象类型

- 例

  ~~~typescript
  interface IInfo {
      name: string
      age: number
      height?: string
  }
  
  const info: IInfo = {   
      name: 'zzc6332',
      age: 18
  }
  ~~~

#### `interface` 和 `type` 的区别

- 接口几乎所有特性都可以在别名中使用

- 别名的使用类型更广，接口类型只能用于声明对象类型，但扩展性更广，如：

  - 接口声明可以多次声明同一个接口名称，重复声明接口可以对接口进行补充

    ~~~typescript
    interface IInfo {
        name: string
        age: number
    }
    
    interface IInfo {
        height?: string
    }
    ~~~

  - 接口支持继承

#### 接口继承

- 接口可以继承一个接口类型中所有的属性

  ~~~typescript
  interface IInfo {
      name: string
      age: number
  }
  
  interface IInfo2 extends IInfo{
      height?: string
  }
  ~~~

- 接口支持多继承（类不支持多继承）

  ~~~typescript
  interface IPerson {
      name: string
      eating: () => void
  }
  
  interface IAnimal {
      running: () => void
  }
  
  interface IOrcs extends IPerson, IAnimal {
      transformation: () => void
  }
  ~~~

### 类型断言(`Type Assertions`)

- `TS` 允许将获取到的数据的类型使用 `as` 操作符断言成**更具体**或**更广泛**（如断言成 `any / unknown`）的类型，但不能断言成与原先冲突的类型

- 使用场景

  - 比如在浏览器环境中通过 `document.getElementById` 使用 `ID` 获取一个 `img` 标签元素，`TS` 只能推断它会返回 `HTMLElement | null`，但无法推断出它的具体类型是 `HTMLImageElement`，此时可以将其断言为 `HTMLImageElement` 类型

    ~~~typescript
    const myEl = document.getElementById('my-img') as HTMLImageElement
    ~~~

- 会产生问题的操作

  ~~~typescript
  const a = 1 as any as string
  ~~~

### 非空类型断言

- 使用 `!` 表示确定某个标识符是有值的，可跳过 `TS` 在编译阶段对它的检测

- 例

  ~~~typescript
  interface IMessage {
    name: string
    age: number
  }
  
  function plusMessage(message?: IMessage) {
    // 这种写法会报错，可选链操作符不能用于操作属性
    // console.log(message?.age++)
      
    // 此时使用非空类型断言可跳过编译检测
    console.log(message!.age++)
  }
  ~~~

### 字面量类型(`Literal Types`)

- 可以使用值本身作为字面量类型

- 使用 `cosnt` 声明的值为简单数据类型的变量自动推导出的都是字面量类型

- **如果在只能使用类型的地方使用了字面量，则会作为字面量类型而非字面量数据**

- 字面量推断(`Literal Inference`)

  - 当初始化变量为一个对象的时候，`TS` 会假设这个对象的属性的值未来会被修改，默认推断属性为通用类型
  
  - 如果要求对象中的属性类型是字面量类型，可以使用 `as const`
  
    ~~~typescript
    const info = {   
        name: 'zzc6332',
        age: 18
    } as const
    // 此时 info 推导出来的对象类型中的属性都是字面量类型
    ~~~

### 类型缩小(`Type Narrowing`)

- 通过条件分支和类似于 `typeof number === "number"` 的判断语句，来改变 `TS` 的执行路径

- 在给定的执行路径中，可以缩小比声明时更小的类型，这个过程称之为类型缩小

- 编写的判断语句可以称之为类型保护(`Type Guards`)

- 常见的类型保护有

  - `typeof`

  - 平等缩小，如 `===`、`==`、`!==`

    - 一般配合 `switch` 语句，常用于字面量类型的判断

  - `instanceof`

  - `in`

    - 用于确定对象是否具有带名称的属性

      ~~~typescript
      interface IFish {
        swim: () => void
      }
      
      interface IDog {
        run: () => void
      }
      
      function move(animal: IFish | IDog) {
        if ('swim' in animal) {
          animal.swim()
        } else {
          animal.run
        }
      }
      ~~~
    
  - 类型谓词，见 `关键字-is`

### 函数类型相关

#### 函数类型表达式(`Function Type Expressions`)

- 最简单描述一个函数的方式是使用函数类型表达式，它的写法有点类似于箭头函数

  ~~~typescript
  function greeter(fn: (a: string, b: string) => void) {
    fn("Hello", "World")
  }
  
  greeter(function(s) {
    console.log(s)
  })
  // 虽然 fn 接收的函数类型有两个参数，但如果传入的函数只接收一个参数也不会报错，如同在 js 中
  // fn 接收的函数类型表达式中不要使用可选类型
   
  greeter(function(s1, s2) {
    console.log(s1 + ' ' + s2)
  })
  ~~~

- 语法 `(a: string) => void` 表示一个函数有一个名为 `a`，类型是字符串的参数，这个函数没有返回任何值

- 如果一个函数参数的类型没有明确给出，它会被隐式设置为 `any`

- 如果在函数类型表达式前加 `new` 关键字，则表示这是一个构造函数类型表达式

#### 调用签名(`Call Signatures`)

- 如果想描述一个带属性的函数，可以在一个对象类型中写一个调用签名

  ~~~typescript
  type DescribableFunction = {
    description: string
    (someArg: number): boolean
  }
  function doSomething(fn: DescribableFunction) {
    console.log(fn.description + " returned " + fn(6))
  }
  ~~~

- 调用签名与函数表达式的表达稍有不同，在参数列表和返回类型之间用的是 `:` 而不是 `=>`

#### 构造签名(`Construct Signatures`)

- `TS` 的函数也可以使用 `new` 操作符调用，调用时 `TS` 会认为这是一个构造函数

- 可以写一个构造签名，方法是在调用签名前加一个 `new` 关键字

  ~~~typescript
  type SomeConstructor = {
    new (s: string): SomeClass
  }
  function fn(ctor: SomeConstructor) {
    return new ctor("hello")
  }
  class SomeClass {
      ......
  }
  ~~~

- 一些对象，比如 `Date` 对象，可以直接调用，也可以使用 `new` 操作符调用，此时可以将调用签名和构造签名合并在一起

  ~~~typescript
  interface CallOrConstruct {
    new (s: string): Date;
    (n?: number): number;
  }
  ~~~
  
- 如果在函数类型表达式前加 `new` 关键字，则表示这是一个构造函数类型表达式

#### 重载签名(`Overload Signatures`)

- 在 `TS` 中可以为函数编写不同的重载签名，来表示函数可以用不同的方式进行调用

- 一般是编写两个以上的重载签名，再去编写一个通用的函数以实现

  ~~~typescript
  // 编写重载签名
  function add(arg1: number, a: number): number
  function add(arg1: string, arg2: string): string
  function add(arg1: string | number, arg2: string | number): string
  
  // 编写通用函数
  function add(arg1: any, arg2: any) {
    return arg1 + arg2
  }
  
  console.log(add(1, '2')) // 会根据传入的类型选择重载签名以执行函数
  ~~~

- 函数的重载签名必须在实际函数定义之前定义

- 一般情况下，尽可能使用联合类型来实现不确定的参数

#### 参数相关

- 可以指定某个参数是可选的，使用该参数时需进行类型缩小

  ~~~typescript
  function foo(x: number, y?: number) {
    // y 的类型是 number | undefined 联合类型
    if (typeof y === 'number') {
      return x + y
    } else {
      return x
    }
  }
  ~~~

- `TS` 支持参数默认值

  ~~~typescript
  function foo(x: number, y = 2) { // 使用参数默认值时，参数的类型注解可以省略
    return x + y
  }
  ~~~

- 剩余参数

  ~~~typescript
  function foo (...nums: number[]) {
      console.log(nums)
  }
  ~~~

#### `this` 的类型

- `this` 的编译选项

  - `tsconfig.json` 中，`noImplicitThis` 选项决定是否明确 `this` 的类型
  - 如为 `true`，则会根据上下文推导 `this`，但无法正确推导时仍会报错，需要明确指定 `this`

- 指定 `this` 类型

  - 使用第一个参数类型指定 `this` 的类型，该形参名必须为 `this`
  - 后续调用函数传入参数时，从第二个参数开始传递，`this` 参数会在编译后被抹除

- `this` 相关的内置工具

  - `TS` 提供了一些工具类型来辅助进行常见的类型转换，这些类型全局可用

    - `ThisParamterType`

      - 用于提取一个函数类型的 `this` 参数类型

      - 如果这个函数类型没有 `this` 参数则返回 `unknown`

      - 示例
  
        ~~~typescript
        function foo(this: {name: string}) {
            console.log(this.name)
        }
        
        type FooThisType = ThisParameterType<typeof foo>
        ~~~

    - `OmitThisParameter`

      - 用于移除一个函数类型的 `this` 参数类型，并返回修改后的函数类型
  
      - 不会改变函数本身的类型
  
      - 示例
  
        ~~~typescript
        function foo(this: {name: string}) {
            console.log(this.name)
        }
        
        type FooType = OmitThisParameter<typeof foo>
        ~~~

    - `ThisType`
  
      - `ThisType` 被用作标记一个上下文的 `this` 类型
  
      - 示例
  
        ~~~typescript
        interface IInfo {
          name: string
          age: number
        }
        
        interface IPerson {
          info: IInfo,
          running: () => void
        }
        
        // 意为 IInfo 类型作为 this 的类型绑定给 person 对象中的所有方法
        const person: IPerson & ThisType<IInfo> = {
          info: { name: "abc", age: 18 },
          running() {
            console.log(this.name + ' is running')
          }
        }
        
        person.running.call(person.info)
        ~~~

## `TypeScript` 面向对象编程

### 类的定义

~~~typescript
class Person {
  // 声明成员属性
  name: string // 指定类型
  age: number
  isPerson = true // 设置初始化值

  constructor(name: string, age: number, isPerson?: boolean) {
    this.name = name
    this.age = age
    if (typeof isPerson === 'boolean') {
      this.isPerson = isPerson
    }
  }
    
  running() {
    console.log(this.name + " is running")
  }
}

const p1 = new Person('abc', 18)
p1.running()
~~~

### 类的继承

~~~typescript
class Student extends Person {
  sno: string

  constructor(name: string, age: number, sno: string, isPerson?: boolean) {
    super(name, age, isPerson)
    this.sno = sno
  }

  studying() {
    console.log(this.name + " is studying")
  }
}

const sdt1 = new Student("abc", 18, "001")
sdt1.studying()
~~~

### 类的成员修饰符

- 在 `TS` 中，类的属性和方法支持三种可见性修饰符
  - `public`
    - 修饰的是在任何地方可见、共有的属性或方法，默认编写的属性就是 `public` 的
  - `private`
    - 修饰的是仅在同一类中可见、私有的属性或方法
  - `protected`
    - 修饰的是仅在类自身及子类中可见、受保护的属性或方法

- 示例

  ~~~typescript
  class Person {
    protected name: string
    private age: number
  
    constructor(name: string, age: number) {
      this.name = name
      this.age = age
    }
  
    running() {
      console.log(this.name + " is running")
    }
  }
  
  class Student extends Person {
    sno: string
  
    constructor(name: string, age: number, sno: string, isPerson?: boolean,) {
      super(name, age)
      this.sno = sno
    }
  
    studying() {
      console.log(this.name + " is studying")
    }
  }
  
  const sdt1 = new Student("abc", 18, "001")
  sdt1.studying()
  
  console.log(sdt1.age) // 属性“age”为私有属性，只能在类“Person”中访问。ts(2341)
  console.log(sdt1.name) // 属性“name”受保护，只能在类“Person”及其子类中访问。ts(2445)
  ~~~

- 如果一个属性不希望外界任意修改，可以使用 `readonly` 修饰符

  ~~~typescript
  class Person {
    readonly name: string
    constructor(name: string) {
      this.name = name
    }
  }
  
  const p = new Person('abc')
  console.log(p.name)
  
  p.name = 'cba' // 无法为“name”赋值，因为它是只读属性。ts(2540)
  ~~~

### 存取器(`getters`/`setters`)

- 可以通过 `getters/setters` 对私有属性进行读取和修改

  ~~~typescript
  class Person {
    private _name: string
    private _age: number
  
    constructor(name: string, age: number) {
      this._name = name
      this._age = age
    }
  
    get name() {
      return this._name
    }
  
    get age() {
      return this._age
    }
  
    set age(newValue: number) {
      if (newValue >= 0 && newValue < 150) {
        this._age = newValue
      }
    }
  }
  
  const p1 = new Person('abc', 18)
  
  console.log(p1.name) // 'abc'
  
  p1.age = 20
  p1.age = -1
  console.log(p1.age) // 20
  ~~~

### 参数属性(`Parameter Properties`)

- `TS` 提供了语法糖，可以把一个构造函数参数转成一个同名同值的类属性

- 可以通过在构造函数参数前添加一个可见性修饰符 `public`、`private`、`protected` 或只读修饰符 `readonly` 来创建参数属性，最后这些类属性字段也会得到这些修饰符

- 例

  ~~~typescript
  class Person {
      constructor(public name: string, private _age: number){}
  }
  
  //等同于
  class Person {
      name: string
      private _age: number
      
      constructor(name: string, _age: number) {
          this.name = name
          this._age = _age
      }
  }
  ~~~

### 抽象类(`Abstract`)

- 抽象类可以实现面向对象编程中的多态特征

- 抽象类与抽象方法

  - 抽象类是使用 `abstract` 声明的类
  - 抽象方法是没有具体实现的方法（没有方法体），也是用 `abstract` 声明
  - 抽象方法必须存在于抽象类中

- 抽象类的特点

  - 抽象类无法被实例化
  - 抽象类可以包含抽象方法，也可以包含有实现体的方法
  - 抽象类的子类必须实现父类中的抽象方法，除非该子类也是抽象类

- 使用示例

  ~~~typescript
  // 抽象类
  abstract class Shape {
    abstract getArea(): number
  }
  
  // 子类
  class Circle extends Shape {
    constructor(private r: number) {
      super()
    }
  
    getArea() {
      return this.r * this.r * Math.PI
    }
  }
  
  class Rectangle extends Shape {
    constructor(private w: number, private h: number) {
      super()
    }
  
    getArea() {
      return this.w * this.h
    }
  }
  
  // 通用实现函数
  function calcArea(shape: Shape) {
    return shape.getArea()
  }
  
  console.log(calcArea(new Circle(10)))
  console.log(calcArea(new Rectangle(20, 30)))
  ~~~

### 类的类型

~~~typescript
class Person {
  constructor(public name: string, public age: number) { }
}

const p1: Person = new Person('abc', 18) // 类本身可以作为它的实例的类型
const p2: Person = {name:'abc', age: 18} // 由于 TS 使用鸭子类型，该赋值方式也成立

// 类可以当作一个有构造签名的函数
function factory(ctor: new (name: string, age: number) => void) {
  const p = new ctor('abc', 18)
  console.log(p)
}

factory(Person)
~~~

#### 类型检测-鸭子类型

- `TS` 对于类型检测的时候使用的是鸭子类型

- 鸭子类型：当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子

- 鸭子类型只关心属性和行为，不关心对象所属的具体类型

- 示例

  ~~~typescript
  class Person {
    constructor(public name: string, public age: number) { }
  }
  
  class Dog {
    constructor(public name: string, public age: number) { }
  }
  
  const p1: Person = new Dog('Mocha', 1)
  ~~~

### 类与接口

#### 类实现接口

- 如果接口被一个类实现，那么在之后需要传入接口的地方，都可以将这个类传入

- 这种方式是面向接口开发 

- 示例

  ~~~typescript
  interface IInfo {
    name: string
    age: number
  }
  
  interface IRun {
    running: () => void
  }
  
  interface ISwim {
    swimming: () => void
  }
  
  class Person implements IInfo, ISwim, IRun {
    constructor(public name: string, public age: number) { }
    running() {
      console.log(this.name + " is running")
    }
    swimming() {
      console.log(this.name + " is swimming")
    }
  }
  
  const p = new Person('abc', 18)
  
  p.running()
  p.swimming()
  ~~~

#### 抽象类和接口的区别

- 抽象类在很大程度上和接口类似，都可以在其中定义方法，让子类或实现类来实现对应的方法
- 抽象类和接口的区别
  - 抽象类是事物的抽象，用来捕捉子类的通用特性，而接口通常是一些行为的描述
  - 抽象类通常用于一系列关系紧密的类之间，接口只是用来描述一个类应该具有什么行为
  - 接口可以为被多层实现（如一个类可以实现多个接口），抽象类在大多数面向对象语言中只能单一继承
  - 抽象类中除了抽象方法还可以有具有实现体的具体方法，接口中之只能有函数的声明而没有实现体
- 通常会这样描述抽象类、接口与类之间的关系
  - 比如动物是一个抽象类，具体的猫是一种动物，猫是动物的子类，猫的行为比如爬树、喵喵叫等行为分别是一个个接口
  - 抽象类和子类之间就是动物和猫的关系，代表猫是一种动物
  - 类和接口之间就是猫和猫拥有的行为的关系，代表猫可以爬树和喵喵叫

### 索引签名(`Index Signatures`)

- 有时候无法提前知道一个类型里所有属性的名字，但是知道这些值的特征，这种情况可以用一个索引签名来描述可能的值的类型

  ~~~typescript
  interface IInfo {
    name: string
    age: number
    [key: string]: string | number // 使用索引签名，表示可以使用一个字符串索引获取值，获取到的值可以是 string | number 类型
  }
  
  const info1: IInfo = {
    name: 'abc',
    age: 18,
    height: 190, // 为属性赋值时可以使用 string | number 类型
    1: 1, // 注意，读取对象的属性时，number 类型的属性名会被转换成 string 类型，因此当索引签名要求属性名为 string 时，仍可以定义 number 类型的属性名
  }
  
  console.log(info1.height)
  console.log(info1.a) // 注意 info1.a 的值应是 undefined，但类型检测认为它是 string | number 类型
  
  // ICollection 类型的参数可以接收数组类型和 tuple 类型
  interface ICollection {
    [index: number]: number
    [key: string]: any // 如果要读取数组的其它属性和方法，需要使用字符串索引签名
    length: number // length 的返回类型要满足字符串索引签名的返回类型
  }
  
  function printCollection(collection: ICollection) {
    for (let i in collection) {
      console.log(collection[i])
    }
  }
  
  const array: number[] = [1, 2]
  const tuple: [number, number] = [3, 4]
  
  printCollection(array)
  printCollection(tuple)
  ~~~

- 一个索引签名的属性类型必须是 `string` 或是 `number`

- 可以同时使用数字索引签名和字符串索引签名，但数字索引返回类型一定要是字符串索引返回类型的子类型

- 索引签名所在类型中如果定义了其它属性，那它们的返回类型也要符合对应的索引签名的返回类型

### 严格字面量检测现象/类型擦除

- 示例

  ~~~typescript
  interface IPerson {
    name: string
    age: number
  }
  
  const obj = { name: 'abc', age: 18, height: 188 }
  
  const p1: IPerson = obj // 可以赋值
  
  const p2: IPerson = { name: 'abc', age: 18, height: 188 } // 不能将类型“{ name: string; age: number; height: number; }”分配给类型“IPerson”
  ~~~

- 现象解释

  - 第一次创建的字面量分配给一个变量或传递给一个非空目标类型的参数时，会进行严格的对象类型检测，目标类型中不存在的属性是不允许存在于字面量中的
  - 当类型断言或字面量的类型扩大时，新鲜度消失，此时可以存在目标类型中不存在的属性，方便参数的传递

### 枚举类型(`enum`)

- 将一组可能出现的值，一个个枚举出来，定义在一个类型中，这个类型就是枚举类型

- 枚举类型即是一种数据类型，也可以当作数据来使用，详见 `关键字 - enum`

- 枚举允许开发者定义一组命名常量，可以是数字或字符串类型

- 示例

  ~~~typescript
  enum Direction {
    LEFT,
    RIGHT
  }
  
  function turnDirection(direction: Direction) {
    switch (direction) {
      case Direction.LEFT:
        console.log("向左")
        break
      case Direction.RIGHT:
        console.log("向右")
        break
      default:
        const Never: never = direction
    }
  }
  
  turnDirection(Direction.LEFT)
  ~~~

- 枚举类型的值

  - 枚举类型默认是有值的

  - 没有显式赋值时，枚举类型默认会从 0 开始递增将数字隐式地赋值给每一个命名常量

    ~~~typescript
    enum Direction {
      LEFT, // LEFT === 0
      RIGHT, // RIGHT === 1
      UP, // UP === 2
      DOWN // DOWN === 3
    }
    ~~~

  - 如果显式赋值

    ~~~typescript
    enum Direction {
      LEFT = 100, // LEFT === 100
      RIGHT, // RIGHT === 101
      UP, // UP === 102
      DOWN // DOWN === 103
    }
    ~~~

    ~~~typescript
    enum Direction {
      LEFT, // LEFT === 0
      RIGHT, // RIGHT === 1
      UP = "UP", // 如果将赋值了 number 以外的其它类型的值，之后的命名常量必须显式赋值
      DOWN = "DOWN"
    }
    
    ~~~

- 在 `TS` 中定义枚举后，在 `JS` 中会被解析成对象，它们的对应关系为

  - `TS` 中

    ~~~typescript
    // 纯数字枚举
    enum ENUM_NUMBER {
      FIRST,
      SECOND,
      THIRD,
    }
    
    // 非纯数字枚举
    enum ENUM_WHEREVER {
      FIRST = "第一个",
      SECOND = "第二个",
      THIRD = 333,
    }
    ~~~

  - `JS` 中

    ~~~javascript
    var ENUM_NUMBER;
    // 纯数字枚举时，通过数字也能获取到键名值
    (function (ENUM_NUMBER) {
      ENUM_NUMBER[(ENUM_NUMBER["FIRST"] = 0)] = "FIRST";
      ENUM_NUMBER[(ENUM_NUMBER["SECOND"] = 1)] = "SECOND";
      ENUM_NUMBER[(ENUM_NUMBER["THIRD"] = 2)] = "THIRD";
    })(ENUM_NUMBER || (ENUM_NUMBER = {}));
    
    // 非数字值的枚举项只能通过键名得到值
    (function (ENUM_WHEREVER) {
      ENUM_WHEREVER["FIRST"] = "\u7B2C\u4E00\u4E2A";
      ENUM_WHEREVER["SECOND"] = "\u7B2C\u4E8C\u4E2A";
      ENUM_WHEREVER[(ENUM_WHEREVER["THIRD"] = 333)] = "THIRD";
    })(ENUM_WHEREVER || (ENUM_WHEREVER = {}));
    ~~~

- 枚举类型中的位运算

  - 位运算

    - 位运算主要用于处理二进制数据
    - 常用的运算符包括
      - `&` 按位与
        - 对两个操作数的每个对应位执行逻辑与操作，每个位中，两个位都为 `1` 时，该结果位为 `1`，否则该结果位为 `0`
      - `|` 按位或
        - 对两个操作数的每个对应位执行逻辑或操作，每个位中，两个位中至少有一个位 `1` 时，该结果位为 `1`，否则该结果位为 `0`
      - `^` 按位异或
        - 对两个操作数的每个对应位执行逻辑异或操作，当两个位不相同时，该结果位为 `1`，否则该结果位为 `0`
      - `~` 取反
        - 对操作数的每个位执行逻辑取反操作
      - `<<` 左移
        - 将操作数的所有位向左移动指定的次数，左移过程中，右侧空出的位置用 `0` 填充
      - `>>` 右移
        - 将操作数的所有位向右移动指定的次数，右移过程中，左侧空出的位用原来的符号位填充（正数用 `0` 填充，负数用 `1` 填充，与负数的补码表示法相关）

  - 枚举类型中的位运算

    ~~~typescript
    enum Flags {
      A = 1 << 0, // 表示不移位
      B = 1 << 1, // 代表 1 向左移动一位，即 0010，值为 2
      C = 1 << 2, // 代表 1 向左移动两位，即 0100，值为 4
      D = 0B1000, // 也可以用该方法表示二进制，值为 8
      "这是一个字符串属性" = "这是一个字符串值"
    } // 可以认为 Flags 中的每个属性都是二进制位中的一个位，位运算的结果中，对应的位如果是 1，则表示包括该属性，如果是 0，则表示不包括该属性
    
    // 表示 Flags.A 或 Flags.C，二进制表示为 0101，值是5
    const flag1 = Flags.A | Flags.C
    
    // 检测两个值是否有同时包括的属性
    function detectionFlags(X: number, Y: number) {
      if (X & Y) {
        // 如果 X & Y 的结果不为 0，则代表存在同时位 1 的位，即有同时包括的属性
        const res = splitValue(X & Y).map(item => Flags[item]) // 使用 Flags 中的数字类型值作为索引，能从 Flags 中获取对应的命名属性
        return res
      } else {
        return false
      }
    }
    
    const flag2 = Flags.B | Flags.D
    const flag3 = Flags.A | Flags.B | Flags.C
    
    console.log(detectionFlags(flag1, flag2)) // 返回 false，代表 flag1 和 flag2 中没有相同的 Flags
    console.log(detectionFlags(flag1, flag3)) // 返回 [ "A", "C" ]，代表 flag1 和 flag3 中都有 Flags.A 和 Flags.C
    
    // 可以使用位运算符进行标志位的移除：
    const flag4 = flag1 & ~Flags.C // 移除标志位 C，flag4 的值为 1，二进制表示为 0001
    
    // 工具函数
    
    // 将二进制码拆分为数组，如 0B1010 会被拆分为 [0B1000，0B0010]
    function splitValue(value: number): number[] {
      const bin = value.toString(2)
      const res: number[] = []
      for (let i = bin.length - 1; i >= 0; i--) {
        const char = bin.charAt(i) // 1000
        const zeros = "0".repeat(bin.length - i - 1)
        const item = parseInt((char + zeros), 2)
        if (item !== 0) res.push(item)
      }
      return res
    }
    
    // 获取对应的值
    function findFlags(value: number | string): string | number {
      return (Flags as { [key: string]: number | string })[value]
    }
    
    // 一些小发现：
    
    // - 枚举类型本身的索引签名能接收的类型是它所拥有的属性名字符串，用以获取对应的值
    // - 还可以接收属性对应的数字类型的值，用以获取属性名，如果多个属性拥有相同的值，则返回最后一个定义的属性
    console.log(Flags[0B0100])
    
    // - 如何强行让其接收任何字符串？ A: 可以使用类型断言扩大索引签名范围
    console.log((Flags as { [key: string]: number | string })['这里可以使用任意字符串']) // 打印结果为 undefined
    
    // 根据上面的内容可以用来创建一个输入任何值以返回 Flags 的属性名的函数
    function getEnumKey(value: string | number): string | number | undefined {
      return Object.keys(Flags).find(key => {
        return (Flags as { [key: string]: number | string })[key] === value
      })
    }
    
    console.log(getEnumKey("这是一个字符串值")) // 打印结果为 "这是一个字符串属性"
    ~~~

## `TypeScript` 泛型编程

### 泛型基本使用

- 泛型`(Generics)`是一种编程概念，在编程语言中用于增强代码的灵活性和可复用性
- 泛型允许我们在定义函数、类或接口时不指定具体的数据类型，而是用一个占位符来表示类型，实际使用时，可以为这个占位符提供具体的数据类型，从而使代码适用于不同的数据类型

- 在定义函数时使用泛型

  ~~~typescript
  // 定义时使用占位符表示类型
  function foo<Type>(arg: Type): Type {
    return arg
  }
  
  // 使用时提供具体类型，得到的返回值会被准确识别
  // 完整写法
  let res1 = foo<string>('abc')
  let res2 = foo<number>(1)
  let res3 = foo<number[]>([1, 2])
  
  // 省略写法，可自动根据参数推导返回值
  let res4 = foo('abc')
  let res5 = foo(1)
  let res6 = foo([1, 2])
  ~~~

- 泛型中传入多个类型

  ~~~typescript
  function foo<T, E>(a1: T, a2: E) {}
  ~~~

- 泛型中常用的占位符

  - `T`：`Type`，类型
  - `K`、`V`：`Key` 和 `Value`，键值对
  - `E`：`Element`，元素
  - `O`：`Object`，对象

- 在定义接口时使用泛型

  ~~~typescript
  interface IPerson<T1 = string, T2 = number> { // 泛型可以设置默认值
    name: T1,
    age: T2
  }
  
  const p1: IPerson<string, string> = {
    name: "abc",	
    age: "18"
  }
  
  const p2: IPerson = {
    name: "cba",
    age: 8
  }
  ~~~

- 在定义类时使用泛型

  ~~~typescript
  class Point<T = number>{
    constructor(public x: T, public y: T) { }
  }
  
  const p1 = new Point(10, 20)
  const p2 = new Point<string>('10', '20')
  const p3: Point<string> = new Point('1','2')
  // p2 和 p3 的声明方式等效
  ~~~

### 泛型约束(`Generic Constraints`)

- 可以让泛型类型参数继承一个接口，以实现泛型约束

- 使用示例

  ~~~typescript
  interface ILength {
    length: number
  }
  
  // 泛型 T 必须实现 ILength 接口
  function getLength<T extends ILength>(arg: T) {
    return arg.length
  }
  
  const length1 = getLength('aaa')
  const length2 = getLength([1, 2, 3])
  const length3 = getLength({ length: 100, name: 'abc' })
   // 由于使用了泛型，使得带有除了 length: number 属性之外属性的类型也能传入 getLength 方法中
   // 由于使用了泛型约束，使得只有拥有 length: number 属性的类型才能传入 getLength 方法中
  
  ~~~

  - 如果直接使用接口来注解传入的参数，会使得传入的参数必须完全符合该接口成员，不能有多余的成员
  - 使用泛型继承接口实现泛型约束，使得传入的参数可以自动推导，但必须有接口中的成员

- 在泛型中，类型参数也可以被其它类型参数约束

  ~~~typescript
  function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key]
  } // K 被约束为 只能是 T 中的键名
  
  const info = {
    name: 'abc',
    age: 18
  }
  
  console.log(getProperty(info, 'name')) // 打印 "abc"
  console.log(getProperty(info, 'height')) // 类型“"height"”的参数不能赋给类型“"name" | "age"”的参数。ts(2345)
  ~~~

### 映射类型(`Mapped Types`)

- 有时一个类型需要基于另外一个类型，可以考虑使用映射类型
  - 大部分内置工具都是通过映射类型来实现的
  - 大多数类型体操的题目也是通过映射类型完成的
  - 映射类型只能用 `type` 定义
  
- 映射类型的基本使用

  ~~~typescript
  interface IPerson {
    name: string,
    age: number
  }
  
  type MapType<T> = {
    // 映射类型会遍历传入 T 的所有键名
    [K in keyof T]: T[K]
  }
  
  type newPerson = MapType<IPerson>
  ~~~

- 映射修饰符(`Mapping Modifiers`)

  - 在使用映射类型时，有两个额外的修饰符可能会用到

    - `readonly`: 用于设置属性只读
    - `?`：用于设置属性可选

  - 可以通过前缀 `-` 或者 `+` 以删除或添加这些修饰符，如果没有写前缀，则默认使用 `+` 前缀

  - 示例

    ~~~typescript
    interface IPerson {
      readonly name: string,
      age: number,
      height?: number
    }
    
    type MapType<T> = {
      -readonly [key in keyof T]-?: T[key]
    }
    
    type newPerson = MapType<IPerson>
    ~~~

### 条件类型(`Conditional Types`)

- 条件类型可根据条件选择不同的类型

- 条件类型使用条件表达式来确定要返回的类型

- 条件类型通常与泛型一起使用，以根据输入类型的属性或条件来确定输出类型

- 条件类型语法：`T exrtends U ? X : Y`

- 场景示例

  - 需求

    - 定义一个函数，接收两个参数，参数可以接收字符串或数字类型，但两个参数的类型必须相同，返回将两个参数相加的结果

  - 示例

    ~~~typescript
    // 在重载签名中使用泛型，在返回类型中使用条件类型
    function sum<T extends number | string>(arg1: T, arg2: T): T extends number ? number : string
    
    function sum(arg1: any, arg2: any) {
      return arg1 + arg2
    }
    
    console.log(sum(20, 30)) // 50
    console.log(sum("20", "30")) // "2030"
    ~~~

- 在条件类型中推断(`Inferring Within Conditional Types`)

  - 条件类型提供了 `infer` 关键字，可以从正在比较的类型中推断类型，然后 `true` 分支里引用该推断结果

  - 场景示例

    - 需求

      - 封装两个工具类型，接收一个函数类型作为泛型参数
      - 一个工具类型可以返回函数类型的参数类型
      - 一个工具类型可以返回函数类型的返回值类型

    - 示例

      ~~~typescript
      // 定义一个函数类型，供工具类型调用测试
      type CalcuFnType = (arg1: number, arg2: number) => number
      
      // 定义获取函数参数类型的工具类型
      type FnParamsType<T extends (...args: any[]) => any> = T extends (...args: infer P) => any ? P : never
      
      // 获取 CalcuFnType 的参数类型
      type CalcuFnParamsType = FnParamsType<CalcuFnType>
      
      // 定义获取函数返回值类型的工具类型
      type FnReturnType<T extends (...args: any[]) => any> = T extends (...args: any[]) => infer R ? R : never
      
      // 获取 CalcuFnType 的返回值类型
      type CalcuFnReturnType = FnReturnType<CalcuFnType>
      ~~~

    - 解析 

      - `type FnParamsType<T extends (...args: any[]) => any>`

        ` = T extends (...args: infer P) => any ? P : never` 

      - 结构：`type 类型名<泛型> = 条件类型` 

      - 其中泛型为`<T extends (...args: any[]) => any>`

        - 表示传入的泛型参数 `T` 需要符合 `(...args: any[]) => any`

      - 条件类型为 `T extends (...args: infer P) => any ? P : never`

        - 条件为 `T extends (...args: infer P) => any`
          - 表示传入的 `T` 符合 `T extends (...args: infer P) => any`，并且实际传入 `T` 时会推断出 `...args` 的类型为 `P`，此处的 `P` 是一个刚被定义的参数类型
        - 如果条件符合, 则在 `true` 分支中返回推断出来的 `P`，即参数列表的类型
        - 由于定义泛型时已经确定过 `T` 的条件，该条件类型一定符合条件，因此 `false` 分支返回 `never`

- 分发条件类型(`Distributive Conditional Types`)

  - 当在泛型中使用条件类型的时候，如果传入一个联合类型，就会变成分发的(`distributive`)

  - 示例

    ~~~typescript
    // toArray1<T> 与 toArray2<T> 有什么区别
    type toArray1<T> = T[]
    type toArray2<T> = T extends any ? T[] : never
    
    type newType1 = toArray1<number | string> // newType1 = (string | number)[]
    type newType2 = toArray2<number | string> // newType2 = string[] | number[]
    ~~~

    - 当传入联合类型 `string | number` 到 `toArray2<T>` 时，会遍历联合类型中的每一个成员，相当于 `toArray2<string> | toArray2<number>`

## 扩展知识

### TypeScript 模块化

- `TS` 可实现很多模块化方案，现在最主要使用的是 `ES Module`

#### 非模块(`Non-modules`)

- `TS` 和 `JS` 中的非模块
  - `JavaScript` 规范声明没有任何 `export` 的 `JS` 文件都应该被认为是一个脚本，而非一个模块
  - 在一个脚本文件中，变量和类型会被声明在共享的全局作用域，将多个输入文件合并成一个输出文件，或者在 `HTML` 中使用多个 `<script>` 标签加载这些文件
- 如果一个文件，没有任何 `import` 或者 `export`，但是仍希望将它作为模块处理，则可以在文件末尾添加`export {}`，这样会把文件改成一个没有导出任何内容的模块

#### 内置类型导入(`Inline type imports`)

- `TypeScript 4.5` 开始允许单独导入类型

- 推荐在类型前使用 `type` 前缀，表明被导入的是一个类型，这样可以让一个非 `TypeScript` 编译器，比如 `Babel`、`swc` 或者 `esbuild` 等知道什么样的导入可以被安全移除

  ~~~typescript
  import { type IFoo, type IDType } from "./foo"
  ~~~

  ~~~typescript
  // type 可以放在大括号外面，表示导入的全是类型
  import type { IFoo, IDType } from "./foo"
  ~~~

#### 命名空间(`namespace`)

- `TS` 有自己的模块格式，名为 `namespace`，在 `ES Module` 之前出现

- 命名空间在 `TS` 早期时，称之为内部模块，目的是将一个模块内部再进行作用域的划分，防止一些命名冲突的问题

- 虽然命名空间没有被废弃，但是由于 `ES Module` 已经拥有了命名空间的大部分特性，因此更推荐使用 `ES Module`，这样才能与 `JS` 的发展方向保持一致

- 示例，在不同命名空间中定义名为 `format` 的函数

  ~~~typescript
  namespace price {
      function format(price: number) {
          return "￥" + price
      }
  }
  
  namespace date {
      function format(dateString: string) {
          return dateString
      }
  }
  
  // 使用命名空间中的内容
  price.format(999)
  ~~~

### 类型查找

- 类型声明文件(`Type Declaration`)/类型定义文件(`Type Definition`)
  - 后缀为 `.d.ts`
  - 用来做类型的声明(`declare`)

#### 内置类型声明

- 内置声明类型是 `TS` 自带的，内置了一些 `JS` 运行时的一些标准化 `API` 的声明文件
  - 包括如 `Function`、`String`、`Math`、`Date` 等内置类型，也包括运行环境中的 `DOM API` 等
- 这些内置类型声明文件被命名为 `lib.[something].d.ts`
- 通常在安装 `typescript` 环境时会一起安装这些类型声明文件

- 内置声明的环境配置
  - 在 `tsconfig.json` 的 `target` 字段和 `lib` 字段中声明哪些内置类型声明可以使用
  - [TypeScript: TSConfig Reference - Docs on every TSConfig option (typescriptlang.org)](https://www.typescriptlang.org/tsconfig#lib)

#### 外部定义类型声明

- 外部的类型声明通常是使用一些第三方库时，需要的一些类型声明

- 这些库通常有两种类型声明方式

  - 方式一：在库中包含 `.d.ts` 文件
  - 方式二：额外安装用于声明类型的库，如安装 `react` 的声明类型
    - `npm i @types/react -S -D`

- 也可以自己为库定义类型声明

  - 可新建一个目录，如 `/src/types`，创建 `*.d.ts` 文件

  - 示例：为 `lodash` 自定义类型声明

    ~~~typescript
    declare module "lodash" {
        export function join(...args: any[]): any
    }
    ~~~

- 可以为任何 `JS` 内容编写类型声明

  - 为构造函数编写类型声明

    - `JS` 文件中

      ~~~javascript
      function Person(name, age) {
          this.name = name
          this.age = age
      }
      ~~~

    - `*.d.ts` 文件中

      ~~~typescript
      declare class Person {
          constructor(public name: string, public age: number) 
          // 声明类型时不写函数体
      }
      ~~~

    - `TS` 文件中

      ~~~typescript
      const p = new Person('abc', 18)
      ~~~

#### 类型声明文件语法

- `declare` 可以用于声明模块

  - 声明模块的语法：`declare module "模块名" {}`

  - 在声明模块的内部，可以通过 `export` 导出对应库的类、函数等

- `declare` 可以用于声明文件

  - 如 `*.vue` 文件等 `sfc` 文件，`*.jpg` 文件等媒体文件

  - 示例

    ~~~typescript
    // 从 .vue 后缀的文件导入的模块会被定义为 DefineComponent 类型
    declare module "*.vue" {
        import {type DefineComponent } from "vue"
        const component: DefineComponent
        export default component
    }
    
    // 从 .jpg 后缀的文件导入的模块会被定义为 string 类型（打包工具会将图片文件转换为 URI 字符串或 base64 编码字符串）
    declare module "*.jpg" {
        const src: string
        export default src
    }
    ~~~

- `declare` 命名空间

  - 如果通过 `CDN` 的方式在 `index.html` 中引入了 `JS` 代码，如 `jQuery`

  - 可以通过命名空间进行声明

    ~~~typescript
    declare namespace $ {
        // ......
        function ajax(settings: any): void
        // ......
    }
    ~~~

  - 之后就可以在 `TS` 中使用了

    ~~~typescript
    $.ajax({
        url: "......"
        success: (res: any) => {
            console.log(res)
        }
    })
    ~~~

### TS 配置文件

- `tsconfig.json` 文件的作用
  - 让 `TypeScript Compiler` 在编译时，知道如何去编译 `TS` 代码和进行类型检测
    - 比如是否允许不明确的 `this` 选项，是否允许隐式的 `any` 类型
    - 将 `TS` 代码编译成什么版本的 `JS` 代码
  - 让编辑器可以按照正确的方式识别 `TS` 代码
    - 对哪些语法进行提示、类型错误检测等
- 当目录中出现了 `tsconfig.json` 文件，则说明该目录时 `TypeScript` 项目的根目录
- 同样的，`JavaScript` 项目可以使用 `jsconfig.json` 文件，它的作用与 `tsconfig.json` 基本相同
- `tsconfig.json` 在编译时如何被使用
  - 在调用 `tsc` 命令并且没有其它输入文件参数时，编译器将由当前目录开始向父级目录寻找包含 `tsconfig` 文件的目录
  - 当命令行中指定了输入文件参数，`tsconfig.json` 文件会被忽略

#### 常见配置项

-  文档地址 [TypeScript: TSConfig Reference - Docs on every TSConfig option (typescriptlang.org)](https://www.typescriptlang.org/tsconfig)

- `files` / `include` / `exclude` 字段

  - `files`

    - 编写一个数组，用于指定项目中包括哪些文件

    - 通常项目中文件较少时使用

    - 例

      ~~~json
      "files": [
        "src/app.ts",
        "src/utils.ts"
      ]
      ~~~

  - `include`

    - 编写一个数组，用于指定项目中包括哪些文件，允许使用通配符来匹配多个文件

    - 默认匹配根目录下所有文件

    - 例

      ~~~json
      "include": [
        "src/**/*.ts",
        "test/**/*.ts"
      ]
      ~~~
  
  - `exclude`

    - 编写一个数组，用于指定从 `include` 中排除哪些文件

    - 例

      ~~~json
      "exclude": ["node_modules"]
      ~~~
  
- `compilerOptions` 字段

  ~~~json
  {
      "compilerOptions": {
          // 目标代码
          "target": "esnext",
          // 指定需要用到的库，默认根据 target 来获取
          "lib": ["esnext", "dom", "dom.iterable", "scripthost"],
          // 指定模块化方案
          "module": "commonjs",
          // 打开所有的严格模式
          "strict": true,
          // strict 为 ture 时相当于下列选项生效
          "allowJs": true,
          "noImplicitAny": false,
          "strictNullChecks": true,
          "strictFunctionTypes": true,
          "noImplicitThis": true,
          // jsx 的处理方式
          "jsx": "perserve", // 保留原有的 jsx 格式
          // 是否帮助导入一些需要的功能模块
          "importHelpers": true,
          // 模块路径解析规则
          "moduleResolution": "node",
          // 跳过对整个库进行类型检测，而仅仅检测用到的类型
          "skipLibCheck": true,
          // 让 esModule 和 commonjs 可以相互调用
          "esModuleInterop": true,
          // 允许 import xxx from "xxx" 写法，使其等同于 import * as xxx from "xxx"
          "allowSyntheticDefaultImports": true,
          // 是否生成 sourceMap 文件
          "sourceMap": true,
          // 文件路径在解析时的基本 url
          "baseUrl": ".",
          // 路径的映射设置，类似于 webpack 中的 alias
          "paths": {
              "@/*": ["src/*"]
          }
      }
  }
  ~~~

## 一些内置工具

### `Partial<T>`

- 用于构造一个 `T` 下面的所有属性都设置为可选的类型

- 使用示例

  ~~~typescript
  interface IPerson{
    name: string,
    age: number
  }
  
  type IPersonOptional = Partial<IPerson>
  ~~~

- 类型体操：自己实现一个 `Partialdfasd `

  ~~~typescript
  interface IPerson {
    name: string,
    age: number
  }
  
  // 映射类型
  type MyPartial<T> = {
    [K in keyof T]?:T[K]
  }
  
  type IPersonOptional = MyPartial<IPerson>
  ~~~

### `Required<T>`

- 用于构造一个 `T` 下面的所有属性全部设置为必填的类型，与 `Partial<T>` 相反

- 自己实现一个 `Required`

  ~~~typescript
  interface IPerson {
    name: string,
    age?: number
  }
  
  // 映射类型
  type MyRequired<T> = {
    [K in keyof T]-?:T[K]
  }
  
  type IPersonOptional = MyRequired<IPerson>
  ~~~

### `Readonly<T>`

- 用于构造一个 `T` 下面的所有属性都设置为只读的类型

### `Record<K, T>`

- 用于构造一个对象类型，它的所有键都是 `K` 类型，它的所有值都是 `T` 类型

- 自己实现一个 `Record`

  ~~~typescript
  type MyRecord<K extends keyof any, T> = {
    [Key in K]: T
  }
  
  type NewType = MyRecord<number, string>
  ~~~

### `Pick<T, K>`

- 用于构造一个对象类型，它从 `T` 类型里面挑选了一些 `K` 作为属性，`K` 为属性名

- 如果挑选多个属性，则 `K` 是这些属性名的联合类型

- 自己实现一个 `Pick`

  ~~~typescript
  interface IPerson {
    name: string,
    age: number,
    height: number
  }
  
  type MyPick<T, K extends keyof T> = {
    [Key in K]: T[Key]
  }
  
  type PickedIPerson = MyPick<IPerson, 'name' | 'age'>
  ~~~

### `Omit<T, K>`

- 用于构造一个对象类型，它从 `T` 类型里面去除了 `K` 以外的属性作为属性

- 自己实现一个 `Omit`

  ~~~typescript
  interface IPerson {
    name: string,
    age: number,
    height: number
  }
  
  // K 是传入的参数类型，Key 是 T 中被遍历的属性名
  type MyOmit<T, K extends keyof T> = {
    // 先将 Key in keyof T，即遍历时拿到的 Key，断言为 Key 本身，接着就可以对其进行条件类型判断
    // 使用条件类型判断 Key 是否满足传入的 K
    // - 如果满足，则被排除，返回 never
    // - 如果不满足，则不被排除，将 Key 返回，执行映射类型操作
    [Key in keyof T as Key extends K ? never : Key]: T[Key]
  }
  
  type PickedIPerson = MyOmit<IPerson, 'age' | 'height'>
  
  // 一些思考
  // - 上面代码中，在映射类型中的 `Key in keyof T as Key` 中，as Key 是将遍历时拿到的 Key 断言为了 Key 类型
  // - 而以下代码会报错
  // - let str: string = 'abc' as str
  // - 编辑器提示：“str”表示值，但在此处用作类型。是否指“类型 str”?ts(2749)
  // - 可见断言时只能使用类型，包括字面量类型，但如果尝试使用变量进行断言时，并不会将变量的值转变为字面量类型，只会将其当作值
  // - 而在 `Key in keyof T as Key` 中，Key 也是一个变量
  // - 但 Key 是一个类型变量，它代表一个键类型（也属于字符串字面量类型），因此可以用来做断言
  ~~~

### `Exclude<UnionType, ExcludedMembers>`

- 用于构造一个类型，该类型从 `UnionType` 中排除了所有符合 `ExcludedMembers` 的类型

- 自己实现一个 `Exclude`

  ~~~typescript
  // 利用分发条件类型，传入的联合类型 T 中的成员会分别被传入条件类型中处理，再组合成联合类型
  type MyExclude<T, E> = T extends E ? never : T
  
  type NewType = MyExclude<"a" | "b" | "c", "c">
  ~~~

- 利用 `Pick` 和 `Exclude`，可方便地实现一个自己的 Omit

  ~~~typescript
  interface IPerson {
    name: string,
    age: number,
    height: number
  }
  
  type MyOmit<T, K> = Pick<T, Exclude<keyof T, K>>
  
  type PickedIPerson = MyOmit<IPerson, 'age' | 'height'>
  ~~~

### `Extract<UnionType, UnionMembers>`

- 用于构造一个类型，该类型从 `UnionType` 中提取出所有符合 `UnionMembers` 的类型

- 自己实现一个 `Extract`

  ~~~typescript
  // 利用分发条件类型，传入的联合类型 T 中的成员会分别被传入条件类型中处理，再组合成联合类型
  type MyExtract<T, U> = T extends U ? T : never
  
  type NewType = MyExtract<"a" | "b" | "c" | 1, string>
  ~~~

### `NonNullable<T>`

- 用于构造一个类型，该类型从 `T` 中排除了所有 `null`、 `undefined` 的类型

- 自己实现一个 `NonNullable`

  ~~~typescript
  // 利用分发条件类型，传入的联合类型 T 中的成员会分别被传入条件类型中处理，再组合成联合类型
  type MyNonNullable<T> = T extends undefined | null ? never : T
  
  type NewType = MyNonNullable<"a" | undefined | null>
  ~~~

### `ReturnType<T>`

- 用于构造一个类型，该类型含有 `T` 函数类型的返回值类型

- 自己实现一个 `ReturnType`

  ~~~typescript
  type FnType = (arg1: number) => number
  
  // 在条件类型中推断
  type MyReturnType<T extends (...args: any[]) => any> = T extends (...args: any[]) => infer R ? R : never
  
  type FnReturnType = MyReturnType<FnType>
  ~~~

### `InstanceType<T>`

- 用于构造一个类型

- 传入的 `T` 是一个构造函数的类型

- 符合 `T` 类型的构造函数创建的实例的类型，就是 `InstanceType<T>` 要返回的类型

- 使用场景

  ~~~typescript
  class Person { }
  
  // 定义一个函数，该函数接收一个构造函数，并返回构造函数的调用结果
  function factory(ctor: new (...args: any[]) => any) {
    return new ctor()
  }
  
  factory(Person) // 返回类型被推断成 any 类型
  
  // 如何推断出具体的返回类型
  // 使用泛型和 InstanceType<T>
  function factory2<T extends new (...args: any[]) => any>(ctor: T):InstanceType<T> {
    return new ctor()
  }
  
  factory2(Person) // 返回类型被推断成 Person 类型
  ~~~

- 自己实现一个 `InstanceType`

  ~~~typescript
  type MyInstanceType<T extends new (...args: any[]) => any> = T extends new (...args: any[]) => infer I ? I : never
  ~~~

## 调试 `TS` 代码

- `VSCode` 中调试 `TS` 代码

  - 在 `launch.json` 中配置

    ~~~json
    {
      "version": "0.2.0",
      "configurations": [
        {
          "name": "Debug TS",
          "type": "node",
          "request": "launch",
          "runtimeArgs": [
            "-r",
            "ts-node/register"
          ],
          // "console": "integratedTerminal",
          "internalConsoleOptions": "openOnSessionStart",
          "skipFiles": [
            "<node_internals>/**",
            "${workspaceFolder}/node_modules/**"
          ],
          "program": "${workspaceFolder}/Stack/Stack.ts"
        }
      ]
    }
    ~~~

## 关键字

### `is`

- `is` 关键字是类型谓词（`Type predicate`），用于定义一种特殊类型的函数返回值

- 类型谓词的结构

  ~~~typescript
  function isOfType(arg: any): arg is T {
      // ...检查 arg 是否为 T...
  }
  ~~~

  - 上述 `isOfType` 函数的返回值类型被指定为 `arg is Type`，其中 `arg` 是参数名，`T` 是类型
  - 如果在条件分支的条件中使用了 `isOfType` 函数传入某个参数调用得到的返回值，那么如果通过了判断条件，则该分支中会将传入的那个参数的类型安全地缩小为 `T`

- 示例

  ~~~typescript
  function isString(test: any): test is string {
      return typeof test === 'string';
  }
  
  function example(foo: any) {
      if (isString(foo)) {
          console.log('it is a string', foo.length); // foo 在这里被 TS 推断为 string
      } else {
          console.log('it is not a string');
      }
  }
  ~~~

### `typeof`

- `JS` 和 `TS` 中都有 `typeof` 关键字，区别是
  - 在 `JS` 中，`typeof` 关键字返回一个字符串，表示操作数的类型
  - 在 `TS` 中
    - 如果使用 `const`、`let`、`var` 方式声明的变量接收 `typeof` 关键字的返回值，则接收到的是一个字符串，表示操作数的类型，此时 `typeof` 关键字的用法与在 `JS` 中相同
    - 如果使用 `type` 关键字声明的类型接收 `typeof` 关键字的返回值，则接收到的是一个 `TS` 类型定义
    - 注意，`TS` 中的 `typeof` 只能对数据进行转化，不能转化 `type` 或 `interface`

### `enum`

- `enum` 关键字用于定义枚举类型

- 枚举类型即可以当作类型使用，也可以当作数据使用，例

  ~~~typescript
  enum Day {
    SUNDAY,
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY
  }
  
  // Day 可以作为类型定义，此处 today 是一个 Day 类型的变量，它的值只能是 `Day.SUNDAY`、`Day.MONDAY ` 等 `Day` 枚举中定义的一个值
  let today: Day = Day.SUNDAY;
  
  // Day 同时也是数据，因此可以使用 typeof 关键字获取它作为数据的类型定义
  type DayType = typeof Day
  // 由于鸭子类型，可以将任意符合 DayType 定义的对象作为 DayCopy 的值
  const DayCopy: DayType = {
    // DayType 类型对象中必须包含 Day 枚举中定义的键名以及对应的正确的值
    SUNDAY: 0,
    MONDAY: 1,
    TUESDAY: 2,
    WEDNESDAY: 3,
    THURSDAY: 4,
    FRIDAY: 5,
    SATURDAY: 6, 
    // 另外，DayType 类型中实现了索引签名 [key: number]: string
    666: '这是一个字符串'
  }
  
  // 如果获取 Day 的类型字符串，会得到 'object'
  const DayTypeString = typeof Day
  console.log(DayTypeString) // 'object'
  ~~~

### `keyof`

- `keyof` 关键字作用在一个 `TS` 类型上 时，可映射出该类型的所有成员名称的联合类型
  - 如果作用在一个非对象类型上，如 `number`、`string`（不包括 `any`、`unknown`、`never`），则会返回其 `prototype` 上所有 `key` 组成的联合类型
  - 如果作用在 `any` 类型上，则表示所有可以作为键名的值的联合类型，等同于 `number | string | symbol`
  - 如果作用在 `unknown` 类型上，则会返回 `never`
  - 如果作用在 `never` 类型上，则会返回 `number | string | symbol`，相当于作用在 `any` 上

### `in`

- 在 `JS` 和 `TS` 中，`in` 关键字都可以用以：

  - 属性检查

    - 检查对象是否包含特定的键，与在 `JS` 中用法相同，例：

      ~~~typescript
      const obj = { a: 1, b: 2, c:3 }
      console.log('a' in obj) // true
      console.log('d' in obj) // false
      ~~~

  - 遍历属性

    - 遍历对象的属性，与在 `JS` 中用法相同，例：

      ~~~typescript
      const obj = { a: 1, b: 2, c:3 }
      for (let key in obj) {
        console.log(key)
      }
      ~~~

- 此外在 `TS` 中，`in` 关键字还可以用与**映射类型**

  - **类型映射**只能用于使用 `type` 关键字声明的对象类型中

  - 声明对象类型时，可以使用 `in` 关键字来遍历一个**联合类型**

    - 该**联合类型**的**成员**必须是**能用作键名的类型**，包括

      - `number`、`stirng`、`symbol`
      - 符合以上类型的字面量类型
      - `any` 类型被 `in` 关键字遍历时会被视为 `number | string | symbol`

    - 示例

      ~~~typescript
      type SomeKeys = 'first' | 'second' | number
      type MappedType = {
        [K in SomeKeys]: any
      } 
      type MappedTypeEquivalent = {
        first: any
        second: any
        [x: number]: any
      }
      // MappedType 等同于 MappedTypeEquivalent
      
      type AnyKeys = keyof any
      type MappedType1 = {
        [K in AnyKeys]: any
      }
      type MappedType2 = {
        [K in any]: any
      }
      type MappedType1Equivalent = {
        [x: string]: any
        [x: number]: any
        [x: symbol]: any
      }
      // MappedType1 等同于 MappedType2 等同于 MappedType1Equivalent
      
      type MappedType3 = {
        [K in keyof any]: any
      }
      type MappedType3Equivalent = {
        [x: string]: any
      }
      // MappedType3 等同于 MappedType3Equivalent
      // 注意虽然 MappedType1 和 MappedType3 在映射类型时，in 关键字都是作用与 keyof any，但是 TypeScript 对其解释的方式不同，直接使用 [K in keyof any] 时，只能映射 string 类型的索引签名
      ~~~

## 补充

### 实现一个能省略任意参数的函数示例

-  函数 `foo` 依次接收 `str`、`num`、`boo`、`arr` 四个参数，参数名对应了它们的参数类型

  - 其中 `num` 默认值为 `100`，`bool` 默认值为 `false`，`arr` 默认值为 `[666]`
  - 其中后三个参数都是可选参数，且省略的参数的位置可以被之后的参数占用
  - 利用重载签名实现

  ~~~typescript
  // 第一个重载签名覆盖了所有参数都存在，以及从最后一个参数开始往前连续省略的情况
  function foo(str: string, num?: number, bool?: boolean, arr?: any[]): void
  
  // 之后的重载签名都是关于有中间参数缺失的情况
  // 第 2、3、4 个参数都是可以省略的、其中省略 (2、3、4) | (3、4) | (4) 的情况已经被覆盖
  // 省略了 2、3
  function foo(str: string, arr: any[]): void
  // 省略了 2 或 2、4
  function foo(str: string, bool: boolean, arr?: any[]): void
  // 省略了 3
  function foo(str: string, num: number, arr: any[]): void
  
  function foo(
    str: string,
    o1?: number | typeof o2,
    o2?: boolean | typeof o3,
    o3?: any[]
  ) {
    // 定义实际要使用的变量
    let num: number = 100
    let bool: boolean = false
    let arr: any[] = [666]
  
    // 定义参数类型检测函数
    // 参数类型检测函数内部逻辑不一定要完全准确判断传入的值一定精确地属于某个类型，因为 foo 函数接收参数时已经通过重载签名对其进行过初步的类型检测，所以参数类型检测函数中只需判断其是否具有符合该参数应有的特征即可
    function isNum(arg: any): arg is number {
      return typeof arg === 'number'
    }
    function isBool(arg: any): arg is boolean {
      return typeof arg === 'boolean'
    }
    function isArr(arg: any): arg is any[] {
      return Array.isArray(arg)
    }
  
    // 参数匹配，判断接收的每个可选参数是否属于某个可能的类型，将其赋值给实际要使用的变量
    const optionals = [o1, o2, o3]
    optionals.forEach((optional) => {
      if (isArr(optional)) {
        arr = optional
      } else if (isBool(optional)) {
        bool = optional
      } else if (isNum(optional)) {
        num = optional
      }
    })
  
    console.log(
      'str:' + str + ';  num:',
      num + ';  bool:',
      bool + ';  arr:',
      arr
    )
  }
  ~~~


## 注意点

- 对于数组类型，使用索引访问其中元素时，`TS` 对其的类型推断始终基于该数组的类型，然而当读取不存在元素的索引时，其值为 `undefined`，可能造成编译时才发生的报错，需要注意手动添加类型注解或类型断言。

  同理，索引签名描述的类型，也要考虑 `undefined` 的可能性

## 拓展

### 分配律

分配律，在 `TS` 官网中被称为 [distributive-conditional-types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)。

如果将一个联合类型作为泛型参数输入条件类型时，只要该泛型参数直接出现在条件类型表达式的子类型上，条件类型就会呈现出分配律。简单地说就是条件类型的子类型中的 `T<A|B>` 会按照 `T<A> | T<B>` 的方式进行解释。例如：

~~~typescript
type Foo<T> = T extends number ? true : false;

type Bar = BelongToNumber<1 | string>; // Bar 等价于 true | false 等价于 boolean
~~~

如果是以下情况则会带来意外问题：

~~~typescript
type Foo<T> = T extends undefined ? undefined : (a: T) => T;

type Bar = Foo<number | string>; // Bar 等价于 ((a: string) => string) | ((a: number) => number)，而这种函数的联合类型和函数重载的处理方式不同，实际调用时会被解释成 (a: never) => string) 
~~~

如果要抑制条件类型的分配律，可以将条件中的泛型构造成其他类型，使得条件中不要直接出现联合类型：

~~~typescript
type Foo<T> = [T] extends [undefined] ? undefined : (a: T) => T;
// 或 type Foo<T> = T[] extends undefined[] ? undefined : (a: T) => T;

type Bar = Foo<number | string>; // Bar 等价于 (a: string | number) => string | number
~~~

## 拓展实战

### 联合类型转元组类型

[typescript - ts联合类型怎么转元组? - SegmentFault 思否](https://segmentfault.com/q/1010000042243980)

~~~typescript
type UnionToIntersection<U> = (U extends any ? (a: (k: U) => void) => void : never) extends (a: infer I) => void ? I : never;
type UnionLast<U> = UnionToIntersection<U> extends (a: infer I) => void ? I : never;
type UnionToTuple<U> = [U] extends [never] ? [] : [...UnionToTuple<Exclude<U, UnionLast<U>>>, UnionLast<U>];
~~~

~~~typescript
type UnionToIntersection<U> = (U extends any ? (k: U) => void : never) extends (
k: infer I
) => void
? I
: never;

type UnionToTuple<U> =
UnionToIntersection<U extends any ? () => U : never> extends () => infer W
? [...UnionToTuple<Exclude<U, W>>, W]
: [];
~~~
