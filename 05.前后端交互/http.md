# HTTP协议

## 介绍

- HTTP(hypertext transport protocol)协议，即超文本传输协议，是一种基于TCP/IP的应用层通信协议，这个协议详细规定了浏览器和万维网服务器之间互相通信的规则
- 协议主要规定了两方面的内容
  - 客户端向服务器发送请求，称之为请求报文
  - 服务器向客户端返回数据，称之为响应报文

## 请求

- HTTP请求报文包括四部分

  - 请求行
  - 请求头
  - 空行
  - 请求体

- 请求行

  ~~~http
  GET http://localhost:3000/index.html?username=sunwukong&password=123123 HTTP/1.1
  ~~~

  - 请求方式

    - 常见的HTTP的请求方法：GET 和 POST（其余还有PUT DELETE HEAD OPTIONS TRACE CONNECT共八种 ）

    - 当提交表单，且表单的method属性值为post时，会发送POST 请求

    - GET和POST的区别

      - GET主要用来获取数据

        POST主要用来提交数据

      - GET带参数请求，是将参数缀到URL之后

        POST带参数请求，是将参数放到请求体中

      - POST请求相对GET请求更安全，因为GET请求的参数会暴露在浏览器地址栏

      - GET请求大小有限制，一般为2k

        POST请求大小没有限制

  - 请求的URL

    - 协议

      - https/http
      - mongodb
      - ftp
      - ssh

    - 域名 

    - 路径

    - 查询字符串(query string)

      ?开头，由&分隔的参数

      urlencoded编码形式

    - `锚点`

      `#开头，用于页面跳转，不在网页请求中`

  - 协议的版本

- 请求头

  ~~~http
  Host: localhost:3000
  Connection: keep-alive
  Pragma: no-cache
  Cache-Control: no-cache
  Upgrade-Insecure-Requests: 1
  User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
  Accept-Encoding: gzip, deflate, br
  Accept-Language: zh-CN,zh;q=0.9
  ~~~

  - Accept

    - 表明客户端所能接受的数据的类型

  - Accept-Language

    - 表明客户端支持的预览类型
    - q代表权重

  - User-Agent

    - 客户端（浏览器）的字符串标志
    - 每一款浏览器的值都不一样

  - Accept-Encoding

    - 表明客户端支持的压缩方式

  - Host

    - 主机名

  - Connection

    - 连接配置

    - keep-alive

      保持连接，后续请求不用重新连接

    - close

      断开

  - Cookie

    - 特殊的请求头，用于识别用户身份

- 请求体

  - GET请求的请求体是空的，POST请求的请求体一般不为空
  - 请求体接收页面提交的表单中的对应的name属性和表单值
  - 请求体的格式可以是任意格式，常用格式为JSON与URL查询字符串

## 响应

- HTTP响应报文包括四部分
  - 响应行
  - 响应头
  - 空行
  - 响应体

- 响应行

  ~~~http
  HTTP/1.1 200 OK
  ~~~

  - 协议的版本

  - 响应状态码

    - 响应信息分为五类
      - 信息响应(100-199)
      - 成功响应(200-299)
      - 重定向(300-399)
      - 客户端错误(400-499)
      - 服务器错误(500-599)

  - 响应状态字符串

    - 与响应状态码一一对应，描述响应状态

    - 示例

      - 200 OK

        成功

      - 302  Found

        跳转

      - 403 Forbidden

        禁止的

      - 404 Not  Found

        找不到资源

      - z500 Internal Server Error

        服务器内部错误

- 响应头

  ~~~http
  X-Powered-By: Express
  Accept-Ranges: bytes
  Cache-Control: public, max-age=0
  Last-Modified: Wed, 21 Mar 2018 13:13:13 GMT
  ETag: W/"a9-16248b12b64"
  Content-Type: text/html; charset=UTF-8
  Content-Length: 169
  Date: Thu, 22 Mar 2018 12:58:41 GMT
  Connection: keep-alive
  ~~~

  - X-Powered-By: Express
    - 自定义的头，表示用的框架，一般不返回容易造成安全漏洞
  - Cache-Control
    - 缓存控制
    - 值为private时，只允许客户端缓存数据
    - 值为public时，允许代理缓存数据
  - Connection
    - 连接设置
  - <span style="color:red;font-weight:bold">Connect-Type</span>
    1. 响应体的MIME类型
       - text/html
         - 响应体为HTML内容
       - text/css
         - 响应体为CSS内容
       - application/javascript
         - 响应体为JavaScript
       - image/png
         - 响应体为png图片
    2. 响应体的字符集
  - Date
    - 响应时间
  - Expires
    - 过期时间
  - Server
    - 服务器信息
  - Set-Cookie
    - 设置cookie
  - Strict-Transport-Security
    - 与请求头Upgrade-Insecure-Requests结合使用，强制使用https发送请求
  - TraceId
    - 追踪id
  - X-Ua-Compatible
    - 强制IE浏览器使用最新的解析器来解析网页
  - Content-Length
    - 响应体的长度

