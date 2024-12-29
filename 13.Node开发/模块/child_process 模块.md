`child_process` 模块提供了创建子进程的功能，可以执行外部命令、脚本文件等，并与其进行交互。

## 创建子进程的方法

### `exec()`

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#child_processexeccommand-options-callback)

`child_process.exec(command[, options][, callback])` 可以衍生一个 `shell`，然后在该 `shell` 中执行语句。例：

~~~javascript
const {exec} = require('child_process')

exec('ls ../',(error,stdout,stderr)=>{
  console.log(error)
  console.log(stdout)
  console.log(stderr)
})
~~~

如果提供了回调函数，则会使用参数 `(error, stdout, stderr)` 调用它：

>  成功后，`error` 将是 `null`。 出错时，`error` 将是 `Error` 的实例。 `error.code` 属性将是进程的退出码。 按照惯例，除 `0` 之外的任何退出码都表示错误。 `error.signal` 将是终止进程的信号。
>
> 传给回调的 `stdout` 和 `stderr` 参数将包含子进程的标准输出和标准错误的输出。 默认情况下，`Node.js` 会将输出解码为 `UTF-8` 并将字符串传给回调。 `encoding` 选项可用于指定用于解码标准输出和标准错误的输出的字符编码。 如果 `encoding` 是 `'buffer'` 或无法识别的字符编码，则 `Buffer` 对象将被传给回调。

可以提供一个配置选项：

> - `signal` 属性可以接收一个 `AbortSignal`。启用后，如果在相应的 `AbortController` 上调用 `.abort()`，则类似在该子进程上调用了 `.kill()`，只是传给回调的错误将是 `AbortError`：
>
>   ~~~javascript
>   const { exec } = require('child_process');
>   const controller = new AbortController();
>   const { signal } = controller;
>   const child = exec('grep ssh', { signal }, (error) => {
>     console.log(error); // 一个 AbortError
>   });
>   controller.abort();
>   ~~~
>
> - 如果设置了 `timeout` 的值大于 `0`，则如果子进程运行时间超过 `timeout` 毫秒，父进程将发送由 `killSignal` 属性（默认为 `'SIGTERM'`）标识的信号。

可以使用 `util.promisify` 使其 `Promise` 化：

~~~javascript
const child_process = require('child_process')
const { exec } = child_process
const util = require('util')

const  exec2 = util.promisify(exec)

exec2('ls ../')
    .then((data)=>{
        console.log(data.stdout)
    })
~~~

`child_process.exec()` 的一个参数如果被注入了，则可能会被执行以外的代码：

> 例如：
>
> ~~~javascript
> const { exec } = child_process
> const util = require('util')
> 
> const  exec2 = util.promisify(exec)
> const userInput = '. && pwd'
> 
> exec2(`ls ${userInput}`).then(data =>{
>     console.log(data.stdout)
> })
> ~~~
>
> 如果上面的 `userInput` 被注入为 `. && rm -rf / `，后果不堪设想。

使用 `execFile()` 可以很好的避免注入问题。

### `execFile()`

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#child_processexecfilefile-args-options-callback)

`child_process.execFile(file[, args][, options][, callback])` 不会衍生 `shell`，而是指定可执行文件直接作为新进程衍生。例：

> ~~~javascript
> const child_process = require('child_process')
> const { execFile } = child_process
> 
> const userInput = '. && pwd'
> 
> execFile('ls',['-la',userInput],(error, stdout, stderr)=>{
>     console.log(stdout)
>     console.log(stderr) // ls: cannot access '. && pwd': No such file or directory
> })
> ~~~
>
> 第一个参数指定要执行的程序。之后的参数必须以数组形式传入，数组中的每一项都会被作为单独的一个参数，因此其中的 `&&` 不会被当作连接符。

使用流的方式接收 `stdout`：

~~~javascript
const child_process = require('child_process')
const { execFile } = child_process

const userInput = '.'

const streams = execFile('ls',['-la',userInput])
streams.stdout.on('data',(chunk)=>{
    console.log(chunk)
})
~~~

