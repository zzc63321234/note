# AJAX

## AJAX简介

- AJAX(Asynchronous JavaScript And XML)，即异步的JS和XML
- 通过AJAX可以在浏览器中向服务器发送异步请求，使得页面无刷新即可获取数据
- AJAX不是新的编程语言，而是一种将现有的标准组合在一起使用的新方式

## XML简介

- XML(Extensible Markup Language)，即可扩展标记语言

- XML被设计用来传输和存储数据

- XML和HTML类似，都是标记语言，不同的是HTML中都是预定义标签，而XML中全是自定义标签，用来表示一些数据

- 用XML表示要一个学生的数据

  ~~~xml
  <student>
      <name>孙悟空</name>
      <age>1000</age>
      <gender>男</gender>
  </student>
  ~~~

  现在这种数据方法已经被JSON取代

  ~~~json
  {"name": "孙悟空", "age": "1000", "gender": "男"}
  ~~~

## AJAX的特点

- 优点
  - 可以无需刷新页面而与服务器端进行通信
  - 允许根据用户事件来更新部分页面内容
- 缺点
  - 没有浏览历史，不能回退
  - 存在跨域问题
  - 对SEO不友好

## AJAX的使用

#### 核心对象

- XMLHttpRequest
  - AJAX的所有操作都是通过该对象进行的

#### 使用步骤

~~~javascript
// 在页面中使用该js文档
// 1. 创建XMLHttpRequest实例对象
const xhr = new XMLHttpRequest()

// 2. 设置请求信息
xhr.open(method, url) // 配置请求
xhr.setRequestHeader(key, value) // 设置请求头（可选）

// 3. 发送请求
xhr.send(body) // get请求不传body参数

// 4. 接收响应
xhr.onreadystatechange = () => {
    if(xhr.readtState === 4 && xhr.status >= 200 && xhr.status < 300){
        xhr.responseType = 'json' // 指定返回数据的格式是json
        console.log(xhr.response) // 响应体在server.js路由中配置
    }
}
~~~

#### 使用AJAX向服务器发送参数

- 发送GET请求可在url中携带params参数和urlencoded参数
- 发送POST请求可携带请求体


- 发送urlencoded格式请求体

  ~~~javascript
  // 在页面中使用该js文档
  // 1.创建XMLHttpRequest实例对象
  const xhr = new XMLHttpRequest()
  
  // 2.设置请求信息
  xhr.open('POST', '/ajax-post') // 配置请求
  xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded') // 在请求头中设置请求体格式
  
  // 3.发送请求
  xhr.send('name=value') // 带请求体发送
  
  // 4. 接收响应
  xhr.onreadystatechange = () => {
      if(xhr.readtState === 4 && xhr.status >= 200 && xhr.status < 300){
          console.log(xhr.response)
      }
  }
  
  // 服务端js代码中需设置app.use(express.urlencoded({extended:false}))来解析urlencoded格式请求体
  ~~~


- 发送json格式请求体

  ~~~javascript
  // 在页面中使用该js文档
  // 1.创建XMLHttpRequest实例对象
  const xhr = new XMLHttpRequest()
  
  // 2.设置请求信息
  xhr.open('POST', '/ajax-post') // 配置请求
  xhr.setRequestHeader('Content-type', 'application/json') // 在请求头中设置请求体格式
  
  // 3.发送请求
  const person = "{"namne": "value"}"
  xhr.send(person) // 带请求体发送
  
  // 4. 接收响应
  xhr.onreadystatechange = () => {
      if(xhr.readtState === 4 && xhr.status >= 200 && xhr.status < 300){
          console.log(xhr.response)
      }
  }
  
  // 服务端js代码中需设置app.use(express.json())来解析urlencoded格式请求体
  ~~~


####  AJAX请求状态

- xhr.readyState可以用来查看请求当前的状态

  - 0：表示XMLHttpRequest实例已经生成，但open未调用

  - 1：open已调用，但send还未调用，此时仍然可以修改请求头信息

    > xhr.setRequestHeader(key, value) //可用来设置请求头

  - 2：表示send()方法已经执行，并且响应头信息和状态码已经收到

    > xhr.getResponseHeader(key) //  获取指定的响应头
    >
    > xhr.getAllResponseHeaders() // 获取所有的响应头

  - 3：表示正在接收服务器传来的部分数据，小的数据会在次阶段一次性接收完毕，较大的数据有待进一步接收

  - 4：表示数据已经接收完毕

