## Promise

## 相关

#### 实例对象与函数对象

- 实例对象
  - new构造函数产生的对象，称之为实例对象
- 函数对象
  - 将函数作为对象使用时，称之为函数对象
  - 函数对象的name属性值为该函数名，且为只读，不可修改

#### 回调函数

- 同步的回调函数
  - 立即在主线程上执行，不会进入回调队列中
  - 如：数组遍历相关的回调函数，Promise的executor函数
- 异步的回调函数
  - 不会立即执行，会进入回调队列，等主线程执行完毕后执行
  - 如：定时器回调，ajax回调，Promise的成功、失败回调

#### JS的error处理

##### 错误的类型

- Error

  所有错误的父类型

- ReferenceError

  引用的变量不存在

- TypeError

  数据类型不正确

- RangeError

  数据值不再其所允许的范围内（无线递归的情况）

- SyntaxError

  语法错误

更多错误类型[Error - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Error)

##### 错误处理

- 捕获错误

  ~~~javascript
  // try中执行可能出现错误的代码，一旦出错，立即执行try中的代码，调用catch，并携带错误信息
  try {
      console.log(1)
      console.log(a)
      console.log(3)
  } catch (error) {
      console.log('代码执行出错，错误原因：' + error)
  }
  ~~~

- 抛出错误

  ~~~javascript
  // 当时间戳为奇数时抛出一个错误
  function demo () {
      const date = Date.now
      if (date % 2 === 0) {
          console.log('偶数')
      } else {
          throw new Error('奇数')
      }
  }
  // 捕获抛出的错误
  try {
      demo()
  } catch (error) {
      console.log('error:' + error)
  }
  ~~~

##### error对象的结构

- message属性：错误相关信息
- stack属性：记录错误相关信息，以及错误所在的位置

## Promise的理解

#### 理解

- 抽象表达
  - Promise是ES6提出的，在JS中进行异步编程的新方案（旧方案是单纯使用回调函数）
- 具体表达
  - 从语法上来说，Promise是一个构造函数
  - 从功能上来说，Promise的实例对象可以用来封装一个异步操作，并可以获取其成功或失败的值（用来管理异步任务）

#### Promise实例对象的状态

- 每一个Promise实例对象都有三种状态，分别为
  - pending 初始化
  - fulfilled 成功
  - rejected 失败
- 当Promise实例对象创建时，初始状态为pending，根据异步任务是否成功而转变为fulfilled或rejected，且只可转变一次
- 成功的结果数据一般称为value，失败的结果数据一般成为reason

## Promise的使用

#### Promise构造函数

- 在创建promise实例对象时，往Promise构造函数中传入一个executor函数，executor函数会立即同步回调，异步的代码放在executor函数内部
- executor函数的第一个参数是resolve函数，第二个参数是reject函数
- 在executor函数中启动一个异步任务（定时器、ajax请求）
- 根据异步任务的结果，做不同处理
  - 如果异步任务成功了，则调用resolve(value)，会让promise实例对象的状态变为fulfilled，同时指定成功的value
  - 如果异步任务失败了，则调用reject(reason)，会让promise实例对象的状态转变为rejected，同时指定失败的reason
  - promise实例对象的状态只可转变一次，之后再次调用resolve(value)或reject(reason)则不会再生效

- 基本使用

  ~~~javascript
  // 创建Promise实例对象
  const p = new Promise((resolve, reject) => {
      // 在executor内部启动一个异步任务
      ......
      // 分支语句中，当异步任务成功的情况下
      resolve(xxx) // xxx会赋值给p.then()中成功的回调中的参数value
      ......
      // 分支语句中，当异步任务失败的情况下
      reject(yyy) // yyy会赋值给p.then()中失败的回调中的参数reason
      ......
  })
  
  // 使用Promise实例对象，配置成功与失败的回调
  p.then(
      // 成功的回调函数
      value => console.log(value),
      // 失败的回调函数
      reason => console.log(reason)
  )
  ~~~

#### APIs

##### Promise.prototype.then()

