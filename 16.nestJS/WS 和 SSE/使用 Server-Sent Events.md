`Server-Sent Events`（`SSE`）允许网页自动从 `Web` 服务器获取更新。

`SSE` 使用的仍是 `HTTP` 协议，从服务端返回的 `Content-Type` 是 `text/event-stream`，这是一个流，可以多次返回内容。

在 `Nest` 中，可以对 `Handler` 使用 `@Sse()` 装饰器进行注解，表示这是一个 `event stream` 类型的接口。例：

~~~typescript
@Sse('stream')
stream() {
    return new Observable((observer) => {
      observer.next({ data: { msg: 'aaa'} });

      setTimeout(() => {
        observer.next({ data: { msg: 'bbb'} });
      }, 2000);

      setTimeout(() => {
        observer.next({ data: { msg: 'ccc'} });
      }, 5000);
    });
}
~~~

`Handler` 中返回一个 `Observable` 对象，在内部使用 `observer.next()` 返回消息。

