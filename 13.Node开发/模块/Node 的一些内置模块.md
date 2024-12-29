## `crypto`

`crypto` 模块用以实现各种密码学功能。

`Hash` 算法示例：

~~~javascript
const crypto = require('crypto')
// 使用 md5 算法
const hash = crypto.createHash('md5')
// 创建哈希值
hash.update('xxx')
// 将哈希值转换为 16 进制
const res = hash.digest('hex')
~~~

对称加密示例：

~~~javascript
const crypto = require('crypto')

const algorithm = 'aes-256-cbc';
const key = crypto.randomBytes(32);
const iv = crypto.randomBytes(16);

function encrypt(text) {
  let cipher = crypto.createCipheriv(algorithm, Buffer.from(key), iv);
  let encrypted = cipher.update(text);
  encrypted = Buffer.concat([encrypted, cipher.final()]);
  return { iv: iv.toString('hex'), encryptedData: encrypted.toString('hex') };
}

function decrypt(text) {
  let iv = Buffer.from(text.iv, 'hex');
  let encryptedText = Buffer.from(text.encryptedData, 'hex');
  let decipher = crypto.createDecipheriv(algorithm, Buffer.from(key), iv);
  let decrypted = decipher.update(encryptedText);
  decrypted = Buffer.concat([decrypted, decipher.final()]);
  return decrypted.toString();
}

const text = '需要加密的数据';
const encryptedText = encrypt(text);
console.log(decrypt(encryptedText));
~~~

`HMAC` 加密示例：

~~~javascript
const crypto = require('crypto')

// 创建HMAC
const hmac = crypto.createHmac('sha256', '密钥');

// 更新数据
hmac.update('需要签名的数据');

// 输出签名
console.log(hmac.digest('hex'));
~~~

## `OS`

`os` 模块提供了于操作系统相关的实用方法和属性。

部分属性方法用法如下：

### `os.homedir()`

返回当前用户的主目录的字符串路径。

