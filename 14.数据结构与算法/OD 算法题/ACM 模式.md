`ACM` 模式源自于 `ACM` 国际大学生程序设计竞赛。在这种竞赛中，参赛者需要从头到尾编写完整的程序，包括手动处理输入和输出。

## `NodeJs` 的 `ACM` 处理

### 事件监听方式

~~~javascript
/* JavaScript Node ACM模式 控制台输入获取 */
const readline = require("readline");
 
const rl = readline.createInterface({
  input: process.stdin,
});
 
rl.on("line", (line) => {
  console.log(line);
});
~~~

每当从输入流中读取新行时，该模块底层就会触发 `line` 事件，并且将新行的字符串作为参数传入 `callback` 中。这种方式是一种异步的输入获取方式，会导致每行之间失去联系，即 `callback` 内部不知道当前获取的新行输入是哪一行。

### 异步迭代器方式

`readline.Interface` 实现了异步迭代器接口，用于异步获取输入数据，可以用下面的方法来从异步迭代器中获取输入内容：

~~~javascript
const rl = require("readline").createInterface({ input: process.stdin });
var iter = rl[Symbol.asyncIterator]();
const readline = async () => (await iter.next()).value;
 
void (async function () {
  let line;
  while ((line = await readline())) {
    console.log(line);
  }
})();
~~~

如果环境支持 `ECMA 2018` 语法，也可以使用 `for await` 语法处理异步迭代器：

~~~javascript
const rl = require('readline').createInterface({ input: process.stdin });

void (async function () {
  for await (const line of rl) {
    console.log(line);
  }
})();
~~~

### 定行输入获取

获取定行输入时通常会定义一个全局变量 `lines` 数组，将每次监听获得的输入行缓存进 `lines`，这样 `lines` 数组每个元素的序号就是对应的行数，`lines` 数组的长度就是一共输入了几行。

比如以下输入要求：

> 第一行输入整数 `n`
>
> 第二行输入 `n` 个整数，以逗号分隔

~~~javascript
/* JavaScript Node ACM模式 控制台输入获取 */
const readline = require("readline");
 
const rl = readline.createInterface({
  input: process.stdin,
});
 
const lines = [];
rl.on("line", (line) => {
  lines.push(line);
 
  if (lines.length == 2) {
    const n = parseInt(lines[0]);
    const arr = lines[1].split(",").map(Number);
 
    console.log(n);
    console.log(arr);
 
    lines.length = 0;
  }
});
~~~

由于一共会输入两行，因此当 `lines` 数组长度达到 `2` 时，说明当前用例已经输入完成，其中 `lines[0]` 就是第一行输入，`lines[1]` 就是第二行输入。在回调函数的最后，清空 `lines` 数组，使得可以进行下一组用例的测试。

### 不定行输入获取

当输入行数量不确定时，需要利用循环不停地获取输入，让循环结束的操作一般有两种：

1. 输入行为空（对于新 `OJ` 系统一般不适用）；
2. 识别 `EOF` 信号。

`EOF` 是 `end of line` 的缩写，表示文字流（`stream`）的结尾。这里的文字流可以是文件（`file`），也可以是标准输入（`stdin`）。如何产生 `EOF`：

- 在线 `OJ` 系统从文件中读取输入数据，读取到文件末尾时会自动带上 `EOF` 标志；
- 本地 `IDE` 的控制台当输入完数据后，另起一行，继续键入 `ctrl + d` 来产生 `EOF` 标志。

当 `readline.Interface` 接收到 `EOF` 信号后会出发 `close` 事件，并且它的异步迭代器会变成完成状态。

