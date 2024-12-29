# Express

## 简介

- Express 是一个基于 Node.js 平台的极简、灵活的 web 应用开发框架
- Express是基于Node.js的http内置模块进一步封装出来的
- 使用Express，可以方便、快速地创建Web网站服务器或API接口服务器
  - Web网站服务器：专门对外提供Web网页资源的服务器
  - API接口服务器：专门提供API接口的服务器

## 使用

~~~javascript
// 引入 express 模块
const express = require('express')

// 创建应用对象
const app = express()

// 配置路由
app.get('./', ()=>{hello express})

// 托管静态资源根目录
app.use(express.static(__dirname + 'public'))
// 托管后，可在url的路径中直接访问public目录中的文件

// 在托管的静态资源访问路径之前，挂载路径前缀
app.use('/files', express.static('files')) 
// 挂载前缀后，可通过/files前缀地址访问files目录中的文件，如：
// http://localhost:3000/files/package.json

// 可以托管多个静态资源目录，访问静态资源文件时，express.static()函数会根据目录的添加顺序查找所需的文件

// 开启服务器，监听3000端口
app.listen(3000)
~~~

## 路由(Route)

#### 简介

- 广义上来讲，路由就是映射关系
- 在Express中，路由指的是客户端的请求与服务器处理函数之间的映射关系

#### Route的组成

1. HTTP请求类型(get或post)
2. URI路径
3. 处理函数

#### Route的实现

- app.method(path, callback)

  - 参数

    - method

      HTTP的请求方法，get或post

      all可同时配置两种请求

    - path

      需要处理的URL地址

    - callback

      处理请求的回调函数

#### Route的运行流程

- 当Express服务器收到一个请求后，需先经过路由的匹配
- 在匹配时，会按照路由定义的先后顺序将请求和路由进行匹配，如果请求类型和路径同时匹配成功，则Express会将这次请求转交给对应的回调函数进行处理


- 在回调函数中，通过Request对象可以读取请求，通过Response对象可以返回响应

## Request对象

#### 简介

- Request对象是路由回调函数中的第一个参数，包含用户发给服务器的请求信息


- 通过Request对象可以读取用户发送的请求，包括URL地址中的查询字符串中的参数，post请求体中的参数

#### 属性和方法

- request.method

  - 请求的方法

- request.url

  - 请求的url

- request.httpVersion

  - 请求的HTTP协议版本

- request.headers

  - 请求头信息，拿到的是一个对象

- request.get(key)

  - 获取请求头中指定key对应的value

- request.query

  - get请求url中查询字符串的参数，拿到的是一个对象

- request.params

  - get请求url中的params参数，拿到的是一个对象

  - 使用方式

    ~~~javascript
    app.get('/:id',(req,res) => {
        const id = req.params.id
        console.log(id)
        res.end(id)
    })
    ~~~

- request.body

  - post请求体，拿到的是一个对象(要借助一个中间件)

## Response对象

#### 简介

- Response对象是由路由回调函数中的第二个参数，代表了服务器发送给用于的响应信息
- 通过Response对象可以设置响应报文中的各个内容，包括响应头和响应体

#### 属性和方法

- response.statusCode
  - 响应状态码
- response.statusMessage
  - 响应状态字符串
- response.setHeader(key, value)
  - 设置响应头
- response.write(内容)
  - 将内容写入响应体
- response.send(内容)
  - 给浏览器做出一个响应，会自动追加响应头
  - 不能在设置了响应体之后在设置响应头，因此该方法不能在response.write()之后执行
- response.end(内容)
  - 给浏览器做出一个响应，不会自动追加响应头
- response.download(路径)
  - 让浏览器下载一个文件
  - 不能设置"content-type"响应头
- response.sendfile(路径)
  - 将一个文件中的内容响应给浏览器
  - 必须为绝对路径
- response.redirect(url)
  - 重定向到一个新的地址
  - 状态码会变为302
- response.set(key, value)
  - 自定义响应头内容
- response.status(code)
  - 设置响应状态码

## 中间件(Middleware)

#### 简介

- 中间件指业务流程的中间处理环节
- Express的中间件，本质上是一个处理函数，当一个请求到达Express服务器后，可以连续调用多个中间件，对这次请求进行预处理
- 中间件的形参列表，包含req，res，next三个参数，而路由处理函数中只包含req和res
- next函数是实现多个中间件连续调用的关键，它表示把流转关系转交给下一个中间件或路由

#### 中间件功能

- 执行任何代码
- 修改请求和响应对象
- 终结请求-响应循环
- 调用堆栈中的下一个中间件

####  中间件的分类