#### IE浏览器缓存问题

- 问题：在IE中，ajax向服务器发送过一次GET请求后，之后的相同的GET请求不会再发给服务器，而是直接加载缓存中的数据

- 解决方式：浏览器的ajax缓存是根据url地址来记录的，只要保证每次请求的url地址不同，即可避免缓存问题

  ~~~javascript
  xhr.open('GET', 'url/t='+Date.now())
  ~~~

#### 异常处理与超时处理

- 异常处理

  ~~~javascript
  // 设置请求异常时的回调
  xhr.onerror = () => console.log('请求出错')
  ~~~

- 超时处理

  ~~~ javascript
  // 设置超时判断时间，超时后不再接收服务器对本次请求的任何响应
  xhr.timeout = 2000

  // 设置超时的回调
  xhr.ontimeout = () => console.log('请求超时')
  ~~~

- 主动关闭请求

  ~~~javascript
  // 如果请求没有到达服务器，则取消请求；如果请求已经到达服务器，则不接收响应
  xhr.abort()
  ~~~

## jQuery中的AJAX

- 使用jQuery发送完整ajax请求

  ~~~javascript
  $.ajax({
      url: '/ajax', // 请求的url
      mothod: 'POST', // 请求类型，如不写，则默认为GET
      data: { a: 1 }, // 请求携带的参数
      dataType: 'json', // 响应数据格式
      timeout: 2000 // 指定超时的时间
      success: (data, resText, xhr) => { connsole.log(data, resText) }, // 成功的回调,data为接收到的响应体，resText为响应状态字符串，xhr为原生AJAX中的xhr
      error: (xhr) => { console.log('请求出错') } // 失败的回调
  })
  ~~~

- 精简get请求

  - $get(url [,data] \[,callback] [,type])

    - url

      请求的url地址

    - data

      请求携带的参数，如果有query参数可以以对象的形式写在这里，如果有params参数择需要写在url地址里

    - callback

      载入成功时的回调函数

    - type

      设置返回内容格式，xml/html/script/json/text/_default

- 精简post请求

  - $post(url [,data] \[,callback] [,type])

    - url

      请求的url地址

    - data

      请求携带的参数

    - callback

      载入成功时的回调函数

    - type

      设置返回内容格式，xml/html/script/json/text/_default


## 跨域

#### 同源策略

- 同源策略是由Netscape提出的一个著名的安全策略，现在所有支持JavaScript的浏览器都会使用这个策略
- Web是构建在同源策略基础上的，浏览器只是针对同源策略的一种实现
- 同源指：协议、域名（IP地址），端口都完全相同
- 非同源的限制
  - Cookie不能读取
  - DOM无法获得
  - Ajax请求不能获取数据（可以发送请求）

#### 解决跨域问题

##### JSONP

- JSONP(JSON with Padding)是一个非官方的跨域解决方案，只支持get请求

- 原理
  - 网页有一些标签天生具有跨域能力，比如img link iframe script等
  - JSONP就是利用script标签引入src地址来发送请求的url，从而避开同源策略
  - JSONP不通过AJAX请求也能做到不刷新页面来发送请求接收数据
    - 在前端封装好函数，设置形参以接收响应数据
    - 通过操作DOM给页面添加script标签，通过script标签的src地址发送请求
    - 在服务端对应的路由中设置响应体为：调用前端指定函数的代码字符串，并将json对象数据传入函数参数中，通过调用前端函数的方式实现无刷新接收数据

