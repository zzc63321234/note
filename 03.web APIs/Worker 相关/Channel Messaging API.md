`Channel Messaging API` 允许两个不同的脚本运行在同一个文档的不同浏览器上下文（比如两个 `iframe`，或者文档主体和一个 `iframe`，使用 `SharedWorker` 的两个文档，或者两个 `worker`）来直接通讯，在每端使用一个端口（`port`）通过双向频道（`channel`）向彼此传递消息。

## `MessageChannel`

`Channel Messaging API` 的 `MessageChannel` 接口允许我们创建一个新的消息通道，并通过它的两个 `MessagePort` 属性发送数据。

使用 `new MessageChannel()` 可以创建一个新的 `MessageChannel` 对象。

`MessageChannel` 对象的实例属性有 `port1` 和 `port2` 这两个只读属性，它们都是 `MessagePort` 对象，分别表示通道的第一个端口（发送消息的源上下文）和第二个端口（接收消息的目的地上下文）。

## `MessagePort`

`MessagePort` 接口代表 `MessageChannel` 的两个端口之一，它可以让你从一个端口发送消息，并在消息到达的另一个端口监听它们。

`MessagePort` 是一个可转移对象。

### 实例方法

####  `postMessage()`

从端口发送一条消息，并且可选是否将对象的所有权交给其他浏览器上下文。

#### `start()`

`MessagePort` 接口的 start() 方法开始发送该端口中的消息队列。只有在使用 `EventTarget.addEventListener` 时才需要此方法；使用 `onmessage` 事件时已隐含调用该方法。

#### `close()`

`MessagePort` 接口的 `close()` 方法断开端口连接，使其不再处于活动状态。这将停止向该端口发送消息。

### 事件

`message` 事件和 `messageerror` 事件。