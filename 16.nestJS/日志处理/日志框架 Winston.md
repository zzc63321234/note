`Winston` 是 `Node.js` 上流行的日志框架。

## 基本使用

~~~bash
npm i winston
~~~

~~~javascript
const winston = require('winston');

// 创建 logger 实例
const logger = winston.createLogger({
  level: 'debug', // 指定打印的日志级别
  format: winston.format.simple(), // 指定日志格式
  // 指定日志传输方式
  transports: [
    // 指定 Console 传输方式，日志会在控制台打印
    new winston.transports.Console(),
    // 指定 File 传输方式，日志会添加到指定文件行尾
    new winston.transports.File({
      dirname: 'log',
      filename: 'test.log',
      maxsize: 1024, // 指定文件最大字节数，winston 会自动为其分文件保存，如果单次内容大于该字节数，则无法成功保存
    }),
  ],
});

logger.info('这是一条 info 级别的日志');
logger.error('这是一条 error 级别的日志');
logger.debug('这是一条 debug 级别的日志');
~~~

使用 `Node` 运行以上代码，日志会在 `Node` 控制台输出，并会写道对应文件的末尾。

## `Transport`

[winston/docs/transports.md at 3943c4132af86a8843459582b1316bf0cc40d459 · winstonjs/winston (github.com)](https://github.com/winstonjs/winston/blob/HEAD/docs/transports.md#winston-core)

`Winston` 中的 `Transport` 允许开发者定义日志信息的存储方式和位置。每个 `Winston` 实例可以配置多个 `Transport`，这些 `Transport` 可以根据日志级别不同，将日志存储到不同位置。例如：错误日志可能存储在远程数据库中，所有日志输出到控制台或本地文件。

`winston` 内建了四个 `Transport`：`Console`、`File`、`Http`、`Stream`。也有一些 `winston` 贡献者维护的，或社区支持的 `Transport`。

### `DailyRotateFile`

`DailyRotateFile` 是 `winston` 贡献者维护的 `Transport`，支持按不同时间将日志保存到不同的文件中。

~~~bash
npm i winston-daily-rotate-file
~~~

~~~javascript
// import winston from 'winston';
// import 'winston-daily-rotate-file';
const winston = require('winston');
require('winston-daily-rotate-file');

const logger = winston.createLogger({
  level: 'debug', // 指定全局默认的打印的日志级别（Transport 中的 level 字段可以单独设置）
  format: winston.format.simple(), // 指定日志格式
  // 指定日志传输方式
  transports: [
    // 指定 Console 传输方式，日志会在控制台打印
    new winston.transports.Console(),
    // 将日志按日期保存到对应文件中
    new winston.transports.DailyRotateFile({
      level: 'info', // 指定要保存的日志级别，如果不指定则使用全局 level 字段
      dirname: 'log',
      filename: 'test-%DATE%.log', // 指定保存的文件名，%DATE% 是日期占位符，如果不使用占位符，则默认会在文件名末尾使用 .%DATE%
      datePattern: 'YYYY-MM-DD HH:mm', // 指定日期格式，默认为 YYYY-MM-DD，这里精确到分钟，即不同分钟的日志会被分开保存
    }),
  ],
});

logger.info('这是一条 info 级别的日志');
logger.error('这是一条 error 级别的日志');
logger.debug('这是一条 debug 级别的日志');
~~~

### `Http`

`Http` 是 `winston` 内建的 `Transport`，可以将日志作为请求体通过 `Http` 的 `Post` 请求发送到指定服务器。

例如在 `Nest` 中定义一个 `Handler` 以打印接收的请求体：

~~~typescript
  @Post('log')
  log(@Body() body) {
    console.log('收到了 winston 通过 Http 发送的日志：');
    console.log(body);
  }
~~~

通过 `winston` 发送 `Http` 请求：

~~~javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'debug',
  format: winston.format.simple(),
  transports: [
    new winston.transports.Http({
      host: 'localhost',
      port: '3000',
      path: '/log',
    }),
  ],
});

logger.info('这是一条 info 级别的日志');
logger.error('这是一条 error 级别的日志');
logger.debug('这是一条 debug 级别的日志');
~~~

运行 `winston` 代码，`Nest` 的控制台会打印如下内容：

~~~
收到了 winston 通过 Http 发送的日志：
{ level: 'info', message: '这是一条 info 级别的日志' }
收到了 winston 通过 Http 发送的日志：
{ level: 'error', message: '这是一条 error 级别的日志' }
收到了 winston 通过 Http 发送的日志：
{ level: 'debug', message: '这是一条 debug 级别的日志' }
~~~

### 动态增删 `Transport`

`logger` 实例可以通过 `add()`、`remove()` 方法来动态地增删 `Transport`，通过 `clear()` 方法可以清空 `Transport`：

~~~javascript
const winston = require('winston');

const consoleTransport = new winston.transports.Console();
const fileTransport = new winston.transports.File({
  level: 'info',
  dirname: 'log',
  filename: 'test.log',
});

const logger = winston.createLogger({
  level: 'debug',
  format: winston.format.simple(),
  transports: [consoleTransport],
});

logger.remove(consoleTransport);
logger.add(fileTransport);

