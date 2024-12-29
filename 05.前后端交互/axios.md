# `axios`

## `HTTP` 相关

#### 请求方式与请求参数

##### 请求方式

- `GET` ----- 查(`Read`)

  从服务器端读取数据

- `POST` ----- 增(`Create`)

  向服务器端添加新数据

- `PUT` ----- 改(`Update`)

  更新服务器端已存在的数据

- `DELETE` ----- 删(`Delete`)

  删除服务器端数据

##### 请求参数

- `query` 参数（查询字符串参数）
  - 参数包含在请求地址中，格式如：/xxx?name=tom&age=18
  - 安全性较低，敏感参数不要使用query参数
  - query参数又称查询字符串参数，编码方式为urlencoded
- `params` 参数
  - 参数包含在请求地址中，格式如：/xxx/tom/18
  - 安全性较低，敏感参数不要使用params参数
- 请求体参数
  - 参数包含在请求体中，可通过浏览器开发工具或抓包工具查看
  - 常用的两种格式：
    1. `urlencoded` 格式
       - 如：`name=tom&age=18`
       - 对应请求头：`Content-Type: application/x-www-form-urlencoded`
    2. `json` 格式
       - 如：`{ "name": "tom", "age", 12 }`
       - 对应请求头：`Content-Type: application/json`
- 注意：
  - `GET` 请求不能携带请求体参数，因为 `GET` 请求没有请求体
  - 理论除了 `GET` 之外的请求，可以随意使用上述三种类型参数种的任何一种，甚至可以同时使用，但一般不这样做
  - 约定俗成的规定：
    - `form` 表单发送 `post` 请求时，自动使用请求体参数，使用 `urlencoded` 编码
    - `jQuery` 发送 `ajax-pos`t 请求时，自动使用请求体参数，使用 `urlencoded` 编码
  - 开发种请求到底发送给谁，使用的请求方式和携带的参数，要参考品项目的 `API` 接口文档

## `API` 相关

#### `API` 分类

##### `REST API`（`restful` 风格的 `API`）

- 发送请求进行 `CRUD` 的哪种操作由请求方式决定
- 同一个请求路径可以进行多种操作
- 请求方式会用到`GET/POST/PUT/DELETE`

##### 非 `REST API`（ `restless` 风格的 `API`）

- 请求方式不决定请求进行 `CRUD` 的哪种操作
- 一个请求路径只对应一种操作
- 请求方式一般只用到 `GET` 和 `POST`

#### 快速搭建 `REST API`

##### `json-server`

- 用来快速搭建 `REST API` 的工具包
- 在线文档：https://github.com/typicode/json-server
- 下载安装：`npm i json-server -g`
- 启动命令行执行命令：`json-server db.json`


##### `api-doc`

- 可快速生成 `api` 接口文档

#### 一般 `http` 请求与 `ajax` 请求

- `ajax` 请求是一种特别的 `http` 请求
- 对服务器端来说，没有任何区别
- 对于浏览器端，只有 `xhr` 或 `fetch` 发出的才是 `ajax` 请求，其他的都是非 `ajax` 请求
- 浏览器端收到响应
  1. 一般请求：浏览器一般会自动刷新页面，并直接显示响应体数据，
  2. `ajax` 请求：浏览器不会刷新页面，只是调用监视的回调函数并传入传入响应相关数据

## `axios` 的理解和使用

### `axios` 是什么

- 前端最流行的 `ajax` 请求库
- `react/vue` 官方都推荐使用 `axios` 发送 `ajax` 请求
- 文档：https://github.com/axios/axios

### `axios` 特点

- 基于` promise` 的异步 `ajax` 请求库
- 浏览器端 / `node` 端都可以使用
- 支持请求/响应拦截器
- 支持请求取消
- 请求/响应数据转换
- 批量发送多个请求

### `axios` 的使用

#### 发送请求

##### `axios(config)`

- 可发任意类型的请求

~~~javascript
axios({
    url:'http://localhost:5000/persons', // 请求地址
    method:'POST', // 请求方式
    params: {id: 1}, // 携带的query参数，注意不是指params参数
    data:{name:tom, age:18}, // 配置携带请求体参数，json编码
    data:'name=tom&age=18', // 配置携带请求体参数，urlencoded编码
    timeout: 2000, // 配置超时时间
    headers: {name: value} // 配置请求头
    responseType: 'json' // 配置响应数据的格式
})
~~~

- `axios(config)` 函数调用后，返回值是一个 `promise` 实例
- 使用返回的 `promise` 实例调用 `then()`，成功的值一般命名为 `response`，失败的值一般命名为 `error`
- 成功的值是一个 `axios` 封装的 `response` 对象，服务器返回的数据在 `response.data` 中

##### axios.get(url[, config])

- 发送GET请求

~~~javascript
axios.get('http://localhost:5000/person', {
    params: {id: 1}
})
~~~

##### axios.post(url[,data ,config])

- 发送POST请求

~~~javascript
axios.post('http://localhost:5000/person', {name:tom, age:18}) // 携带json编码请求体
axios.post('http://localhost:5000/person', 'name=tom&age=18') // 携带urlencoded编码请求体
~~~

