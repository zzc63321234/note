## `EventSource API`

`EventSource` 接口是 `Web` 客户端实现接收 `SSE`（`Server-Sent Events`） 通信的接口。

一个 `EventSource` 实例会对 `HTTP` 服务器开启一个持久化的连接，服务器会以 `text/event-stream` 格式发送事件。此连接会一直保持开启，直到通过调用 `eventSource.close()` 关闭。

`SSE` 是单向的，数据消息只能从服务端发送到客户端。

### `EventSource` 构造函数

用法：

~~~javascript
const evtSource = new EventSource(url, configuration);
~~~

接收两个参数：

- `url`  代表远程资源的位置。
- `configuration` 是一个可选配置项。可选项是：
  - `withCredentials`：指示 `CORS` 是否应包含凭据，默认为 `false`。

### 继承

`EventSource` 接口从其父接口 `EventTarget` 继承属性和方法，包括：

- `EventTarget.addEventListener()`
- `EventTarget.removeEventListener()`
- `EventTarget.dispatchEvent()`
- 以及各种 `onevent` 属性

### 实例属性

- `EventSource.readyState`：

  一个代表连接状态的数字。

  可选值有：

  - `0`：代表 `CONNECTING`。
  - `1`：代表 `OPEN`。
  - `2`：代表 `CLOSED`。

- `EventSource.url` 和 `EventSource.withCredentials`：：

  对应 `EventSource` 构造函数中接收的两个参数。

### 实例方法

- `EventSource.close()`：

  用于关闭当前的连接。

  如果调用了此方法，则会将 `EventSource.readyState` 的值设置为 `2`（`CLOSED`）。

### 事件

- `error`：

  在事件源连接未能打开时触发。

- `open`：

  在与事件源的连接打开时触发。

- `message`：

  在从事件源接收到数据时触发。

- 自定义事件：

  事件源本身如果发送了具有 `event` 字段的消息，将创建一个以该值为键的特定事件，以此来代替 `message` 事件。

## `SSE`

`SSE` 是一种在基于浏览器的 `Web` 应用程序中仅从服务器向客户端发送文本消息的技术。`SSE` 基于 `HTTP` 协议中的持久连接，具有有 `W3C` 标准化的网络协议和 `EventSource` 客户端接口，作为 `HTML5` 标准套件的一部分。

要订阅服务器事件，客户端发出 `GET` 请求带有指定的 `header`：

- `Accept: text/event-stream`：表示可接收事件流类型。
- `Cache-Control: no-cache`：禁用任何的事件缓存。
- `Connection: keep-alive`：表示正在使用持久连接。

请求示例：

-  `GET /sse HTTP/1.1 Accept: text/event-stream Cache-Control: no-cache Connection: keep-alive`

服务器应该使用以下响应体来确认订阅：

- `Content-Type: text/event-stream;charset=UTF8`：表示响应内容为事件流类型，编码为 `UTF8`。
- `Transfer-Encoding: chunked`：表示服务器流式传输动态生成的内容，因此内容大小事先未知。

事件流格式：

> 事件流是一个简单的文本数据流，文本应该使用 `UTF-8` 格式的编码。
>
> 事件流中的消息由两个换行符（`\n\n`）分开。
>
> 以 `:` 开头的行为注释行，会被客户端忽略。
>
> 每条消息由一行或多行文字组成，列出该消息的字段。每个字段的表现形式为 “`字段名:字段值`”。
>
> 规范中规定了下面这些字段：
>
> - `event`:
>
>   一个用于标识事件类型的字符串。
>
>   如果指定了这个字符串，浏览器会将具有该值作为事件名称的事件分派给相应的监听器，网站源代码应该使用 `addEventListener()` 来监听指定的事件。如果一个消息没有指定事件名称，那么 `onmessage` 处理程序就会被调用。
>
> - `data`：
>
>   消息的数据字段。
>
>   当 `EventSource` 接收到多个以 `data:` 开头的连续行时，会将它们连接起来，在它们之间插入一个换行符。末尾的换行符会被删除。
>
> - `id`：
>
>   事件 `ID`，是事件的唯一标识符。
>
>   客户端收到后会将其保存为 `Last-Event-ID`。
>
>   如果连接丢失，客户端会自动重新连接，并发送  `Last-Event-ID`，这样服务器就能直到客户端最后接收到的是哪个事件，从而可以从那个事件之后开始发送，
>
> - `retry`：
>
>   重新连接的时间。
>
>   如果与服务器的连接丢失，浏览器将等待指定的时间，然后尝试重新连接。
>
>   这必须是一个整数，以毫秒为单位指定重新连接的时间。如果指定了一个非整数值，该字段将被忽略。
>
> 如果一行文本中不包含冒号，则整行文本会被解析成为字段名，其字段值为空。
>
> 事件流文本示例：
>
> > 下面的例子中发送了三条消息，第一条仅仅是个注释，因为它以冒号开头。正如之前提到的，如果消息可能不会定期发送，这可以作为保持连接的机制，非常有用。
> >
> > 第二条消息只包含了一个 data 字段，值为“some text”。第三条消息包含的两个 data 字段会被解析成为一个字段，值为“another message\nwith two lines”。注意值中的换行字符。
> >
> > ~~~
> > : this is a test stream
> > 
> > data: some text
> > 
> > data: another message
> > data: with two lines
> > ~~~

