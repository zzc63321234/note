### fs 模块

#### 简介

- `fs` 是 `File System` 的缩写，即文件系统
- 对于任何一个为服务器端服务的语言或者框架通常会有自己的文件系统
- 借助 `fs` 模块，可以在不同操作系统（`windows`、`Mac OS`、`Linux`）上操作文件

#### API

- [File system | Node.js v16.20.2 Documentation (nodejs.org)](https://nodejs.org/docs/latest-v16.x/api/fs.html)
- `fs` 的 `API` 大多数提供三种操作方式
  - 同步操作文件
    - 代码会被阻塞
  - 异步回调函数操作文件
    - 代码不会被阻塞
    - 需要传入回调函数，获取到结果时，回调函数被执行
  - 异步 `Promise` 操作文件
    - 代码不会被阻塞
    - 通过 `fs.promises` 调用方法操作，返回一个 `Promise`

##### 读取文件

- `fs.readFile(path[,options], callback)`

- 在同一目录下，创建文件 `HW.txt` 与 `test.js`

  ~~~javascript
  // 导入 fs 模块
  const fs = require('fs')
  
  // 同步读取
  const res1 = fs.readFileSync('./HW.txt')
  console.log(res1) // res1 的内容是 Buffer 格式
  console.log(res1.toString()) // 转换为字符串
  
  const res2 = fs.readFileSync('./HW.txt', {
    encoding: 'utf8' // 使用 utf-8 编码模式解码
  })
  console.log(res2) // res2 是解码后的字符串
  
  // 回调函数读取
  fs.readFile('./HW.txt', { encoding: 'utf8' }, (err, data) => {
    if (err) {
      console.log('读取文件失败：', err)
      return
    }
    console.log(data)
  })
  
  // Promise 读取
  fs.promises.readFile('./HW.txt', { encoding: 'utf8' }).then(res => {
    console.log(res)
  }).catch(err => {
    console.log('读取文件失败')
  })
  ~~~

##### 写入文件

- `fs.writeFile(file,data[,options],callback)`

  - `options` 可以传配置对象，也可以直接传编码格式字符串

- 示例

  ~~~javascript
  const fs = require('fs')
  
  const content = "Hello World"
  
  fs.writeFile('./foo.txt', content, {
    flags: 'a+', // 写入方式
    encoding: 'utf8' // 字符编码，写入文件时默认就是 utf8
  }, err => {
    if (err){
      console.log(err)
    }
  })
  ~~~

- `flags` 选项

  - `w` 以写入模式打开文件并进行写入，如果不存在则创建文件，写入前会清空文件，写入文件时的默认值
  - `w+` 以读写模式打开文件并进行写入，如果不存在则创建文件，写入前会清空文件
    - 如果文件已经被其它进程打开时
      - `w` 模式下无法打开该文件
      - `w+` 模式下可以打开该文件
  - `r` 打开文件读取，读取文件时的默认值
  - `r+` 打开文件进行读写
  - `a` 以写入模式打开文件并进行追加（`append`）写入，将流放在文件末尾，如果不存在则创建文件
  - `a+` 以读写模式打开文件并进行追加写入，将流放在文件末尾，如果不存在则创建文件

##### 文件描述符(File descriptors)

- 什么是文件描述符

  - 在常见的操作系统上，对于每个进程，内核都维护着一张当前打开着的文件和资源的表格
  - 每个打开的文件都分配了一个称为文件描述符的简单的数字标识符
  - 在系统层，所有文件系统操作都使用这些文件描述符来表示和追踪每个特定的文件
  - `Windows` 系统使用了一个虽然不同但概念上类似的机制来跟踪资源

- 为了简化用户的工作，`Node.js` 抽象出操作系统之间的特定差异，并为所有打开的文件分配一个数字型的文件描述符

- `fs.open()` 方法用于分配新的文件描述符

  - 一旦被分配，则文件描述符可用于从文件读取数据、向文件写入数据或请求关于文件的信息

  - 示例

    ~~~javascript
    const fs = require('fs')
    
    // 通过文件描述符打开文件
    fs.open('./HW.txt', (err, fd) => {
      if (err) {
        console.log('打开文件失败：', err)
      }
    
      console.log(fd) // fd 就是文件描述符
    
      // 之后便可以对该文件进行操作
      // 获取文件信息
      fs.fstat(fd, (err, state) => {
        if (err) return
        console.log(state) // 打印文件信息
    
        // 通过 fs.open 打开的文件不会自动关闭，需手动关闭
        fs.close(fd)
      })
    })
    ~~~
  
- 在 `Node.js` 中，文件描述符是一个非负整数，用于标识打开的文件或其他 `I/O` 资源。文件描述符从3开始，是因为在每个 `Node.js` 进程启动时，前三个文件描述符已经被预留给了标准输入（`stdin`）、标准输出（`stdout`）和标准错误输出（`stderr`）。这三个文件描述符分别是0、1和2。

  - 文件描述符为0的是标准输入（`stdin`），用于接收用户的输入。
  - 文件描述符为1的是标准输出（`stdout`），用于输出程序的正常结果。
  - 文件描述符为2的是标准错误输出（`stderr`），用于输出程序的错误信息。

  因此，从文件描述符3开始，可以用于打开其他文件或进行其他I/O操作。这样的设计可以确保标准输入、标准输出和标准错误输出在程序中始终保持可用，而不会被其他文件操作所覆盖。

  需要注意的是，文件描述符的具体值可能会根据操作系统和环境而有所不同。在不同的操作系统中，文件描述符的分配规则可能会有所差异。但通常情况下，文件描述符从3开始是一个常见的约定。

##### 文件夹操作

- `fs.mkdir(path[, options], callback)`

  - 创建一个新文件夹

- `fs.readdir(path[, options], callback)`

  - 读取文件夹内容

  - 示例

    ~~~javascript
    const fs = require('fs')
    
    // 遍历文件夹中的所有文件名
    function readDirectory(path) {
      fs.readdir(path, { withFileTypes: true }, (err, files) => {
        // files 是一个数组
        // files 中默认包含的是目录中文件的文件名
        // 当开启 withFileTypes 选项时，files 中包含的是目录中文件的信息
        files.forEach(item => {
          if (item.isDirectory()) {
            readDirectory(`${path}/${item.name}`)
          } else {
            console.log(item.name)
          }
        })
      })
    }
    
    readDirectory('./foo')
    ~~~

- `fs.rename(oldPath, newPath, callback)`

  - 对文件或文件夹进行重命名，根据 `newPath` 的不同，也可将其进行移动

#### Stream

- `Stream` 在编程中通常翻译为流

- 程序中的流的含义为，当从一个文件中读取数据时，文件的字节数据源源不断地读取到程序中

  - 流是连续字节的一种表现形式和抽象概念

- 所有的流都是 `EventEmitter` 的实例

- `Node.js` 中有四种基本流类型

  - `Readable`

    - 可读流

    - 可以从中读取数据的流
    - 例如 `fs.createReadStream()`

  - `Writable`

    - 可写流

    - 可以向其写入数据的流
    - 例如 `fs.createWriteStream()`

  - `Duplex`

    - 双工流

    - 同时具有可读和可写的流
    - 例如 `net.Socket`

  - `Transform`

    - 转换流

    - 可以在写入和读取数据时修改或转换数据的流
    - 例如数据转换、数据过滤、解压缩等操作
    - 例如 `zlib.createDeflate()`

- `fs.createReadStream(path[, options])`

  - `options` 对象属性

    - `start` 文件读取开始的位置
    - `end` 文件读取结束的位置
    - `highWaterMark` 一次性读取字节的长度，即缓冲区大小，默认是 `64 * 1024` 即 `64KB`
    - [fs 文件系统 | Node.js API 文档 (nodejs.com.cn)](http://www.nodejs.com.cn/api/fs.html#fscreatereadstreampath-options)

  - 返回一个新的可读流实例

  - 使用示例

    ~~~javascript
    const fs = require('fs')
    
    // 创建可读流，存储返回的实例
    const readStream = fs.createReadStream('./foo.txt', { start: 8, end: 20, highWaterMark: 4 })
    
    // readStream 是 EventEmitter 的实例
    // 使用 readStream 监听 data 事件，获取读到的数据
    readStream.on('data', data => {
      console.log(data.toString())
    
      readStream.pause() // 暂停读取流
    
      setTimeout(() => {
        readStream.resume()
      }, 500) // 0.5s 后恢复读取流
    })
    
    // 可读流的其它事件监听
    readStream.on('open', fd => {
      console.log('fd 是文件描述符：', fd)
      console.log('通过可读流将文件打开，open 事件发生在 data 事件之前')
    })
    
    readStream.on('end',()=>{
      console.log('文件读取结束')
    })
    
    readStream.on('close', () => {
      console.log('文件被关闭')
    })
    ~~~

- `fs.createWriteStream(path[, options])`

  - `options` 对象属性

    - [fs 文件系统 | Node.js API 文档 (nodejs.com.cn)](http://www.nodejs.com.cn/api/fs.html#fscreatewritestreampath-options)

  - 返回一个新的可写流实例

  - 使用示例

    ~~~javascript
    const fs = require('fs')
    
    const writeStream = fs.createWriteStream('./foo.txt', {
      flags: 'a+', // flags 值为 a+ 时，start 选项会被忽略
    })
    
    // 使用 write 方法写入
    writeStream.write('Hello', err => {
      if (err) {
        console.log('写入失败')
        return
      }
      console.log('写入成功')
      // 写入流不会自动关闭，需要手动关闭
      writeStream.close()
      // 事件关闭后会发出一个 finish 事件
      // 注意 close 方法不能在 write 方法和 end 方法之前的同步代码中调用
      // 但可以像这样在回调函数中调用，当监听到 write 或 end 方法全部执行结束后，会关闭文件，close 方法本身是异步的
      // 这里写 close 只是示例这样写不会报错，一般不这么用，而是在最后一次 write 之后同步调用 close 来关闭，或者最后一次写入直接调用 end
    })
    
    // 可监听 open 事件
    writeStream.on('open', fd => {
      console.log('通过可写流将文件打开：', fd)
    })
    
    // 可监听 finish 事件
    writeStream.on('finish', () => {
      console.log('文件写入结束')
    })
    
    // 如果使用 end 方法，则代表先进行 write 操作，之后立即调用 close 方法，因此 end 方法之后不能再同步调用 write 或 end
    writeStream.end('World', err => {
      if (err) {
        console.log('写入失败')
        return
      }
      console.log('写入成功')
    })
    
    ~~~

- 将读取到的输入流放到输出流中进行写入

  - 手动写入

    ~~~javascript
    const fs = require('fs')
    
    const readStream = fs.createReadStream('./foo.txt')
    const writeStream = fs.createWriteStream('./foo copy.txt')
    
    readStream.on('data', data => {
      writeStream.end(data, err => {
        if (err) console.log(err)
      })
    })
    ~~~

  - 使用 `pipe` 方法

    ~~~javascript
    const fs = require('fs')
    
    const readStream = fs.createReadStream('./foo.txt')
    const writeStream = fs.createWriteStream('./foo copy.txt')
    
    readStream.pipe(writeStream)
    ~~~

#### FileHandle

- `FileHandle` 类是用于表示打开文件的句柄的类

- `FileHandle` 的实例对象是数字文件描述符的对象封装

- 所有 `FileHandle` 对象都是 `EventEmitter` 的实例

- `FileHandle` 对象的实例通过 `fs.promise.open()` 方法创建

  ~~~javascript
  const fs = require('fs').promises;
  
  async function openFile() {
    const fileHandle = await fs.open('path/to/file', 'r')
    // 在这里可以使用fileHandle进行文件操作
  }
  
  openFile()
  ~~~

- `FileHandle` 对象具有一系列方法，可以用于对文件进行操作

  - [fs 文件系统 | Node.js API 文档 (nodejs.com.cn)](http://www.nodejs.com.cn/api/fs.html#class-filehandle)

### events 模块

- `Node` 中的核心 `API` 都是基于异步事件驱动的

  - 在这个体系中，某些事件发射器（`Emitters`）发出某一个事件
  - 监听器 （`Listeners`）可以监听这个事件，并且传入回调函数，回调函数会在监听到事件时调用

- 发出事件和监听事件都是通过 `EventEmitter` 的实例来完成的

  ~~~javascript
  const { EventEmitter } = require('events')
  const emitter = new EventEmitter()
  ~~~

- 发出事件与监听事件

  - `emitter.on(eventName, listener)`
    - 监听事件，也可以使用 `addListener`
  - `emitter.off(eventName, listener)`
    - 移除事件监听，也可以使用 `removeListener`
  - `emitter.emit(eventName[, ...args])`
    - 发出事件，可以携带一些参数

- 监听方法补充

  - `emitter.once(eventName, listener)`
    - 事件监听一次
  - `emitter.prependListener(eventName, listener)`
    - 将监听事件添加到最前面
  - `emitter.prependOnceListener(eventName, listener)`
    - 将监听事件添加到最前面，监听一次
  - `emitter.removeAllListeners([eventName])`
    - 不传参时移除所有监听器
    - 传入事件名组成的数组时，移除这些事件的监听器

- 获取监听器信息的方法
  - `emitter.eventNames()`
    - 返回当前 `EventEmitter` 对象注册的事件字符串数组
  - `emitter.setMaxListeners(n)`
    - 设置当前 `EventEmitter` 对象的最大监听器数量，默认为 `10`
  - `emitter.getMaxListeners()`
    - 返回当前 `EventEmitter` 对象的最大监听器数量
  - `emitter.listenerCount(eventName)`
    - 返回当前 `EventEmitter` 对象某一个事件名称的监听器个数
  - `emitter.listeners(eventName)`
    - 以数组形式返回当前 `EventEmitter` 对象某个事件监听器上所有的监听器

### Buffer

- `Buffer` 是 `Node` 提供的一个全局类
  - 它的实例可以看成是一个存储二进制的数组
  - 数组中的每一项可以保存8位二进制，对应一个字节的大小
  - 为什么是8位
    - 计算机中，一位二进制存储的数据非常有限，所以通常会将8位二进制合在一起作为一个单元，这个单元称之为一个字节（`byte`）
    - `1byte = 8bit`，`1kb = 1024 byte`，`1M = 1024kb`
    - 很多编程语言中 `int` 类型是4个字节，`long` 类型是8个字节
    - `TCP` 传输的是字节流，在写入和读取时都需要说明字节的个数
    - `RGB` 的每个值的范围是 `0-255`，也是用一个字节存储的

- 传入字符串
  - `Buffer.from(str[, encoding])`
    - 默认编码格式为 `utf-8`

- 创建一块新内存，默认每个位都是0

  - `Buffer.alloc(size[, fill[, encoding]])`

  - 示例

    ~~~javascript
    const buf = Buffer.alloc(8)
    
    console.log(buf) // <Buffer 00 00 00 00 00 00 00 00>
    
    console.log(buf[0]) // 访问 buf 的第一个字节
    
    buf[0] = 0x66 // 对 buf 的第一个字节进行操作
    ~~~

### http 模块

- 在 `Node.js` 中，将 `web` 服务器的资源返回给浏览器，主要是通过 `http` 模块

- 基本使用

  ~~~javascript
  const http = require('http')
  
  const HTTP_PORT = 8080
  
  const server = http.createServer((req, res) => {
    res.end('Hello HTTP Server')
    // req 对象本质是一个读取流，包含本次客户端请求的所有信息
    // res 对象本质是一个写入流，用于给客户端返回结果
  })
  
  server.listen(HTTP_PORT, () => {
    console.log(`🚀服务器已在 ${HTTP_PORT} 端口启动`)
  })
  ~~~

- `listen` 函数有三个参数

  - `port`
    - 端口，如果不传则系统默认分配
    - 按照 `IANA`（互联网数字分配局，`Internet Assigned Numbers Authority`）的定义，端口号被分为三个范围：
      1. 知名端口（`Well-known ports`）：范围是 `0-1023`。这些端口主要由著名的网络服务和协议所使用，例如 `HTTP`（端口 `80`）、`HTTPS`（端口 `443`）和 `FTP`（端口 21）等。
      2. 注册端口（`Registered ports`）：范围是 `1024-49151`。这些端口主要用于较为广泛的、非知名的应用程序。可以在官方的 `IANA` 列表中查找它们的分配情况。例如：
         - `MySQL`：端口 `3306`
         - `PostgreSQL`：端口 `5432`
         - `MongoDB`：端口 `27017`
      3. 动态或私有端口（`Dynamic or private ports`）：范围是 `49152-65535`。这些端口主要用于临时的、短暂的连接，或者作为私有和定制端口。一般情况下，操作系统会根据需求自动从这个范围内分配。例如，某些 `P2P` 应用程序和临时的网络服务使用的是这个范围内的端口。
  - `host` （可选）
    - 主机地址
    - 通常可以传
      - `localhost`
      - `127.0.0.1`
      - `0.0.0.0` 默认
  - `callback`

#### request 对象

- 常用属性

  - `request.url`
    - 本次请求的 `URL`
  - `request.method`
    - 本次请求的方式

  - `request.headers`
    - 本次请求的请求头

- 处理不同路径

  ~~~javascript
  const server = http.createServer((req, res) => {
    const urlString = req.url
    switch (urlString) {
      case '/login':
        res.end('登录成功')
        break
      case '/products':
        res.end('products')
        break
      default:
        res.end('error message')
    }
  })
  ~~~

##### URL 解析

- 使用 `url` 模块的 `parse` 方法解析 `URL`

  ~~~javascript
  const http = require('http')
  const url = require('url')
  
  const server = http.createServer((req, res) => {
    const urlString = req.url
    const urlInfo = url.parse(urlString)
    console.log(urlInfo.query) // query 参数
    console.log(urlInfo.pathname) // 路径名
    res.end()
  })
  ~~~

- `query` 参数解析

  - 使用 `querystring` 模块的 `parse` 方法解析 `query` 参数

    ~~~javascript
    const http = require('http')
    const url = require('url')
    const qs = require('querystring')
    
    const server = http.createServer((req, res) => {
      const urlString = req.url
      const { query } = url.parse(urlString)
      console.log(qs.parse(query))
      res.end()
    })
    ~~~

  - 也可用 `ES6` 内置类 `URLSearchParams` 解析 `query` 参数

    - 使用 `URLSearchParams` 解析已有 `query` 参数

      ~~~javascript
      // 假设当前URL为 https://example.com/search?q=apple&page=1
      
      // 创建一个URLSearchParams对象，将当前URL中的查询参数解析到对象中
      const params = new URLSearchParams(window.location.search);
      
      // 获取特定参数的值
      console.log(params.get('q')); // 输出: "apple"
      console.log(params.get('page')); // 输出: "1"
      
      // 获取所有参数的名称和值
      params.forEach((value, name) => {
        console.log(`${name}: ${value}`);
      });
      // 输出:
      // q: apple
      // page: 1
      ~~~

    - 使用 `URLSearchParams` 创建 `query` 参数

      ~~~javascript
      // 创建一个URLSearchParams对象
      const params = new URLSearchParams();
      
      // 添加查询参数
      params.append('q', 'apple');
      params.append('page', '1');
      
      // 获取特定参数的值
      console.log(params.get('q')); // 输出: "apple"
      
      // 修改参数值
      params.set('page', '2');
      
      // 删除参数
      params.delete('q');
      
      // 将URLSearchParams对象转换为字符串
      const queryString = params.toString();
      console.log(queryString); // 输出: "page=2"
      
      // 构建完整的URL
      const url = 'https://example.com/search?' + queryString;
      console.log(url); // 输出: "https://example.com/search?page=2"
      ~~~

##### 获取请求体

- 由于客户端发送请求时，请求体的数据可能会非常大，所以 `HTTP` 协议采用了分块传输编码（`Chunked Transfer Encoding`）的方式来传输请求体数据

- 因此请求体无法通过 `request.body` 直接获取

- 服务端需要监听 `request` 对象的 `data` 事件和 `end` 事件来获取和处理请求体数据

- 示例

  ~~~javascript
  const server = http.createServer((req, res) => {
    let body = ''
    req.setEncoding('utf8') // 将获取到的请求体转换为 utf-8 编码
    req.on('data', data => {
      body += data
    })
    req.on('end', () => {
      console.log(body)
    })
    res.end()
  })
  ~~~

  ~~~javascript
  const server = http.createServer((req, res) => {
    let bodyBuffers = []
    req.on('data', data => {
      bodyBuffers.push(data)
    })
    req.on('end', () => {
      body = Buffer.concat(bodyBuffers).toString() // 合并 Buffers 再转换成字符串
      console.log(body)
    })
    res.end()
  })
  ~~~

- 获取请求体后，是 `JSON` 格式则用 `JSON.parse()` 解析，如果是 `urlencoded` 格式则用 解析 `query` 参数的方式解析

##### method

- 在 `Restful API` 设计风格中，对于数据的增删改查应该通过不同的请求方式
  - `GET` 查询数据
  - `POST` 新建数据
  - `PATCH` 更新数据
  - `DELETE` 删除数据

##### 请求头

- `request.headers` 中包含了请求头信息
- 信息内容
  - `content-type`
    - 本次请求携带的请求体数据的类型
    - 常见值
      - `application/x-www-form-urlencoded`  表示数据被编码成以 `&` 分隔的键值对，同时以 `=` 分隔键值对
      - `application/json` 表示是 `json` 类型
      - `text/plain` 表示是文本类型
      - `application/xml` 表示是 `xml` 类型
      - `multipart/form-data` 表示是上传文件
  - `content-length`
    - 数据的总长度
  - `keep-alive`
    - 保持连接
    - `http` 是基于 `TCP` 协议的，但是通常在进行一次请求和响应结束后立刻中断
    - 在 `http 1.0` 中，如果想继续保持连接
      - 浏览器需要在请求头中，服务器需要在响应头中分别添加 `connection: keep-alive`
      - 当客户端再次放请求时，就会使用同一个连接，直到一方中断连接
    - 在 `http 1.1` 中，所有连接默认是 `keep-alive` 的
      - 不同的 `Web` 服务器会有不同的保持 `keep-alive` 的时间
      - `Node` 中默认是 5s
  - `accept-encoding`
    - 告知服务器，客户端支持的文件压缩格式
    - 比如 `JS` 文件可以使用 `gzip` 编码，对应 `.gz` 文件
  - `accept`
    - 告诉服务器，客户端可接受文件的格式类型
  - `user-agent`
    - 客户端相关的信息

#### response 对象

##### 返回响应结果

- 如果希望给客户端响应结果数据，可以调用 `response.write` 或 `response.end`，否则客户端会一直等待结果，直至等到请求超时时间
  - `response` 对可写流进行了重写，不允许调用 `close`，最后一次返回数据时调用 `end`

##### HTTP 状态码

- `HTTP` 状态码（`HTTP Status Code`）是用来表示 `HTTP` 响应状态的数字代码

- [HTTP 响应状态码 - HTTP | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/web/http/status)

- 常见 `HTTP` 状态码

  | 常见 HTTP 状态码 | 状态描述              | 信息说明                                           |
  | ---------------- | --------------------- | -------------------------------------------------- |
  | 200              | OK                    | 客户端请求成功                                     |
  | 201              | Created               | POST 请求，创建新的资源                            |
  | 301              | Moved Permanently     | 请求资源的 URL 已经修改，响应中会给出新的 URL      |
  | 400              | Bad Request           | 客户端错误，服务器无法或者不进行处理               |
  | 401              | Unauthorized          | 未授权的错误，必须携带请求的身份信息               |
  | 403              | Forbidden             | 客户端没有权限访问，被拒接                         |
  | 404              | Not Found             | 服务器找不到请求的资源                             |
  | 500              | Internal Server Error | 服务器遇到了不知道如何处理的情况                   |
  | 503              | Service Unavailable   | 服务器不可用，可能处于维护或重载状态，暂时无法访问 |

- 响应状态码的两种方式

  - 为 `response.statusCode` 赋值数字类型
  - 调用 `response.writeHead(statusCode[, statusMessage][, headers])`

##### 响应头

- 返回头部信息，主要有两种方式
  - `res.setHeader(name, value)`
    - 一次写入一个头部信息
  - `response.writeHead(statusCode[, statusMessage][, headers])`
    - 设置所有 `header` 信息
- `Content-Type`
  - 表示响应数据的类型以及数据的编码格式
  - `"Content-Type": "application/json;charset=utf8"`
    - 表示响应数据为 `json` 类型，采用 `utf-8` 编码格式

#### 在服务器中发送网络请求

- 使用 `http` 模块发送请求示例

  ~~~javascript
  const http = require('http');
  
  http.get('http://localhost:8080', res => {
    res.on('data', data => {
      console.log(data.toString())
    })
  })
  ~~~

  ~~~javascript
  const http = require('http')
  
  const options = {
    hostname: 'localhost',
    port: 8080,
    path: '/api',
    method: 'POST',
    headers: {
      'Content-Type': 'application/json' // 设置请求头中的Content-Type
    }
  }
  
  const req = http.request(options, res => {
    console.log('Response status code:', res.statusCode)
  
    res.on('data', (data) => {
      console.log('Received data:', data.toString())
    })
  })
  
  const requestBody = JSON.stringify({ key: 'value' }) // 将请求体数据转换为JSON字符串
  
  req.write(requestBody) // 写入请求体数据
  req.end() // 结束请求
  ~~~

- `axios` 库可以在浏览器中使用，也可以在 `Node` 中使用

  - 在浏览器中 `axios` 基于 `XMLHttpRequest`
  - 在 `Node` 中 `axios` 基于 `http` 模块