可以传入一些选项：

> 常用的有：
>
> - `cwd`：指定子进程的当前工作目录，默认是主进程的当前目录。
>
> - `env`：指定环境变量键值对，默认是 `process.env`。
>
> - `shell`：指定使用的 `shell`。
>
> - `maxBuffer`：
>
>   最大缓存，默认 `1024 * 1024` 字节。
>
>   使用回调的形式有限制，使用流的形式没有限制。

### `spawn()`

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#child_processspawncommand-args-options)

`child_process.spawn(command[, args][, options])` 用法与 `exeFile()` 方法类似，只是不支持回调函数，只能通过流事件获取结果，因此也没有 `maxBuffer` 的限制。

### `fork()`

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#child_processforkmodulepath-args-options)

`child_process.fork(modulePath[, args][, options])` 是 `spawn()` 方法的特例，专用于衍生 `Node.js` 进程。

`fork('./index.js')` 相当于 `spawn('node', ['./index.js'])`。对于 `fork()` 衍生的子进程，会与父进程之间建立 `IPC` （`Inter-Process Communication`）通道，用于在父子进程间进行通信。

实际使用时发现在使用 `ts-node-env` 等运行父程序时，会导致 `fork()` 的执行不符合预期，可以使用 `spawn()` 来代替。为 `spawn()` 传入 `options.stdio` 数组，数组的第四项传入 `'ipc'` 也可实现 `IPC` 通道。

## `ChildProcess` 类

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#class-childprocess)

以上四种创建子进程的方法都会返回一个 `ChildProcess` 实例，表示衍生的子进程。

`ChildProcess` 类继承自 `EventEmitter` 类。

以下将使用 `subprocess` 指代 `ChildProcess` 的实例。

### 事件

`subprocess` 实例的 `on` 方法用于接收子进程的事件。

#### `close` 事件

在子进程已结束并且它的标准输入输出流（`stdio`，包括 `stdin`、`stdout`、`stderr`）已关闭之后，触发 `close` 事件。

回调接受参数：

- `code`：

  子进程的退出码。可选值有：

  - `0`：表示子进程成功结束，没有错误。
  - 非 `0` 数值：表示子进程由于某些错误或问题而结束。
  - `null`：子进程因收到信号而终止。

- `signal`：

  如果子进程因收到信号而终止，则会接受终止信号。否则接受 `null`。常见的信号值包括：

  - `SIGINT`: 通常用于中断一个程序的执行。
  - `SIGTERM`: 请求程序正常退出，是一种优雅的终止方式。
  - `SIGKILL`: 立即终止程序，不能被捕获或忽略。
  - `SIGSTOP`: 停止（暂停）一个进程的执行，不能被捕获或忽略。

例：

~~~javascript
const { spawn } = require('child_process');[^4^][4]
const subprocess = spawn('some-command');

subprocess.on('close', (code, signal) => {
  if (code !== null) {
    console.log(`子进程退出码：${code}`);
  } else {
    console.log(`子进程被信号终止：${signal}`);
  }
});
~~~

#### `exit` 事件

在子进程结束后触发 `exit` 事件。与 `close` 事件不同的是，`exit` 事件触发时，可能子进程的输出流还没有关闭。如果对子进程的输出进行了管道操作或者其它处理，那么 `close` 事件可能会晚于 `exit` 事件被触发。

#### `error` 事件

`error` 事件在以下情况被触发：

- 无法衍生该进程。
- 进程无法终止。
- 向子进程发送消息失败。

#### `disconnect` 事件

调用父进程的 `subprocess.disconnect()` 或子进程中的 `process.disconnect()` 方法后会触发 `disconnect()` 事件。 断开连接后就不能再发送或接收消息，且 `subprocess.connected` 属性变为 `false`。

#### `message` 事件

当子进程中使用 `process.send()` 发送消息时，会触发 `message` 事件。

发送的消息会经过 `JSON` 序列化和解析，结果消息可能与最初发送的消息不同。

回调接受参数：

