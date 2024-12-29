# Koa

- `Koa` 是 `Express` 同一团队开发的一个新的 `Web` 框架
  - `Koa` 相对于 `Express` 更轻量
  - `Koa` 相对于 `Express` 具有更强的异步处理能力

## 基本使用

- 使用示例

  ~~~javascript
  const Koa = require('koa')
  
  const app = new Koa()
  
  app.use((ctx, next) => {
    console.log('匹配到 koa 中间件')
    ctx.body = '返回的数据'
  })
  
  app.listen(8080, () => {
    console.log('服务器启动成功')
  })
  ~~~

## Koa 中间件

- 通过 `koa` 创建的 `app` 对象，注册中间件只能通过 `use` 方法，不提供 `METHODS` 相关方法匹配不同请求方式，不提供 `path` 中间件匹配路径
  - `METHODS` 相关方法和 `path` 中间件可以由 `koajs/router` 的路由实例提供 
- `koa` 中注册的中间件提供了两个参数：
  - `ctx`
    - `ctx`
      - 代表一次请求的上下文对象
    - `ctx.request`
      - `koa` 封装的请求对象
    - `ctx.response`
      - `koa` 封装的响应对象
    - `ctx.req`
      - 原生 `Node` 的请求对象
    - `ctx.res`
      - 原生 `Node` 的响应对象
    - `ctx.path`
      - 请求路径
    - `ctx.method`
      - 请求方式
    - `ctx.app`
      - 从 `ctx` 中可以拿到 `app`
      - `app` 是一个 `EventEmitter` 实例
  - `next`
    - 本质上是一个 `dispatch`，与 `express` 中的 `next` 类似

## Koa 路由

- `Koa` 本身没有提供路由，可以选择第三方库 `koa-router`，或官方库 `koajs/router`

- `koajs/router` 的使用

  - 安装

    ~~~bash
    npm i @koa/router
    ~~~

  - 基本使用

    ~~~javascript
    const Koa = require('koa')
    const KoaRouter = require('@koa/router')
    
    const app = new Koa()
    // 创建路由对象，前缀路径为 '/users'
    const userRouter = new KoaRouter({ prefix: '/users' })
    
    // 在路由中注册路径中间件和路由中间件
    userRouter.get('/', (ctx, next) => { })
    userRouter.post('/', (ctx, next) => { })
    userRouter.delete('/:id', (ctx, next) => { })
    userRouter.patch('/:id', (ctx, next) => { })
    
    // 让路由中的中间件生效
    app.use(userRouter.routes())
    app.use(userRouter.allowedMethods())
    
    app.listen(8080, () => {
      console.log('服务器启动成功')
    })
    ~~~

    - `allowMethods` 中间件用于判断请求的 `method` 是否支持
      - 如果接收到的请求方式可以被 `userRouter` 中的路由匹配到，则可以正常请求
      - 如果接收到的请求方式不会被 `userRouter` 中的路由匹配到，则会返回信息 `Method Not Allowed`，状态码 `405`
      - 如果接收到的请求方式无法被服务器理解，如 `link`、`copy`、`lock` 等扩展或非标准 `HTTP` 方法的请求，则会返回信息 `Not Implemented`，状态码 `501`

## 参数解析

- `query` 参数和 `params` 参数

  - `ctx.query` 或 `ctx.request.query` 中存储请求中的 `query` 参数
  - `ctx.params` 中存储路由解析到的请求中的 `params` 参数

- 使用第三方中间件 `koa-bodyparser` 解析 `json` 格式和 `x-www-form-urlencoded` 格式的请求体

  - 安装

    ~~~bash
    npm i koa-bodyparser
    ~~~

  - 使用 `bodParser` 中间件

    ~~~javascript
    const bodyParser = require('koa-bodyparser')
    app.use(bodyParser())
    ~~~

  - 之后可以在 `ctx.request.body` 中获取解析后的请求体数据

- 使用 `koajs/multer` 解析 `multipart/form-data` 格式的请求体

  - 安装（需要安装 `multer` 依赖）

    ~~~bash
    npm i @koa/multer multer
    ~~~

  - 使用 `multer` 解析 `multipart/form-data` 中的非文件数据

    ~~~javascript
    const multer = require('@koa/multer')
    const formParser = multer()
    
    userRouter.post('/formdata', formParser.any(), (ctx, next) => {
      console.log(ctx.request.body)
      ctx.body = '返回的数据'
    })
    ~~~

  - 使用 `multer` 解析 `multipart/form-data` 中的文件

    - 使用方式同 `express` 中使用 `multer` 中间件
    
    - 文件信息在 `ctx.request.file` 中
    
    - 示例
    
      ~~~javascript
      const multer = require('multer')
      const path = require('path')
      
      const upload = multer({
        dest: path.resolve(__dirname, "./uploads"), // 获取文件后的存放路径，配置了 storage 则会被覆盖
        storage: multer.diskStorage({ // 文件存储相关
          destination(req, file, cb) { // 用于指定存放路径，和 dest 选项一样，如果使用了 storege 选项则会覆盖 dest
            cb(null, path.resolve(__dirname, "./uploads"))
          },
          filename(req, file, callback) { // 指定文件名称
            cb(null, Date.now() + '_' + path.extname(file.originalname))
          }
        })
      })
      
      // 接受单张照片上传
      app.post('/avatar', upload.single('avatar'), (req, res) => {
        console.log(req.file) // 文件信息在 req.file 中
        res.end('上传头像成功')
      })
      
      // 接受多张照片上传
      app.post('/photos', upload.array('photos'), (req, res) => {
        console.log(req.files) // 文件信息在 req.files 中
        res.end('上传多张照片成功')
      })
      ~~~
    