- 第一个参数为onFulfilled函数，即成功的回调函数：value => {}
- 第二个参数为onRejected函数，即失败的回调函数：reason => {}
- 当Promise实例对象的状态完成转变时，会自行调用then()中对应的回调函数
- 一个promise实例可以调用多次then以指定多个回调函数，当promise完成状态转变时，对应的回调函数都会被调用
- then()方法会返回一个新的promise实例对象
  - 返回的新promise实例的结果状态由then()指定的回调函数执行的结果决定
    - 如果then()指定的回调函数执行时抛出异常，则新promise的结果状态为rejected，reason为抛出的异常
    - 如果then()指定的回调函数的返回值是一个非promise的值，则新promise的结果状态为fulfilled，value为该返回值
    - 如果then()指定的回调函数的返回值是一个promise实例，该promise的结果就是新promise的结果
    - 如果then()指定的回调函数没有被定义
      - 简单理解：新promise的结果状态和调用这个then()的promise的状态相同
      - 原理：
        - 当promise状态转变为rejected时，onRejected回调函数没有被定义时，js底层相当于定义了onRejected回调的内容为 'throw reason' ，抛出了一个异常，因此then()返回的新promise结果状态也为rejected，reason也相同
      - 可根据此特性实现错误穿透
    - 如果原本的promise实例状态变为了rejected，那么返回的新promise的结果状态为rejected，reason同原本的promise实例
  - then()的返回值可以和then()形成链式调用，可用来解决回调地狱问题
    - 当then的返回值为一个状态一直是pending的promise时，Promise链中的下一个then()就不会被调用，实现中断Promise链
    - 在Promise链中，如果不指定then()中的的onRejected函数，在Promise链的最后接上catch()指定onRejected函数，可以接收链中出现失败的reason（错误穿透）

##### Promise.prototype.catch()

- 参数为onRejected函数，即失败的回调函数：reason => {}
- 是then()的语法糖，相当于：then(undefined, onRejected)

##### Promise.resolve()

- Promise.resolve(value)
- 作用是直接返回一个promise实例对象
- value传入成功的数据或一个promise实例
  - 当value传入的值不是一个promise实例对象时，返回一个状态为fulfilled的promise实例对象
  - 当value传入的值是一个promise实例对象时，返回一个状态以及value或reason与之相同的promise实例对象，且其状态会随着传入的promise实例对象同时转变

##### Promise.reject()

- Promise.resolve(reason)
- 作用是直接返回一个状态为rejected的promise实例
- reason传入失败的原因

##### Promise.all()

- Promise.all(promiseArr)
- 传入一个包含n个Promise实例的数组
- 返回一个新的promise实例
- 当promiseArr中的所有promise结果状态为fulfilled时，返回的promise实例结果状态为fulfilled，其value为promiseArr中所有promise中的value组成的数组
  - 返回的数组中value的顺序由传入的数组中Promise实例的顺序决定，不受状态转变先后顺序影响
- 当promiseArr中有一个promise结果状态为rejected时，返回的promise实例结果状态为rejected，其reason为promiseArr中第一个状态转变为rejected的promise中的reason

##### Promise.race()

- Promise.race(promiseArr)
- 传入一个包含n个Promise实例的数组
- 返回一个新的promise实例
- 当promiseArr中出现第一个完成结果状态的promise时，返回的promise的结果状态以及value或reason与之相同

#### 如何改变一个promise的状态

- 当一个promise当前状态是pendding时
  - executor内执行resolve(value)，则会转变为fulfilled
  - executor内执行reject(reason)，则会转变为rejected
  - executor抛出异常，则会转变为rejected

#### promise的状态转变和指定回调函数的先后顺序

- 谁先谁后都有可能
  - 如果先指定回调，那么当状态发生改变时，任务队列里的回调函数就会被调用
  - 如果先改变状态，那么当指定回调时，回调函数进入任务队列后就可以被调用

#### Promise的应用

##### 使用Promise管理Ajax请求

~~~javascript
// 创建Promise实例对象
const p = new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.onreadystatechange = () => {
        if (xhr.readyState === 4) {
            if (xhr.status >= 200 && xhr.status < 300) {
                // 接收到正确响应状态码时，调用resolve()并接收响应体
                resolve(xhr.response)
            } else {
                // 接收到非正确响应状态码时，调用reject()
                reject('请求出错')
            }
        }
    }
    xhr.open('GET', 'http://127.0.0.1:8080/cors')
    // xhr.responseType = 'json'
    xhr.send()
})

// Promise实例对象调用then方法
p.then(
    // 成功的回调函数
    value => console.log(value),
    // 失败的回调函数
    reason => console.log(reason)
)
~~~

##### 使用Promise解决回调地狱问题

~~~javascript
/*
需求：当前一次get请求发送成功后，再发送下一次get请求
*/

/*
定义一个sendAjax函数，对xhr的get请求进行封装：
    1.该函数接收两个参数：url（请求地址）、data（参数对象）、index（第几次发送请求）
    2.该函数返回一个Promise实例
        - 若ajax请求成功，则Promise实例成功，成功的value是返回的数据
        - 若ajax请求失败，则Promise实例失败，失败的reason是错误提示
*/
function sendAjax(url, data, index) {
    // 创建Promise实例对象
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
            if (xhr.readyState === 4) {
                if (xhr.status >= 200 && xhr.status < 300) {
                    // 接收到正确响应状态码时，调用resolve()并接收响应体
                    resolve(xhr.response)
                } else {
                    // 接收到非正确响应状态码时，调用reject()
                    reject(`第${index}次请求出错`)
                }
            }
        }
        // 将data参数对象转换为查询字符串
        let str = ''
        for (let key in data) {
            str += `${key}=${data[key]}&`
        }
        xhr.open('GET', url + '?' + str.slice(0, -1))
        // xhr.responseType = 'json'
        xhr.send()
    })
}