logger.info('这是一条 info 级别的日志');
logger.error('这是一条 error 级别的日志');
logger.debug('这是一条 debug 级别的日志');

logger.clear();
~~~

## 日志级别

### 级别排序

`winston` 中的日志级别遵循由 `RFC5424` 指定的严重性排序：序号越低的级别优先级越高。

以下是 `syslog levels` 的级别排序：

~~~json
{
  "emerg": 0,
  "alert": 1,
  "crit": 2,
  "error": 3,
  "warning": 4,
  "notice": 5,
  "info": 6,
  "debug": 7
}
~~~

以下是 `npm logging levels` 的级别排序：

~~~json
{
  "error": 0,
  "warn": 1,
  "info": 2,
  "http": 3,
  "verbose": 4,
  "debug": 5,
  "silly": 6
}
~~~

如果没有为 `winston` 指定使用的日志级别，则默认使用 `npm` 级别。

### 使用日志级别

`winston` 的 `logger` 实例上可以通过两种方式指定发布的日志级别：

~~~javascript
// 1. 在 logger.log() 中分别指定日志级别字符串和日志消息
logger.log('info', '这是一条 info 级别的日志');

// 2. 使用 logger 实例上定义的指定级别的方法
logger.info('这是一条 info 级别的日志)
~~~

创建 `logger` 实例时传递给 `winston.createrLogger()` 方法的配置对象中，以及创建 `Transport` 实例时传入的配置对象中的 `level` 字段都可以定义要处理的消息的最大级别。

### 自定义日志级别

`winston` 中默认使用 `npm` 级别排序，还可以切换为预定义的 `syslog` 和 `cli` 级别，也可以自定义级别排序：

~~~javascript
const myCustomLevels = {
  levels: {
    foo: 0,
    bar: 1,
    baz: 2,
    foobar: 3
  },
  colors: {
    foo: 'blue',
    bar: 'green',
    baz: 'yellow',
    foobar: 'red'
  }
};

const customLevelLogger = winston.createLogger({
  levels: myCustomLevels.levels
});

customLevelLogger.foobar('一些 foobar 级别的消息');
~~~

## 日志格式

创建 `logger` 实例时，配置对象的 `format` 字段可以指定日志格式。另外，`Transport` 的配置对象中也可以通过 `format` 字段单独指定日志格式。

默认使用的是 `json` 格式，一般在写入文件时使用：

~~~javascript
{
  format: winston.format.json()
}
~~~

~~~json
{"level":"info","message":"这是一条 info 级别的日志"}
{"level":"error","message":"这是一条 error 级别的日志"}
{"level":"debug","message":"这是一条 debug 级别的日志"}
~~~

`prettyPrint` 格式在 `json` 格式的基础上加了一些空格或换行符：

~~~javascript
{
  format: winston.format.prettyPrint()
}
~~~

~~~json
{ message: '这是一条 info 级别的日志', level: 'info' }
{ message: '这是一条 error 级别的日志', level: 'error' }
{ message: '这是一条 debug 级别的日志', level: 'debug' }
~~~

`simple` 格式会简单地显示日志级别和消息，一般在控制台打印时使用：

~~~javascript
{
  format: winston.format.simple()
}
~~~

~~~log
info: 这是一条 info 级别的日志
error: 这是一条 error 级别的日志 
~~~

也有一些格式必须和其它格式组合在一起使用，例如 `timestamp` 格式需和基础格式一起使用，使用 `winston.format.combine()` 方法组合多个格式：

~~~javascript
{
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.prettyPrint() // 注意基础格式的顺序必须在最后
  )
}
~~~

~~~json
{
  message: '这是一条 info 级别的日志',
  level: 'info',
  timestamp: '2024-02-02T07:34:57.146Z'
}
{
  message: '这是一条 error 级别的日志',
  level: 'error',
  timestamp: '2024-02-02T07:34:57.148Z'
}
{
  message: '这是一条 debug 级别的日志',
  level: 'debug',
  timestamp: '2024-02-02T07:34:57.149Z'
}
~~~

还可以加上 `label`：

~~~javascript
{
  format: winston.format.combine(
    Winston。format.label({label: '标签'})
    winston.format.timestamp(),
    winston.format.prettyPrint()
  )
}
~~~

~~~json
{
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.prettyPrint() // 注意基础格式的顺序必须在最后
  )
}
~~~

可以为 `simple` 格式的日志加上色彩，色彩会变成 `ANSI` 转义序列加入文本中，在支持的终端中可以显示颜色：

~~~javascript
{
  format: winston.format.combine(
    winston.format.colorize(),
    winston.format.simple()
  )
}
~~~

~~~log
[32minfo[39m: 这是一条 info 级别的日志
[31merror[39m: 这是一条 error 级别的日志
~~~

## 捕获错误日志

`winston` 支持通过向 `exceptionHandlers` 字段中传入 `Transport` 以处理未捕获的错误或 `Promise` 中未捕获的异常：

~~~javascript
const winston = require('winston');

const consoleTransport = new winston.transports.Console();
const errorFileTransport = new winston.transports.File({
  dirname: 'log',
  filename: 'error.log',
});

const errlogger = winston.createLogger({
  format: winston.format.simple(),
  transports: [consoleTransport],
  exceptionHandlers: [errorFileTransport],
});

throw new Error('error-test');
~~~

