# Proxy-Reflect

## 实现监听对象的方式

### ES5中的实现方式

- 利用ES5中的`Object.defineProperty`方法来对属性的操作进行监听

  ~~~javascript
  const obj = {
    name: '小明',
    age: 18,
    height: 188
  }
  
  const keys = Object.keys(obj).forEach(key => {
    let value = obj[key]
    Object.defineProperty(obj, key, {
      set(newVal) {
        console.log(`检测到了${key}被修改`)
        value = newVal
      },
      get() {
        console.log(`检测到了${key}被读取`)
        return value
      }
    })
  })
  ~~~

- 缺点

  - `Object.defineProperty`的设计初衷不是用来监听一个对象中的所有属性
  - 除了`读取`和`赋值`操作以外，如果想监听更丰富的操作，比如新增属性、删除属性，则无法通过`Object.defineProperty`实现

### ES6中的实现方式

- 在ES6中新增了一个`Proxy`类，用于创建一个`代理对象`

  - 如果希望监听一个对象的相关操作，可以先创建一个`Proxy对象`
  - 之后想对该对象进行的所有操作，都通过`Proxy对象`完成，`Proxy对象`可以监听想要对原对象进行的操作

- 实现方式

  1. new一个`Proxy对象`，传入需要监测的对象(`target`)以及一个处理对象(`handler`)
     - `const objProxy = new Proxy(target, handler)`
  2. 之后想对原对象的所有操作都是通过代理对象进行

- 示例

  ~~~javascript
  const obj = {
    name: '小明',
    age: 18,
    height: 188
  }
  
  const objProxy = new Proxy(obj, {
    set(target, property, value) {
      console.log(`监测到${property}被修改为${value}`)
      target[property] = value
      return value
    },
    get(target, property) {
      console.log(`检测到${property}被读取`)
      return target[property]
    }
  })
  ~~~
  
- `target`可以是一个函数对象

## Proxy的捕获器

- 如果要侦听某些具体的操作，那么需要在`handler`中添加对应的捕获器(`Trap`)函数
- 当对代理对象进行操作时
  - 如果在`handler`中配置了对应操作的`Trap`，则操作会被拦截，并执行对应`Trap`中的代码
  - 如果没有配置对应操作的`Trap`，修改操作会直接同步给代理对象

- 当对原对象进行修改时，代理对象也会同步被修改，但不会触发捕获器
- 捕获器的使用
  - 利用捕获器中传入的参数，在捕获器函数中对`target`进行与`捕获到的对代理对象的操作`相同的操作
  - 如果捕获到的操作应有返回值，则需要在捕获器函数中将其`return`

### 常用捕获器

#### set

- 属性设置（增/改）操作的捕获器
- 参数
  - `target` 目标对象（侦听的对象）
  - `property` 将被设置的属性key
  - `value` 新属性值
  - `receiver` 调用的代理对象，通常是proxy本身，但handler的set方法也有可能在原型链上，或以其他方式被间接地调用（因此不一定是proxy本身）

#### get

- 属性读取操作的捕获器
- 参数
  - `target` 目标对象（侦听的对象）
  - `property` 将被设置的属性key
  - `receiver` 调用的代理对象

#### deleteProperty

- `delete`操作符的捕获器
- 参数
  - `target` 目标对象（侦听的对象）
  - `property` 将被删除的属性key

#### has

- `in`操作符的捕获器
- 参数
  - `target` 目标对象（侦听的对象）
  - `property` 需要检查是否存在的属性

#### apply

- 函数调用操作的捕获器，包括直接调用，`call`调用，`apply`调用
- 参数
  - `target` 目标对象(侦听的函数对象)
  - `thisArg` 被调用时的上下文对象
  - `argumentsList` 被调用时的参数数组

#### construct