- 应用级别的中间件

  - 通过app.use()或app.get()或app.post()，绑定到app实例上的中间件，叫做应用级别中间件
  - 全局中间件和局部中间件都是应用级别的中间件

- 路由级别中间件

  - 绑定到exports.Router()实例上的中间件，叫做路由级别的中间件
  - 路由器(Router)是路由级别的中间件

- 错误级别的中间件

  - 作用：专门用来捕获整个项目中发生的异常错误，从而防止项目崩溃的问题
  - 格式：必须有四个形参：(err, req, res, next)
  - 注意：错误级别的中间件，必须注册在所有路由之后

  ~~~javascript
  app.get('/',function(req,res){
     throw new Error('服务器内部发生错误') // 抛出一个自定义的错误
     res.send('Home page')
  })
   
  app.use(function(err,req,res,next){    //错误级别的中间件
     console.log('发生了错误',+err.message)   //在服务器打印错误消息
     res.send('Error!' + err.message)   //在客户端响应错误相关的内容
  })
  ~~~

- Express内置的中间件

  - express.static(路径字符串)

    快速托管静态资源的内置中间件

  - express.json()

    解析JSON格式的请求体数据（仅在4.16.0以上版本中可用）

  - express.urlencoded({ extended: false })

    - 解析URL-encoded格式的请求体数据（仅在4.16.0以上版本中可用）
    - 可替代body-parser，用法相同

- 第三方中间件

  - 第三方开发的中间件，如body-parser

#### 中间件实例

- 全局中间件
  - 每个HTTP请求到达服务端后都会经过的中间件
  - 使用方法
    - 使用app.use()方法调用中间件函数

  ~~~javascript
  // 引入express
  const express = require('express')
  
  // 创建应用对象
  const app = express()
  
  // 配置静态资源（app.use()方法调用的是静态资源中间件）
  app.use(express.static('public'))
  
  // 使用中间件，没有挂载路径，应用的每个请求都会执行该中间件
  app.use(function(req, res, next){
      console.log('这是中间件的响应')
      // 如果不调用next方法，下面的路由将不起作用
      next()
  })
  
  // 配置路由
  app.get('/index', function(req, res){
      console.log('路由index收到get请求')
      res.send('这里是路由返回的信息，/index收到了get请求')
  })
  
  app.post('/index', function(req, res){
      console.log('路由index收到post请求')
      res.send('这里是路由返回的信息，/index收到了post请求')
  })
  
  // 启动服务器
  app.listen(80, function(){
      console.log('服务器启动成功，端口80监听中')
  })
  ~~~


- 局部中间件
  - 在特定的路由规则下会经过的中间件
  - 使用方法
    - 在需要使用的路由方法的第二个参数中传入中间件函数

  ~~~javascript
  // 引入express
  const express = require('express')
  
  // 创建应用对象
  const app = express()
  
  // 配置静态资源
  app.use(express.static('public'))
  
  // 配置中间件
  const checkUser = function (req, res, next){
      // 如果请求url中包含admin=1的查询字符串，则跳转至请求页面，否则跳转至登录页面
      const isAdmin = req.query.admin
      if (isAdmin === '1'){
          next()
      }else{
          res.redirect('/login')
      }
  }
  
  // 配置路由
  app.get('/index',checkUser, function(req, res){
      console.log('路由index收到get请求')
      res.send('这里是路由返回的信息，/index收到了get请求')
  })
  
  // checkUser中间件不会影响下面的这个路由
  app.get('/login', function(req, res){
      console.log('跳转至登录页面')
      res.send('登录页面')
  })
  
  // 启动服务器
  app.listen(80, function(){
      console.log('服务器启动成功，端口80监听中')
  })
  ~~~

  - 在路由中可以定义多个局部中间件

  ~~~javascript
  // 以下两种写法都可以
  app.get('/', mw1, mw2 ,(req, res) => {res.send('Home Page')})
  app.get('/', [mw1, mw2] ,(req, res) => {res.send('Home Page')})
  ~~~