- `message`：解析的 `JSON` 对象或原始值。
- `sendHandle`：[`net.Socket`](https://www.nodejs.com.cn/api-v14/net.html#net_class_net_socket) 或 [`net.Server`](https://www.nodejs.com.cn/api-v14/net.html#net_class_net_server) 对象、或 `undefined`。

如果在衍生子进程时将 `serialization` 选项设置为 `'advanced'`，则 `message` 参数可以包含 `JSON` 无法表示的数据。 有关更多详细信息，请参阅[高级序列化](https://www.nodejs.com.cn/api-v14/child_process.html#child_process_advanced_serialization)。

#### `spawn` 事件

一旦子进程衍生成功，则会触发 `spawn` 事件。否则触发 `error` 事件。

如果触发，则 `'spawn'` 事件在所有其他事件之前，且在通过 `stdout` 或 `stderr` 接收任何数据之前。

例如一条命令 `bash some-command` 衍生成功，则 `'spawn'` 事件将触发，尽管 `bash` 可能衍生 `some-command` 失败。 

### `subprocess` 其它常用方法

#### `send()`

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#subprocesssendmessage-sendhandle-options-callback)

`subprocess.send(message[, sendHandle[, options]][, callback])`。

当父进程和子进程之间建立了 `IPC` 通道时（即当使用 `child_process.fork()` 时），可以使用 `subprocess.send()` 方法向子进程发送消息。 当子进程是 Node.js 实例时，可以通过 [`'message'`](https://www.nodejs.com.cn/api-v14/process.html#process_event_message) 事件接收这些消息。

例：

> 父进程脚本中：
>
> ~~~javascript
> const cp = require('child_process');
> const n = cp.fork(`${__dirname}/sub.js`);
> 
> n.on('message', (m) => {
>   console.log('PARENT got message:', m);
> });
> 
> // 引起子进程打印：CHILD got message: { hello: 'world' }
> n.send({ hello: 'world' });
> ~~~
>
> 然后子进程脚本 `'sub.js'` 可能如下所示：
>
> ~~~javascript
> process.on('message', (m) => {
>   console.log('CHILD got message:', m);
> });
> 
> // 引起父进程打印：PARENT got message: { foo: 'bar', baz: null }
> process.send({ foo: 'bar', baz: NaN });
> ~~~

#### `disconnect()`

关闭父进程和子进程之间的 IPC 通道，一旦没有其他连接使其保持活动状态，则允许子进程正常退出。 调用此方法后，父子进程中的 `subprocess.connected` 和 `process.connected` 属性（分别）将设置为 `false`，进程之间将无法再传递消息。通常会立即触发 `'disconnect'` 事件。

#### `kill()`

`subprocess.kill([signal])` 方法向子进程发送信号。 如果没有给定参数，则进程将被发送 `'SIGTERM'` 信号。如果 `kill` 成功，则返回 `true`，否则返回 `false`。

### `subprocess` 流式接受数据

[child_process 子进程 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/child_process.html#optionsstdio)

#### `stdio`

该属性对应于传给 `child_process.spawn()` 的 `stdio` 选项:

>  `options.stdio` 接收一个数组，其中每一项对应子进程中的文件描述符。`Node` 中的文件描述符 `0`、`1`、`2` 分别对应于 `stdin`、`stdout`、`stderr`。可以指定额外的文件描述符以在父进程和子进程之间创建额外的管道。每一项的值可以是：
>
> - `pipe`：
>
>   在子进程和父进程之间创建管道。如果前三项使用了 `pipe`，则它们分别作用于 `subprocess.stdin`、`subprocess.stdout` 和 `subprocess.stderr`。
>
> - `overlapped`：
>
>   于 `pipe` 相同，只是在句柄上设置了 `FILE_FLAG_OVERLAPPED` 标志。
>
> - `ipc`：
>
>   创建 `IPC` 管道。设置此选项将启用 `subprocess.send()` 方法。 如果子进程是 `Node.js` 进程，则 `IPC` 通道的存在将启用 [`process.send()`](https://www.nodejs.com.cn/api-v14/process.html#process_process_send_message_sendhandle_options_callback) 和 [`process.disconnect()`](https://www.nodejs.com.cn/api-v14/process.html#process_process_disconnect) 方法，以及子进程中的 [`'disconnect'`](https://www.nodejs.com.cn/api-v14/process.html#process_event_disconnect) 和 [`'message'`](https://www.nodejs.com.cn/api-v14/process.html#process_event_message) 事件。
>
> - `ignore`：
>
>   忽略子进程的文件描述符。 虽然 Node.js 将始终为其衍生的进程打开文件描述符 0、1 和 2，但将文件描述符设置为 `'ignore'` 将导致 Node.js 打开 `/dev/null` 并将其附加到子进程的文件描述符。
>
> - `inherit`：
>
>   通过相应的标准输入输出流传入/传出父进程。 在前三个位置，这分别相当于 `process.stdin`、`process.stdout` 和 `process.stderr`。 在任何其他位置，相当于 `'ignore'`。
>
> - `Stream` 对象：
>
>  与子进程共享引用终端、文件、套接字或管道的可读或可写流。 流的底层文件描述符在子进程中复制到对应于 `stdio` 数组中的索引的文件描述符。 流必须有底层描述符（文件流在 `'open'` 事件发生之前没有）。
>
> - 正整数：
>
>   该整数值被解释为当前在父进程中打开的文件描述符。 它与子进程共享，类似于共享 `Stream` 对象的方式。
>
> - `null`、`undefined`：
>
>   使用默认值。 对于标准输入输出文件描述符 `0`、`1` 和 `2`（换句话说，标准输入、标准输出和标准错误），创建管道。 对于文件描述符 `3` 及以上，默认值为 `'ignore'`。
>
>  `options.stdio` 也可以是如下字符串：
>
> - `'pipe'`: 相当于 `['pipe', 'pipe', 'pipe']`（默认）。
> - `'overlapped'`: 相当于 `['overlapped', 'overlapped', 'overlapped']`。
> - `'ignore'`: 相当于 `['ignore', 'ignore', 'ignore']`。
> - `'inherit'`: 相当于 `['inherit', 'inherit', 'inherit']` 或 `[0, 1, 2]`。

在下面的例子中，只有子进程的文件描述符 `1` (标准输出) 被配置为管道，所以只有进程的 `subprocess.stdio[1]` 是流，数组中的所有其他值都是 `null`：

~~~javascript
const assert = require('assert');
const fs = require('fs');
const child_process = require('child_process');

const subprocess = child_process.spawn('ls', {
  stdio: [
    0, // 为子进程使用父进程的标准输入。
    'pipe', // 将子进程的标准输出 pipe 到父进程的 subprocess.stdout。
    fs.openSync('err.out', 'w'), // 将子进程的标准错误定向到指定文件。
  ]
});

assert.strictEqual(subprocess.stdio[0], null);
assert.strictEqual(subprocess.stdio[0], subprocess.stdin);

assert(subprocess.stdout);
assert.strictEqual(subprocess.stdio[1], subprocess.stdout);

assert.strictEqual(subprocess.stdio[2], null);
assert.strictEqual(subprocess.stdio[2], subprocess.stderr);
~~~

`subprocess.stdio[0]`、`subprocess.stdio[1]`、`subprocess.stdio[1]` 分别与 `subprocess.stdin`、`subprocess.stdout`、`subprocess.stderr` 引用相同的值。

以 `subprocess.stdio[1]` 为例，如果子进程衍生时 `option.stdio[1]` 的值最终对应的 `'pipe'`，那么 `subprocess.stdout` 的值将是 `null`。否则 `subprocess.stdout` 是代表子进程的 `stdout` 的 `Readable Stream`。例：

~~~javascript
const { spawn } = require('child_process');

const subprocess = spawn('ls');

subprocess.stdout.on('data', (data) => {
  console.log(`Received chunk ${data}`);
});
~~~

注意，对于 `child_process.fork()` 方法，如果指定了 `options.stdio`，则必须将其中一项设置为 `'ipc'`。