- 通过JSONP的方式发送请求

  ~~~html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>jsonp</title>
      <style>
          .content {
              overflow: auto;
              width: 300px;
              height: 200px;
              margin-top: 20px;
              border: 1px solid #000
          }
      </style>
  </head>
  
  <body>
      <button>click</button>
      <div class="content"></div>
      <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.1/jquery.min.js"></script>
      <script>
          // 获取DOM元素
          const btn = document.querySelector('button')
          const content = document.querySelector('.content')
  
          // 绑定点击事件
          btn.addEventListener('click', () => {
              //~~~~~~~~~~~~~~~~~~~使用原生方式~~~~~~~~~~~~~~~~~~~
              // 定义全局函数，参数为响应传回的json对象
              window.getRes = (data) => {
                  let ul = document.createElement('ul')
                  ul.innerHTML = `<li>name:${data.name}</li>
                  <li>${data.age}</li>`
                  content.appendChild(ul)
              }
  
              // 创建script标签节点，在src中指定路由地址，参数为上面全局函数名称，将script标签节点插入body末尾
              const scriptNode = document.createElement('script')
              scriptNode.src = 'http://127.0.0.1:8080/jsonp?callback=getRes'
              document.body.appendChild(scriptNode)
  
              // 执行完后移除script标签
              document.body.removeChild(scriptNode)
              //~~~~~~~~~~~~~~~~~~~使用原生方式~~~~~~~~~~~~~~~~~~~
              
              //~~~~~~~~~~~~~~~使用jquery封装的getJSON方法~~~~~~~~
              // url中第一个?为查询字符串前缀，第二个?为函数名称的占位符
              // 占位符处传入的函数名称为'jQuery随机数_时间戳'的形式，防止函数名称冲突
              $.getJSON('http://127.0.0.1:8080/jsonp?callback=?', {}, (data) => {
                  let ul = document.createElement('ul')
                  ul.innerHTML = `<li>name:${data.name}</li>
                  <li>${data.age}</li>`
                  content.appendChild(ul)
              })
              //~~~~~~~~~~~~~~~使用jquery封装的getJSON方法~~~~~~~~
          })
  
      </script>
  </body>
  
  </html>
  ~~~

  ~~~javascript
  // 引入模块
  const express = require('express')
  
  // 配置app
  const app = express()
  app.use(express.static('public'))
  
  // 配置路由
  app.get('/jsonp/', (req, res) => {
  
      // 通过params参数结构出要调用的参数名
      const { callback } = req.query
  
      // 定义数据对象
      const person = {
          name: '张三',
          age: 18
      }
  
      // 传回的响应的字符串即html中script标签执行的js代码，通过该代码执行html中的指定函数，将数据对象转换为json对象作为该函数的实参
      res.send(`${callback}(${JSON.stringify(person)})`)
  })
  
  // 监听端口
  app.listen('8080', () => console.log(`
  http://localhost:8080/jsonp.html
  `))
  ~~~


#### CORS

- CORS(Cross-Origin Resource Sharing)，跨域资源共享，是官方的跨域解决方案，在服务器端配置，支持所有常见的请求

- 原理

  - 设置响应头来告诉浏览器，该请求允许跨域，浏览器收到响应后会将该响应放行

- 响应头配置

  - 简单请求（get、post、head）

    ~~~javascript
    app.get('/cors', (req, res) => {
        res.set('Access-Control-Allow-Origin', '*') // 第二个参数为允许的域，若为*通配符，则允许所有跨域请求
        res.set('Access-Control-Expose-Header', '*') // 暴露所有响应头，否则跨域的响应头将不完整
        res.send('响应内容')
    })
    ~~~

  - 复杂请求

    ~~~javascript
    // 复杂请求，如put，delete等，会在请求发送前发送一个options嗅探请求，因此需要配置对应的options请求的路由
    app.options('/cors', (req, res) => {
        res.set('Access-Control-Allow-Origin', '*') 
        res.set('Access-Control-Expose-Header', '*') 
        res.set('Access-Control-Allow-Methods', '*') // 允许跨域的复杂请求类型，若为*通配符，则允许所有请求类型
        res.send() // 需要设置响应给浏览器，但无需设置内容
    })
    
    app.put('/cors', (req, res) => {
        res.set('Access-Control-Allow-Origin', '*') 
        res.set('Access-Control-Expose-Header', '*') 
        res.send('响应内容')
    })
    ~~~

- 使用中间件设置cors

  ~~~javascript
  // 需先安装cors包
  const cors = require('cors')
  app.use(cors())
  ~~~


## Fetch

- fetch是原生函数，可不通过XMLHttpReques对象提交ajax请求

- 使用时返回一个promise对象：

  - 成功的result为请求服务器成功的对象，该对象调用json方法后得到一个新的promise对象
  - 新的promise对象成功的result为响应体

- 相关API

  - GET请求

    ~~~javascript
    fetch(url).then(function(response) {
        return response.json()
      }).then(function(data) {
        console.log(data)
      }).catch(function(e) {
        console.log(e)
      })
    ~~~

  - POST请求

    ~~~javascript
      fetch(url, {
        method: "POST",
        body: JSON.stringify(data),
      }).then(function(data) {
        console.log(data)
      }).catch(function(e) {
        console.log(e)
      })
    ~~~

    

