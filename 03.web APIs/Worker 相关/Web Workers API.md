`Web Worker` 为 `Web` 内容在后台线程中运行脚本提供了一种简单的方法。线程可以执行任务而不干扰用户界面。此外，它们可以使用 `XMLHttpRequest`（尽管 `responseXML` 和 `channel` 属性总是为空）或 `fetch`（没有这些限制）执行 `I/O`。一旦创建，一个 `worker` 可以将消息发送到创建它的 `JavaScript` 代码，通过将消息发布到该代码指定的事件处理器（反之亦然）。

`Worker` 的全局上下文与主线程的 `Window` 不同。

[Web Workers 可以使用的函数和类 - Web API | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Functions_and_classes_available_to_workers)

有许多不同类型的 `worker`：

- 专用 `worker` 是由单个脚本使用的 `worker`。该上下文由 `DedicatedWorkerGlobalScope` 对象表示。
- `Shared worker` 是可以由在不同窗口、`IFrame` 等中运行的多个脚本使用的 `worker`，只要它们与 `worker` 在同一域中。它们比专用的 `worker` 稍微复杂一点——脚本必须通过活动端口进行通信。
- `Service Worker` 基本上是作为代理服务器，位于 `web` 应用程序、浏览器和网络（如果可用）之间。它们的目的是（除开其他方面）创建有效的离线体验、拦截网络请求，以及根据网络是否可用采取合适的行动并更新驻留在服务器上的资源。它们还将允许访问推送通知和后台同步 `API`。

## 专用 `Worker`

### `Worker`

`Worker` 接口是 `Web Workers API` 的一部分，指的是一种可由脚本创建的后台任务，任务执行中可以向其创建者收发信息。要创建一个 `Worker`，只须调用 `Worker(URL)` 构造函数，函数参数 `URL` 为指定的脚本。

#### 构造函数

`Worker()` 构造函数创建一个 `Worker` 对象，该对象执行指定的 `URL` 脚本。这个脚本必须遵守同源策略。

语法：

~~~javascript
const myWorker = new Worker(aURL, options);
~~~

参数：

- `aURL`：

  表示 `Worker` 将执行的脚本的 `URL`，必须遵守同源策略。

- `options`：

  可选配置项，可用属性如下：

  - `type`：

    用于指定 `worker` 类型。

    可选值为 `"classic"`（默认）或 `"module"`。

  - `credentials`：

    用于指定 `worker` 凭证。

    可选值为 `"omit"`（默认）、`"same-origin"`、`"include"`。

  - `name`：

    在 `DedicatedWorkerGlobalScope` 的情况下，用来表示 `worker` 的 `scope` 的字符串，主要i用于调试目的。

#### 实例方法

##### `postMessage()`

`postMessage()` 可以向 `worker` 发送消息。

[`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 的 **`postMessage()`** 方法委托给 [`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) 的 [`postMessage()`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort/postMessage) 方法，该方法会在对应的用于接收 [`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) 的事件循环中添加一个任务。

`Worker` 可以使用 [`DedicatedWorkerGlobalScope.postMessage` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope/postMessage) 方法将信息发送回生成它的线程。

参数：

- `message`：

  要传递给 worker 的对象；这将在传递给 [`DedicatedWorkerGlobalScope.message_event` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope/message_event) 事件的 `data` 字段中。这可以是任何值或可以通过[结构化克隆算法](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)处理的 JavaScript 对象（可以包含循环引用）。

- `transfer`：

  一个可选的、会被转移所有权的[可转移对象](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Transferable_objects)数组。如果一个对象的所有权被转移，它将在发送它的上下文中变为不可用（中止），而仅在接收方的 worker 中可用。

  像 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)、[`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) 或 [`ImageBitmap`](https://developer.mozilla.org/zh-CN/docs/Web/API/ImageBitmap) 类的实例才是可转移对象，才能够被转移。不能将 `null` 作为 `transfer` 的值。

##### `terminate()`

`terminate()` 用于立即终止 `Worker` 的行为。

#### 事件

`Worker` 继承自 `EventTarget`，因此可以接收事件。

##### `message` 事件

当 worker 的父级接收到来自其 worker 的消息时（也就是说，当 worker 通过 [`DedicatedWorkerGlobalScope.postMessage()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope/postMessage) 发送消息时），会在 [`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 对象上触发 `message` 事件。

此事件不能取消，也不会冒泡。

##### `messageerror` 事件

当 [`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 对象接收到一条无法被反序列化的消息时，将在该对象上触发 `messageerror` 事件。

此事件不能取消，也不会冒泡。

##### `error` 事件

[`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 接口的 **`error`** 事件会在 `worker` 发生错误时触发。

### `DedicatedWorkerGlobalScope`

**`DedicatedWorkerGlobalScope`** 对象是 `Worker` 的全局作用域，也可以通过 `self` 关键字来访问。

#### 实例属性

##### `name`

用于获取创建 `Worker` 实例时在可选配置项中传入的 `name`，表示当前 `Worker` 的名称。

#### 常用实例方法

##### `postMessage()`

`postMessage()` 用于将消息传递给主线程。

##### `close()`

关闭 `worker`。相当于在主线程中调用 `worker.terminate()`。

#### 事件

`message`、`messageerror` 等。