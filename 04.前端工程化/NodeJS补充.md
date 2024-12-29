## Node.js 简介

- `Node.js` 是一个基于 `V8 JavaScript` 引擎的 `Javascript` 运行时环境

- 在 `Node.js` 中还可以进行一些其它操作，比如`文件系统读写`、`网络IO`、`加密文件`、`解压缩文件`等操作

## Node 的输入输出

### Node 程序传递参数

- `Node` 运行 `JS` 文件时可以传入一些参数

- ~~~shell
  node index.js num1=20 num2=30
  ~~~

- 传入的参数在 `process` 内置对象中

- 打印 `process.argv` 得到

  ~~~json
  [
      '/usr/local/bin/node',
      '.../.../index.js',
      'num1=20',
      'num2=30'
  ]
  ~~~

- `argv` 名称的由来

  - 在 `C/C++` 程序中的 `main` 函数中，实际上可以获取到两个参数
    - `argc`
      - `argument counter`，传递参数的个数
      - `argument vector`，传入的具体参数
        - `vector` 是矢量的意思，在程序中表示是一种数据结构
        - 在 `JS` 中是一个数组，里面存储一些参数信息

### Node 的输出

- `console.log()` 最常用的输入内容方式
- `console.clear()` 情况控制台
- `consolr.trace` 打印函数的调用栈
- [Console | Node.js v16.20.2 Documentation (nodejs.org)](https://nodejs.org/dist/latest-v16.x/docs/api/console.html)

### REPL

- `Read-Eval-Print Loop` “读取-求值-输出”循环
- `REPL` 是一个简单的、交互式的编译环境
- 浏览器中的 `Console` 就可以看作是一个 `REPL`
- `Node` 中使用 `REPL`
  - 直接在命令行中输入 `node` 并回车便可进入 `REPL` 模式
  - 按两次 `Ctrl + C` 或输入 `exit` 按回车便可退出 `REPL` 模式

## Node 中的全局对象

### 特殊全局对象

- 特殊全局对象并不是真正的全局对象，实际上是模块中的变量，只是每个模块都有，看起来像是全局对象

- 在命令行交互中不可使用
- 常见的特殊全局对象
  - `__dirname` 获取当前文件所在的路径不带文件名
  - `__filename` 获取当前文件所在的路径带文件名

### 常见的全局对象

- `process`
  - 提供了 `Node` 进程中相关的信息
  - 比如 `Node` 的运行环境、参数信息等
- `console`
  - 提供简单的调试控制台
- 定时器函数
  - `setTimeout`
  - `setInterval`
  - `setImmediate`
  - `process.nextTick`

- `global`
  - `process、console、定时器函数`等都有被放到 `global` 中
  - 在 `node` 中，通过 `var` 定义的变量，只存在于当前模块，不会放到 `global` 中
- `globalThis`
  - `ES11` 中添加的全局标识符，指向全局对象
  - 在 `Node 12` 以上版本中支持，指向 `global`
  - 在浏览器中指向 `window`