##### axios.put(url[,data ,config])

- 发送PUT请求

##### axios.delete(url[,data ,config])

- 发送DELETE请求

#### 请求的默认全局配置

##### axios.defaults

~~~javascript
axios.defaults.timeout = 2000 // 设置请求的默认超时时间为2s
axios.defaults.baseURL = 'http://localhost:5000' // 设置默认的服务器地址
~~~

#### 创建新的axios

##### axios.create([config])

- 创建一个新的axios，每个新的axios都有自己的配置
- 创建时传入的配置，会被写入新的axios的defaults对象中
- 新的axios不能使用取消请求和批量发送请求的方法，其余语法是一致的

~~~javascript
const axios2 = axios.create({
    timeout: 3000
    baseURL:'http://api.apiopen.top'
})
~~~

#### 请求/响应拦截器

- 调用axios()后，ajax请求发送的流程：
  1. ajax请求经过请求拦截器处理
  2. 请求拦截器处理后的ajax请求发送到服务器
  3. 服务器的响应经过响应拦截器
  4. 响应拦截器得到响应之后执行成功或失败的回调函数

##### axios.interceptors.request.use()

- 在请求发送出去之前执行一个回调函数
- 可根据情况对请求作处理，如追加请求头、追加参数、界面loading提示等
- 如果设置多个请求拦截器，则请求会先经过后配置的请求拦截器

~~~javascript
axios.interceptors.request.use((config) => {
    /* 对请求配置进行的处理 */
    return config // 需要将处理后的请求的配置返回
})
~~~

##### axios.interceptors.response.use()

- 得到响应后执行成功或失败的回调函数

~~~javascript
axios.interceptors.response.use(
    response => {
        /* 对成功的响应进行处理 */
        return response.data // 将response.data作为axios()返回的promise的value
    },
    error => {
        /* 对失败的响应进行处理 */
        return new Promise(() => {}) // 将一个始终处于pending状态的promise作为axios()的返回值 
    }
)
~~~

- 响应拦截器的成功或失败的回调执行后，会返回一个新的promise实例作为axios()的返回值
  - 如果回调的返回值为一个非promise值，则新promise的状态为fulfilled，value为该返回值
  - 如果回调的返回值为一个promise实例，则新promise的结果状态与该promise相同
- 根据以上特性，可对响应拦截器进行如下配置
  - 将成功的回调的返回值设置为response.data，使得可以直接通过axios()的value拿到响应体
  - 将响应失败的处理直接写在响应拦截器的失败的回调中，将其返回值设置为一个始终处于pending状态的promise，使得axios()不会被then()调用，无需再配置axios()的返回值失败的回调

#### 取消请求

##### axios.CancelToken

- 用于创建取消请求的token对象，是一个构造函数

~~~javascript
const { CancelToken } = axios // 通过解构赋值，将axios.CancelToken赋值给CancelToken
let cancel // * 声明一个cancel变量，用以存储取消请求的函数 *
axios.get('http://localhost:5000/test1?delay=3000', {
    cancelToken: new CancelToken((c) => {
        cancel = c // c就是取消请求的函数
    })
})
cancel() // 取消请求，括号中可写错误信息
~~~

##### axios.isCancel(error)

- 判断error是否是一个取消请求的错误，返回布尔值

##### 通过拦截器配置取消请求

- 实现功能：
  - 点击按钮，取消某个正在请求中的请求
  - 在请求一个接口前，取消前一个未完成的请求

~~~javascript
const btn1 = document.querySelector('#btn1') // btn1用于发送请求
const btn2 = document.querySelector('#btn2') // btn2用于取消请求
const { CancelToken } = axios // 通过解构赋值，将axios.CancelToken赋值给CancelToken
let cancel // * 声明一个cancel变量，用以存储取消请求的函数 *

// 配置请求拦截器
axios.interceptors.request.use((config) => {
    if (cancel) cancel('一个新的请求发出') // * 发送请求时，将上一次发送的请求取消 *
    config.baseURL = 'http://localhost:5000'
    config.cancelToken = new CancelToken(c => {cancel = c}) // * 在请求中配置cancelToken *
    return config
})

// 配置响应拦截器
axios.interceptors.response.use(
    response => response.data,
    error => {
        if (axios.isCancel(error)) {
            console.log('请求被取消，原因是：', error.message) // * 如果是取消请求导致的错误，则返回error.message *
        } else {
            console.log('请求失败，', error)
        }
        return new Promise(() => { })
    }
)

btn1.onclick = async () => {
    const response = await axios.get('/test1?delay=3000')
    console.log(response)
}

btn2.onclick = () => {
    if (cancel) cancel('用户取消了请求') // * 调用取消请求的函数，参数为取消请求后的error.message *
}
~~~

#### 批量执行多个异步请求

##### axios.all(promise)

- 与Promise.all()的使用逻辑基本一致

~~~javascript
btn1.onclick = async () => {
    const response = await axios.all([
        axios.get('/test1?delay=3000'),
        axios.get('/test2?delay=3000'),
        axios.get('/test3?delay=3000')
    ])
    console.log(response)
}
~~~