- body-parser中间件

  - 用以获取请求体

    [正文解析器 - npm (npmjs.com)](https://www.npmjs.com/package/body-parser)

  ~~~javascript
  // 引入express
  const express = require('express')
  const bodyParser = require('body-parser')
  const { response } = require('express')
  
  // 创建应用对象
  const app = express()
  
  // 配置body-parser中间件，解析url编码格式的请求体
  app.use(bodyParser.urlencoded({ extended: false }))
  
  // 配置静态资源
  app.use(express.static('public'))
  
  // 配置路由 
  app.get('/regist', function (req, res) {
      res.sendFile(__dirname + '/reg.html')
  })
  
  app.post('/login', function (req, res) {
      console.log(req.body)
      res.send('请求体接收完毕')
  })
  
  // 启动服务器
  app.listen(80, function () {
      console.log('服务器启动成功，端口80监听中')
  })
  ~~~

#### 中间件使用步骤

1. 安装（如果是第三方中间件）

2. 代码中引入中间件

3. 全局中间件，用app.use()方法调用

   路由中间件，在路由中进行定制设置

4. 在回调函数中使用指定语法操作（可选）

#### 使用中间件的注意事项

- 一定要在路由之前注册中间件

- 客户端发送过来的请求，可以连续调用多个中间件进行处理

- 执行完中间件的业务代码后，记得调用next()函数

- 为防止代码逻辑混乱，调用next()函数后不要再写额外的代码

- 多个中间件之间，共享同一份req和res对象

  ~~~javascript
  app.use((req,res,next)=>{
   
    //获取到请求到达服务器的时间
     const time = Date.now()
    //为req对象添加自定义属性，从而把时间共享给后面的所有路由
     req.startTime = time
     next()
  })
   
  app.get('/',(req,res)=>{
     res.send('Home page' + req.startTime)
  })
   
  app.get('/user',(req,res)=>{
     res.send('User page' + req.startTime)
  })
  ~~~

  

## 路由器(Router)

#### 简介

- Router是一个完整的中间件和路由系统，也可以看作是一个小型的app对象
- 通过Router来挂载路由，可以实现将路由抽离为单独模块，方便对路由进行模块化的管理

#### Router的使用

1. 创建一个routes文件夹

2. 创建单独的router,js文件

   ~~~javascript
   // 引入express模块
   const express = require('express')
   // 创建路由对象，router是一个微型的app对象
   const router = express.Router()
   // 配置路由
   router.get('/home', function(req, res){
       res.send('首页')
   })
   // 暴露router对象
   module.exports = router
   ~~~

3. 在主文件中引入router.js

   ~~~javascript
   // 引入express模块
   const express = require('express')
   // 创建应用对象
   const app = express()
   // 引入router.js中的router对象
   const router = require('./routes/router')
   // 设置router为中间件
   app.use(router)
   /* 类似于托管静态资源目录，路由模块也可挂载访问前缀
   * app.use('/api', router)
   */
   // 启动服务器
   app.listen(80, function () {
       console.log('服务器启动成功，端口80监听中')
   })
   ~~~

   

## EJS

#### 简介

- EJS是一个高效的JavaScript模板引擎
- 模板引擎是为了使用户界面与业务数据（内容）分离而产生的
- 简单来说，使用EJS模板引擎就能动态渲染数据

#### EJS在Express中的使用（无需引入EJS模块，但需安装EJS包）

1. 下载安装

   npm i ejs --save

2. 设置express使用的模板引擎为ejs

   app.set('view engine', 'ejs')

3. 设置ejs使用的模板存放的位置

   app.set('views', './views')

4. 在views目录下创建模板文件，必须是ejs格式

   xxx.ejs

5. 在路由中设置模板响应

   response.render('模板文件名', 数据对象)

#### EJS数据遍历（使用EJS模块的方法）

```javascript
// 引入模块
const express = require('express')
const ejs = require('ejs')

// 创建应用对象
const app = express()

// 配置路由
app.get('/list', (req, res) => {
    // 配置模板和数据对象
    let str = `<ul>
                <% for(let i = 0; i<number.length; i++){ %>
                    <li><%= number[i] %></li>
                <% } %>
                </ul>`

    let data = {
        number: ['1', '2', '3']
    }

    // 渲染模板
    let result = ejs.render(str, data)

    // 输出到响应体
    res.send(result)
})

// 启动服务器
app.listen(80)
```

## 会话控制

#### 简介

- HTTP协议使一个无状态的协议，它无法区分多次请求是否发送自同一客户端
- 通过会话控制，来管理不同的客户端

#### cookie

##### 简介

- cookie本质是一个存储在浏览器的文本，随着http请求自动传递给服务器
- 也可以说cookie是一个头（请求头/响应头）
  - 服务器以响应头的形式将cookie发送给浏览器
  - 浏览器收到后会自动将cookie保存
  - 浏览器再次访问服务器时，会以请求头的形式将cookie发回
  - 服务器可以通过检查浏览器发回的cookie来识别不同的客户端
- cookie是按照域名进行分类保存的，发送请求时，只会携带当前域名下的cookie进行请求 
- 带有时效性的cookie，关闭浏览器不会被销毁
- 不带时效性的cookie，关闭浏览器就会被销毁
- cookie保存在浏览器端

##### 使用cookie

- 通过配置cookie-parser中间件，可以将cookie解析为一个对象，并且添加为res的cookies属性

  1. 安装cookie-parser包

     > npm i cookie-parser -s

  2. 引入

     > const cookieParser = require('cookie-parser')

  3. 设置为中间件

     > app.use(cookieParser())

  4. 在路由中创建cookie

     - res.cookie(key, value [, option])

       - key的类型为String

       - value的类型为String和Object

         如果是Object会在cookie.serialize()之前自动调用JSON.stringify对其进行处理


       - option类型为Object，可用属性如下
         1. domain
            - cookie在什么域名下有效
            - 类型为String
            - 默认为网站域名
         2. expires
            - cookie过期时间
            - 类型为Date
            - 如果没有设置或设置为0，则关闭浏览器后，这个cookie会被销毁
         3. httpOnly
            - 只能被web server访问
            - 类型为Boolean
         4. maxAge
            - 实现expires的功能，设置cookie过期时间
            - 类型为String
            - 表示创建cookie后，多少毫秒之后cookie到期
         5. path
            - cookie在什么路径下有效，默认为"/"
            - 类型为String
         6. secure
            - 只能被HTTPS使用
            - 类型为Boolean，默认为false
         7. signed
            - 使用签名
            - 类型为Boolean，默认为false
            - express会使用req.secret来完成签名，需要cookie-parser配合使用


​    
​     - 设置没有时效性的cookie
​    
​     > res.cookie('username', 'name' )
​    
     - 设置有效期为24小时的cookie
    
     > res.cookie('username', 'name' , {maxAge: 1000\*60\*60\*24})

  5. 修改cookie

     - cookie一旦发送给浏览器，就不能再修改了
     - 可以使用同名的cookie来替换已有cookie

  6. 删除cookie

     - 可以使用一个立即失效的cookie来替换

       > res.cookie('username', 'name', {maxAge: 0})

     - 删除指定的cookie

       > res.clearcookie('username')

  7. 读取cookie

     > req.cookie

##### cookie的缺陷

- 各个浏览器对cookie的数量和大小都有不同的限制，导致cookie中不能保存过多的信息，一般数量不超过50个，单个大小不超过4kb
- cookie时由服务器发送给浏览器，再由浏览器将cookie发回，如果cookie较大会导致发送速度非常慢，影响用户体验

#### session

##### 简介

- session是一个对象，存储特定用户会话所需的属性及配置信息
- sessions是保存在服务器端的数据，保存介质为：文件、数据库、内存

##### session运作流程

- 在服务器中为每一次会话创建一个对象
- 每个对象设置一个唯一的id
- 将该id以cookie的形式发送给浏览器
- 将会话中产生的数据统一保存到这个对象中
- 会话数据保存在服务器中，id保存在客户端中

##### 使用session

1. 下载安装

   > npm i express-session -s

2. 引入模块

   > const session = require('express-session')

3. 设置为中间件

   ~~~javascript
   app.use(session({
       name: 'id22', // 设置cookie的name，默认值为connect.sid
       secret: 'keybord cat', // 必须，参与加密的字符串（又称签名）
       resave: false, // 必须，是否在每次请求时重新保存session
       saveUninitialized: false, // 必须，是否为每次请求都设置一个cookie来存储session的id
       cookie: {
           httpOnly: true, // 开启后前端无法通过JS操作
           maxAge: 1000*30 // 控制sessionID的过期时间
       }
   }))
   ~~~

4. 设置和读取session

   - req.session对象
   - 一般用户登录时设置session
   - 检测用户是否登录时读取session

5. 删除session

   > req.session.distory(function(){
   >
   > ​    console.log('销毁成功')
   >
   > ​    response.send('安全退出登录')
   >
   > })

#### cookie和session的区别

- 存放的位置
  - cookie存放于客户端，在本地临时文件夹中
  - session存放于服务器的内存中，一个session域对象为一个用户浏览器服务
- 安全性
  - cookie是以明文的方式存放在客户端，安全性低，可以通过一个加密算法进行加密后存放
  - session存放于服务器的内存中，安全性比cookie高
- 网络传输量
  - cookie会传递消息给服务器
  - session本身存放于服务器，但是通过cookie传递id，会有少量的传送流量
- 生命周期（以20分钟有效时长为例）
  - cookie的生命周期是累计的，从创建时开始计时，超过20分钟后，cookie生命周期结束
  - session的生命周期是间隔的，从创建时开始计时，如果在20分钟内没有访问session，那么session的生命周期被销毁；如果在20分钟内访问过session，那么session的生命周期会重新开始计算
  - 关机会结束session的生命周期，但对有生命周期的cookie没有影响
- 大小
  - cookie保存的数据不能超过4kb，很多浏览器限制一个站点最多保存50个cookie
  - session保存的数据理论上只受内存大小限制