- `new`操作符的捕获器
- 参数
  - `target` 目标对象（侦听的对象）
  - `argumentsList` constructor的参数列表
  - `newTarget`
    - 作为新创建对象的原型对象的`constructor`属性
    - 如果不传`newTarget`，则它默认就是`target`
    - 简单理解：如果传了`newTarget`，那么`target`就成为了`newTarget`的父类，并且这个方法创建出来的是`newTarget`的实例

## 其他捕捉器

- 见[Proxy() 构造器 - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy)

## Reflect

### Reflect的作用

- `Reflect`是ES6新增的一个内置对象
- `Reflect`提供了很多操作JS对象的方法，大部分类似`Object`中操作对象的方法
- 为什么需要`Reflect`对象
  - 早期ECMA规范中没有考虑到对对象本身的操作如何设计会更规范，将一些操作对象的API放在了`Object`上
  - `Object`作为一个构造函数，拖带这么多操作对象的API并不合适
  - 另外包含一些`in`、`delete`之类的操作符，与`Object`上的操作对象的方法很不统一，使得JS看起来很奇怪
  - 在ES6中将操作对象的API集中到了新增的`Reflect`对象上
- `Object`与`Reflect`对象之间的API关系
  - [比较 Reflect 和 Object 方法 - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/Comparing_Reflect_and_Object_methods)

### Reflect的常见方法

- `Reflect`中的常见方法与`Proxy`中的捕获器一一对应

  - Reflect.set(target, property, value[, receiver])
  
    - 将值分配给对象身上的某个属性，根据是否更新成功来返回布尔值
  
  - Reflect.get(target, property[, receiver])
  
    - 获取对象身上的某个属性，类似于`target[name]`
  
  - Reflect.deleteProperty(target, property)
  
    - 同`delete target[name]`
  
  - Reflect.has(target, property)
    - 同`property in target`
    
  - Reflect.getPrototypeOf(target)
    
    - 获取指定对象的原型
    
    - 类似于`Object.getPrototypeOf()`
    
  - Reflect.setPrototypeOf(target, prototype)
  
    - 设置或改变对象的原型
  
    - 设置对象原型，根据是否更新成功来返回布尔值
  
  - Reflect.isExtensible(target)
  
    - 判断一个对象是否可扩展（即是否能够添加新的属性）
  
    - 类似于`Object.isExtensible()`
  
  - Reflect.preventExtensions(target)
  
    - 阻止新属性添加到对象
  
    - 类似于`Object.preventExtensions()`，返回布尔值
  
  - Reflect.getOwnPropertyDescriptor(target, property)
  
    - 类似于`Object.getOwnPropertyDescriptor`
    - 如果`target`中存在该`property`，则返回对应的属性描述符，否则返回`undefined`
  
  - Reflect.defineProperty(target, property, descriptor)
  
    - 定义或修改一个对象的属性，如果设置成功会返回`true`
  
    - 类似于`Object.defineProperty()`
  
  - Reflect.ownKeys(target)
  
    - 返回一个包含所有自身属性（不包含继承属性）的数组
    - 类似于`Object.keys()`，但不会受`enumerable`影响
  
  - Reflect.apply(target, thisArgument, argumentsList)
  
    - 调用一个对象
  
    - 类似于`Function.prototype.apply()`
  
  - Reflect.construct(target, argumentsList[, newTarget])
  
    - 对构造函数进行`new`操作，实现创建类的实例
  
    - 类似于`new target()`

### Reflect的使用

- 在`Proxy`的捕获器函数中对`target`的操作，可以使用`Reflect`来操作

  ~~~javascript
  const obj = {
    name: '小明',
    age: 18,
    height: 188
  }
  
  const objProxy = new Proxy(obj, {
    set(target, property, value) {
      console.log(`监测到${property}被修改为${value}`)
      return Reflect.set(target, property, value)
    },
    get(target, property) {
      console.log(`检测到${property}被读取`)
      return Reflect.get(target, property)
    }
  })
  ~~~