/*
利用then()的返回值进行链式调用
*/
sendAjax('http://127.0.0.1:8080/cors', { page: 1 }, 1)
    .then(
        value => {
            console.log('第1次请求成功，', value)
            return sendAjax('http://127.0.0.1:8080/cors', { page: 2 }, 2)
        }/* ,
        // onRejected函数可返回一个状态始终为pendding的promise以中断Promise链
        // 但为了方便可不指定onRejected，使用错误穿透在Promise链最后接收reason
        reason => {
            console.log(reason)
            return new Promise(() => { })
        } */
    )
    .then(
        value => {
            console.log('第2次请求成功，', value)
            return sendAjax('http://127.0.0.1:8080/cors', { page: 3 }, 3)
        }/* ,
        reason => {
            console.log(reason)
            return new Promise(() => { })
        } */
    )
    .then(
        value => {
            console.log('第3次请求成功，', value)
        }/* ,
        reason => {
            console.log(reason)
        } */
    )
    // 错误穿透
    .catch(
        reason => { console.log(reason) }
    )
~~~

## async与await

#### async函数

- async函数的返回值为一个promise对象
- 返回的promise对象的结果由async函数执行的返回值决定
  - 如果执行的返回值为非promise值，则async函数返回的peomise状态为fulfilled，value为该返回值
  - 如果执行的返回值为一个promise对象，则async函数返回的peomise的结果与之相同

#### await表达式

- await右侧的表达式一般为promise对象，也可以是其他值，但这样使用没有意义
- 如果表达式是promise对象
  - 如果promise对象结果为fulfilled，则await返回value
  - 如果promise对象结果为rejected，则会报错并抛出reason
- 如果表达式是非promise值，则await直接返回该值

#### async与await的使用

- await必须写在async函数中，但async函数中可以没有await
- 如果await的promise状态为rejected，就会报错并抛出reason，需通过try...catch捕获处理
- await一个promise，相当于使用该promise调用了then()，且在async函数中，await表达式之后的代码相当于写在then()的onFulfilled函数中

##### 使用async与await解决回调地狱问题

~~~javascript
/*
需求：当前一次get请求发送成功后，再发送下一次get请求
*/

/*
定义一个sendAjax函数，对xhr的get请求进行封装：
    1.该函数接收两个参数：url（请求地址）、data（参数对象）、index（第几次发送请求）
    2.该函数返回一个Promise实例
        - 若ajax请求成功，则Promise实例成功，成功的value是返回的数据
        - 若ajax请求失败，则Promise实例失败，失败的reason是错误提示
*/
function sendAjax(url, data, index) {
    // 创建Promise实例对象
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
            if (xhr.readyState === 4) {
                if (xhr.status >= 200 && xhr.status < 300) {
                    // 接收到正确响应状态码时，调用resolve()并接收响应体
                    resolve(xhr.response)
                } else {
                    // 接收到非正确响应状态码时，调用reject()
                    reject(`第${index}次请求出错`)
                }
            }
        }
        // 将data参数对象转换为查询字符串
        let str = ''
        for (let key in data) {
            str += `${key}=${data[key]}&`
        }
        xhr.open('GET', url + '?' + str.slice(0, -1))
        // xhr.responseType = 'json'
        xhr.send()
    })
};

(async () => {
    try {
        const value1 = await sendAjax('http://127.0.0.1:8080/cors', { page: 1 }, 1)
        console.log(value1)
        const value2 = await sendAjax('http://127.0.0.1:8080/cors', { page: 2 }, 2)
        console.log(value2)
        const value3 = await sendAjax('http://127.0.0.1:8080/cors', { page: 3 }, 3)
        console.log(value3)
    } catch (reason) {
        console.log(reason)
    }
})()
~~~

## 宏队列与微队列

#### 原理图

![](.\images\宏队列与微队列.png)

#### 说明

- JS中用来存储执行回调函数的队列包含宏队列与微队列

  - 宏队列：

    用来保存待执行的宏任务（回调），如：定时器回调、DOM事件回调、Ajax回调

  - 微队列：

    用来保存待执行的微任务（回调），如：promise的回调、MutationObserver的回调

- JS执行时会区别这两个队列

  - JS引擎首先必须先执行所有的初始化同步任务代码
  - 每次准备取出第一个宏任务执行前,都要将所有的微任务一个一个取出来执行

##### 关于then()里的回调函数

- promise.then()中的onFulfilled函数和onRejected函数，只有当promise的结果状态确定后，才会使对应的回调函数进入微队列，否则一直挂载在promise上
- 如果promise.then()的回调函数在被指定时，promise的结果状态已经确定，则对应的回调函数会直接进入微队列，不会挂载在promise上