在 `POSIX` 上，它使用 `$HOME` 环境变量（如果已定义）。 否则，它使用[有效的 UID](https://en.wikipedia.org/wiki/User_identifier#Effective_user_ID) 来查找用户的主目录。

在 `Windows` 上，它使用 `USERPROFILE` 环境变量（如果已定义）。 否则，它使用当前用户的配置文件目录的路径。

## `path`

`path` 模块提供了用于处理文件和目录的路径的实用工具。

### `Windows` 与 `POSIX`

`path` 模块的默认操作因运行 `Node.js` 应用的操作系统而异。具体来说，当在 `Windows` 操作系统上运行时，`path` 模块将假定正在使用 `Windows` 风格的路径。

例如在 `POSIX` 和 `Windows` 上使用 `path.basename()` 可能会产生不同的结果：

- `Windows` 环境中：

  ~~~java
  path.basename('C:\\temp\\myfile.html');
  // Returns: 'myfile.html' 
  ~~~

- `POSIX` 环境中：

  ~~~javascript
  path.basename('C:\\temp\\myfile.html');
  // Returns: 'C:\\temp\\myfile.html' 
  ~~~

以上在 `POSIX` 环境中解析 `Windows` 文件路径的 `basename` 得到的结果是非预期的。

当使用 `Windows` 文件路径时，如果需要在任何操作系统上获得一致的结果，需使用 `path.win32`：

~~~javascript
path.win32.basename('C:\\temp\\myfile.html');
// Returns: 'myfile.html' 
~~~

当使用 `POSIX` 文件路径时，如果需要在任何操作系统上获得一致的结果，需使用 `path.posix`：

~~~javascript
path.posix.basename('/tmp/myfile.html');
// Returns: 'myfile.html' 
~~~

### `path.dirname()`

`path.dirname(path)` 方法用于返回路径的目录名。

例：

~~~javascript
path.dirname('/foo/bar/baz/asdf/quux');
// Returns: '/foo/bar/baz/asdf' 
~~~

### `path.extname()`

`path.extname(path)` 方法用于返回路径中文件的拓展名。

例：

~~~javascript
path.extname('index.html');
// Returns: '.html'

path.extname('index.coffee.md');
// Returns: '.md'

path.extname('index.');
// Returns: '.'

path.extname('index');
// Returns: ''

path.extname('.index');
// Returns: ''

path.extname('.index.md');
// Returns: '.md' 
~~~

### `path.basename()`

`path.basename(path[, suffix])` 方法用于返回路径的最后一部分。

接受参数：

- `path`：

  路径字符串。

- `suffix`：

  可选，表示要删除的后缀字符串。

示例：

~~~javascript
path.basename('/foo/bar/baz/asdf/quux.html');
// Returns: 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html');
// Returns: 'quux' 
~~~

### `path.parse()`

`path.parse(path)` 方法用于将给定路径解析成一个对象。

返回的对象具有以下属性：

- `root`
- `dir`
- `base`
- `name`
- `ext`

例：

- 在 `POSIX` 上：

  ~~~javascript
  path.parse('/home/user/dir/file.txt');
  // Returns:
  // { root: '/',
  //   dir: '/home/user/dir',
  //   base: 'file.txt',
  //   ext: '.txt',
  //   name: 'file' } 
  ~~~

- 在 `Windows` 上：

  ~~~javascript
  path.parse('C:\\path\\dir\\file.txt');
  // Returns:
  // { root: 'C:\\',
  //   dir: 'C:\\path\\dir',
  //   base: 'file.txt',
  //   ext: '.txt',
  //   name: 'file' } 
  ~~~

### `path.resolve()`

`path.resolve([...paths])` 方法用于将路径或路径片段的序列解析为绝对路径。

解析的步骤为：

- 给定的路径序列从右到左处理，每个后续的 `path` 会被追加到前面，直到构建绝对路径。
- 如果在处理完所有给定的 `path` 片段之后，还没有生成绝对路径，则使用当前工作目录。

如果没有传入 `path` 片段，则 `path.resolve()` 将返回当前工作目录的绝对路径。

例：

~~~javascript
path.resolve('/foo/bar', './baz');
// Returns: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// Returns: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// If the current working directory is /home/myself/node,
// this returns '/home/myself/node/wwwroot/static_files/gif/image.gif'
~~~

### `path.join()`

`path.join([...paths])` 方法用于将多个 `path` 片段连接在一起，然后规范化生成的路径。

例：

~~~javascript
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// Returns: '/foo/bar/baz/asdf'
~~~

### `path.normalize()`

`path.normalize(path)` 方法用于规范化给定的路径字符串，解析其中的 `.` 和 `..` 片段。

例：

~~~javascript
path.normalize('/foo/bar//baz/asdf/quux/..');
// Returns: '/foo/bar/baz/asdf' 

path.normalize('/foo/bar//baz/./asdf/');
// Returns: '/foo/bar/baz/asdf' 
~~~

### `path.relative()`

`path.relative(from, to)` 方法用于返回 `to` 之于 `from` 的相对路径。

例：

~~~javascript
path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb');
// Returns: '../../impl/bbb' 
~~~

### `path.delimiter`

该属性存储当前平台的路径定界符：

- 对于 `Windows` 是 `;`。
- 对于 `POSIX` 是 `:`。

例：

- 在 `Windows` 上：

  ~~~javascript
  console.log(process.env.PATH);
  // Prints: 'C:\Windows\system32;C:\Windows;C:\Program Files\node\'
  
  process.env.PATH.split(path.delimiter);
  // Returns ['C:\\Windows\\system32', 'C:\\Windows', 'C:\\Program Files\\node\\'] 
  ~~~

- 在 `POSIX` 上：

  ~~~javascript
  console.log(process.env.PATH);
  // Prints: '/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin'
  
  process.env.PATH.split(path.delimiter);
  // Returns: ['/usr/bin', '/bin', '/usr/sbin', '/sbin', '/usr/local/bin'] 
  ~~~

### `path.sep`

该属性存储当前平台的路径片段分隔符：

- 对于 `Windows` 是 `\`。
- 对于 `POSIX` 是 `/`。

### 路径相关其它 `API`

`process.cwd()` 方法用于返回 `Node.js` 进程的当前工作目录的字符串。

全局变量 `__dirname` 始终指向当前执行的脚本所在的目录。

## `readline`

`readline` 模块提供了一个接口，用于一次一次地从可读流（例如 `process.stdin`）中读取数据。

基本用法：

~~~javascript
const readline = require('node:readline');
const { stdin: input, stdout: output } = require('node:process');

const rl = readline.createInterface({ input, output });

rl.question('What do you think of Node.js? ', (answer) => {
  // TODO: Log the answer in a database
  console.log(`Thank you for your valuable feedback: ${answer}`);

  rl.close();
});
~~~

### `readline.createInterface()`

创建一个 `readline.Interface` 实例。

接收一个 `options` 对象作为参数。常用的配置项有：

- `input`：必选，表示要收听的可读流。
- `output`：要写入 `readline` 数据的可写流。
- 其它详见 [readline 逐行读取 | Node.js v20 文档 (nodejs.cn)](https://nodejs.cn/api/v20/readline.html#readlinepromisescreateinterfaceoptions)

### `readline.InterfaceConstructor`

`readline.InterfaceConstructor` 继承自 `EventEmitter`，常用的事件如下：

- `close`：

  发生以下情况之一时会触发 `'close'` 事件：

  - `rl.close()` 方法被调用，`InterfaceConstructor` 实例放弃了对 `input` 和 `output` 流的控制；
  - `input` 流接收到它的 `'end'` 事件；
  - `input` 流接收 Ctrl+D 以表示传输结束（EOT）；

- `line`：

  每当 `input` 流接收到行尾输入（`\n`、`\r` 或 `\r\n`）时，则会触发 `'line'` 事件。这通常在用户按 Enter 或 Return 时发生。

  如果从流中读取了新数据并且该流在没有最终行尾标记的情况下结束，也会触发 `'line'` 事件。

  使用包含单行接收输入的字符串调用监听器函数。

  ```js
  rl.on('line', (input) => {
    console.log(`Received: ${input}`);
  }); 
  ```

- 其它详见 [readline 逐行读取 | Node.js v20 文档 (nodejs.cn)](https://nodejs.cn/api/v20/readline.html#类interfaceconstructor)

常用实例属性或方法如下：

- [`rl.write(data[, key])`](https://nodejs.cn/api/v20/readline.html#rlwritedata-key)
- [`rl.close()`](https://nodejs.cn/api/v20/readline.html#rlclose)
- [`rl.prompt([preserveCursor])`](https://nodejs.cn/api/v20/readline.html#rlpromptpreservecursor)、[`rl.setPrompt(prompt)`]([readline 逐行读取 | Node.js v20 文档 (nodejs.cn)](https://nodejs.cn/api/v20/readline.html#rlsetpromptprompt))
- [`rl.line`](https://nodejs.cn/api/v20/readline.html#rlline)

### `readline.Interface` / `readlinePromises.Interface`

`readline.Interface` 和 `readlinePromises.Interface` 都继承自 `readline.InterfaceConstructor`，它们分别是回调接口和 `Promise` 接口的实现。

以下都以 `promise` 接口举例。

常用的实例属性或方法如下：

- [`rl.question(query[, options])`](https://nodejs.cn/api/v20/readline.html#rlquestionquery-options)

### `Readline`

以 `promise` 风格接口为例，使用 [`new readlinePromises.Readline(stream[, options])`]() 创建 `readlinePromises.Readline` 实例。

常用的实例属性或方法如下：

- [`readline.cursorTo(x[, y])`](https://nodejs.cn/api/v20/readline.html#rlcursortox-y)
- [`readline.moveCursor(dx, dy)`](https://nodejs.cn/api/v20/readline.html#rlmovecursordx-dy)
- [`readline.clearLine(dir)`](https://nodejs.cn/api/v20/readline.html#rlclearlinedir)
- [`readline.commit()`](https://nodejs.cn/api/v20/readline.html#rlcommit)

对于 `promise` 风格的 `API`，要么在 `new readlinePromises.Readline(stream[, options])` 时将 `{autoCommit: true}` 传入，否则需要使用 `readline.commit()` 执行操作。

### 实现在输入时使用方向键切换预选值

~~~javascript
const readline = require("readline");
const { stdin: input, stdout: output } = require("process");

// 创建 readline 接口实例
const rl = readline.createInterface({ input, output });

// 预选的输入值数组
const preselectedInputs = ["选项1", "选项2", "选项3"];
let currentIndex = 0; // 当前选中的预选输入值的索引

// 监听 "keypress" 事件来捕获方向键
readline.emitKeypressEvents(process.stdin);
if (process.stdin.isTTY) process.stdin.setRawMode(true);

process.stdin.on("keypress", (str, key) => {
  if (key.ctrl && key.name === "c") {
    process.exit(); // Ctrl+C 退出
  } else if (key.name === "up" || key.name === "down") {
    // 方向键切换预选输入值
    currentIndex += key.name === "up" ? -1 : 1;
    if (currentIndex < 0) currentIndex = preselectedInputs.length - 1;
    if (currentIndex >= preselectedInputs.length) currentIndex = 0;

    // 清除当前行并输出新的预选输入值
    readline.cursorTo(output, 2);
    readline.clearLine(output, 1);
    rl.line = ''
    rl.stdout.write(preselectedInputs[currentIndex]);
  }
});

rl.on("line", (line) => {
  console.log(`接收到的输入: ${line}`);
  rl.close();
});

rl.prompt();
~~~