## 数据响应

- 给 `ctx.body` 赋值设置响应主体

  - `ctx.response.body` 同 `ctx.body`

- 响应主体可以是以下内容

  - `string` 

    ~~~javascript
    ctx.body = '返回的内容'
    ~~~

  - `Buffer`

    ~~~javascript
    ctx.body = Buffer.from('返回的内容')
    ~~~

  - `Stream`

    ~~~javascript
    const readStream = fs.createReadStream('./test.png')
    ctx.type = 'image/jpeg'
    ctx.body = readStream
    ~~~

  - `Object` | `Array`

    ~~~javascript
    ctx.body = {
      status: 200,
      data: []
    }
    ~~~

  - `null`

    - 不输出任何内容，状态码为 `204`

- `ctx.status` 的值为返回的状态码，默认为 `200` 或 `204`

  - `ctx.response.status` 同 `ctx.status`

## 静态服务器

- 使用 `koa-static` 库在 `koa` 中部署静态服务器

  - 安装

    ~~~bash
    npm i koa-static
    ~~~

  - 使用

    ~~~javascript
    const static = require('koa-static')
    app.use(static('./build'))
    ~~~

## 错误处理

- 可以使用 `app` 发出与监听事件进行统一的错误处理

  - 在路由中间件中，遇到发送错误的条件分支时，使用 `ctx.app` 发出 `error` 事件

    ~~~javascript
    ctx.app.emit('err', -1001, ctx)
    // 传入定义好的错误码和 ctx
    ~~~

  - 使用 `app` 监听 `error` 事件，根据不同错误码进行处理

    ~~~javascript
    app.on('err', (code, ctx) => {
      let message = '发生未知的错误'
      switch (code) {
        case -1001:
          message = '账号或密码错误'
          break
        case -1002:
          message = '请求参数不正确'
          break
        case -1003
          message = '未授权，请检查 token 信息'
          break
      }
      ctx.body = { status: code, message }
    })
    ~~~


## Koa 与 Express 中的 next

- 区别

  - `Koa` 中的 `next()` 返回的类型是 `Promise`
  - `Express` 中的 `next()` 返回的类型是 `void`

- 场景

  - 在 `Koa` 中间件中调用了 `next()` 之后，仍有其它操作，例

    ~~~javascript
    // 中间件 1
    app.use((ctx, next) => {
      // ... 中间件代码
      next()
      /// ... 调用 next() 之后的代码
    })
    
    // 中间件 2
    app.use(async (ctx, next) => {
      // ... 该中间件中开启异步任务
    })
    ~~~

    - 上述代码中，在中间件 1 中调用了 `next()` 之后开始执行中间件 2，当中间件 2 执行完同步代码后，会立刻执行中间件 1 中 `next()` 之后的代码，在 `Express` 中间件中也是这样的执行顺序

    ~~~javascript
    // 中间件 1
    app.use(async (ctx, next) => {
      // ... 中间件代码
      await next()
      /// ... 调用 next() 之后的代码
    })
    
    // 中间件 2
    app.use(async (ctx, next) => {
      // ... 该中间件中开启异步任务
    })
    ~~~

    - 上述代码中，中间件 1 中 `next()` 之后的代码，会等待中间件 2 中的异步结果出来后再执行，而 `Express` 中间件没有设计这样的机制
  
- 注意：

  - `Koa` 中的中间件的 `next()` 即为调用下一个中间件，如果之后的中间件中可能存在异步任务，也需要将当前中间件定义为 `async` 函数，并用 `await` 修饰符调用 `next`，否则之后的中间件的异步任务还未等到结果，当前中间件就结束了

## 补充

- 添加全局共享属性或方法

  - `app.context` 是 `koa` 应用的上下文原型，也是中间件中的 `ctx` 的原型对象，通过向 `app.context` 可以为全局挂载共享属性或方法

  - 例如将所有要挂载的属性放入 `utils` 对象中

    ~~~javascript
    Object.assign(app.context, utils)
    ~~~


## 中间件 TS 支持

例如 `@koa/router`、`@koa/multer`，它们对应的 `TS` 类型库为 `@types/koa__router` 和 `@types/koa__multer`。