- 响应体

  - 响应体的格式比较灵活，常见的格式有
    - HTML
    - JavaScript
    - CSS
    - JSON
    - 图片

## WEB服务

#### 使用NodeJS创建HTTP服务器

~~~js
// 1.引入http模块
const http = require('http')

// 2.调用方法，创建服务对象
const server = http.createServer(function(request, response){
    // 每当有请求发送给服务器时，该回调函数就会被调用
    // 获取请求和设置响应的代码写在这里
    response.end('Hello HTTP server')
})
/**
 * request 请求报文的封装对象
 * response 响应报文的封装对象
**/

// 3.监听端口，启动服务
server.listen(80, function(){
    console.log('服务已经启动，端口 80 监听中......')	
})
/**
 * 端口号对应计算机的服务窗口，总共65536个端口
 * 默认端口是80
 * 在终端中使用快捷键 ctrl + c 停止服务
**/
// -----------------------------------
// 连写
require('http').createServer((request, response) => {}).listen(80)
~~~

#### 获取请求

~~~javascript
// 获取请求方式
console.log(request.method)

// 获取请求的url
console.log(request.url)
  // 获取请求的url中的参数，需引入url模块
  const url = require('url')
  // 获取路径
  console.log(url.parse(request.url).pathname)
  // 获取查询字符串
  console.log(url.parse(request.url, true).query)

// 获取HTTP协议版本
console.log(request.httpVersion)

// 获取请求头信息
console.log(request.headers)

// 获取POST请求中的请求体
// 1.声明一个字符串变量
let body = ''
// 2. 绑定data事件
request.on('data',chunk => {
    // 拼接
    body += chunk.toString()
})
// 3. 绑定end事件
request.on('end', ()=>{
    // 引入querystring模块
    let qs = require('querystring')
    console.log(qs.parse(body))
    response.end('body receive')
})
~~~

- 查询字符串模块

  ~~~javascript
  const qs = query('querystring')
  // 将查询字符串解析为js对象
  qs.parse(查询字符串)
  ~~~

- url模块

  ~~~javascript
  const url = query('url')
  // 获取请求的url中的查询字符串的js对象形式
  url.parse(request.url, true).query
  // 补充：编码、解码uri（不属于url模块，用于转换中文路径）
  decodeURI(路径名)
  encodeURI(路径名)
  ~~~

#### 设置响应

~~~javascript
// 设置状态码
response.statusCode = 200

// 设置状态字符串
response.statusMessage = 'ok'

// 设置响应头
response.setHeader('content-type','text/html;charset=utf-8')

// 设置响应体
response.write('body') 
response.end('body') // 无论使用write()或end()，至少要设置一个内容，保证响应体不为空
~~~

#### 练习

~~~javascript
const http = require('http')
const fs = require('fs')
const url = require('url')
const server = http.createServer((request, response) => {
    // 获取查询字符串中的背景色
    let bg = url.parse(request.url, true).query.bg || 'rgb(128,128,128)'
    // 获取路径
    let pathName = url.parse(request.url).pathname
    // 获取请求方式
    let { method } = request
    // 设置响应体格式和字符集
    response.setHeader('content-type', 'text/html;charset=utf-8')
    // 设置响应体背景色
    response.write(`
    <style>
        body{
            background:${bg}
        }
    </style>
    <body>
    </body>
    `)
    // 根据路径返回不同内容
    if (pathName === '/login' && method.toUpperCase() === 'GET') {
        response.end(fs.readFileSync(__dirname + '/login.html'))
    } else if (pathName === '/register' && method.toUpperCase() === 'GET') {
         response.end(fs.readFileSync(__dirname + '/register.html'))
    } else {
        response.statusCode = 404
        response.statusMessage = 'wwe'
        response.end('<h1>404 Not Found<h1>')
    }
})

server.listen(80, () => console.log('服务已启动，端口80监听中......'))
~~~

#### 根据url路径响应对应的文件

~~~javascript
let directory = __dirname + '/public'
let filePath = directory + pathName
fs.readFile(filePath, (err, data) => {
    if (err) {
        response.statusCode = 404
        response.end('<h1>404 Not Found</h1>')
    } else {
        response.end(data)
    }
})
~~~

[通过读取文件获取响应类型](https://blog.csdn.net/BADAO_LIUMANG_QIZHI/article/details/109231621)

