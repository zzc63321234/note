## 依赖

`Nest` 中使用 `websocket` 需要安装 `@nestjs/websockets`，如果需要集成 `Socket.IO`，则安装 `@nestjs/platform-socket.io`：

~~~bash
$ npm i @nestjs/websockets @nestjs/platform-socket.io
~~~

## 创建 `WebSocket` 模块

使用 `Nest Cli` 创建一个 `WebSocket` 模块：

~~~bash
$ nest g resourse demo
~~~

创建时传输层选择 `WebSocket`：

~~~
What transport layer do you use? WebSockets
~~~

与传输层为 `REST API` 的模块相比，`WebSocket` 模块少了一个用于处理 `HTTP` 请求的 `xxx.controller.ts`，而多了一个用于处理 `WebSocket` 连接的 `xxx.gateway.ts`。

## 常用装饰器

`@nestjs/websockets` 包提供了一些装饰器。

### `@WebSocketGateway()`

用于将一个类定义为 `WebSocket` 网关，它的第一个参数可以指定端口号（如果省略，则网关会与 `HTTP` 服务器监听相同的端口），第二个参数可以接收一个对象参数，用于配置网关的选项，例如路径、跨域设置等。

示例：

~~~typescript
@WebSocketGateway(80, { namespace: 'events' })

@WebSocketGateway(81, { transports: ['websocket'] })
~~~

### `@SubscribeMessage()`

用于将类中的方法定义为 `WebSocket` 消息处理器。

它需要接收一个字符串参数，表示消息的事件名称。因此消息处理器方法名并不会作为消息事件的名称。

消息处理器默认会接受两个参数：

- 第一个参数对应下面的 `@ConnectedSocket()` 装饰器注入的客户端 `Socket`；
- 第二个参数对应下面的 `@MessageBody()` 装饰器注入的消息主体内容。

也可以使用这两个装饰器自行注入。

### `@MessageBody()`

用于将消息的主体内容注入给 `WebSocket` 消息处理器方法的参数。

### `@ConnectedSocket()`

用于将当前连接的客户端 `Socket` 实例注入给 `WebSocket` 消息处理器方法的参数。

### `@WebSocketServer()`

用于将服务器的 `Socket` 实例注入给静态属性。

## 消息处理器的返回值

### `falsy` 值

例如 `undefined`，这样将不会响应客户端。

### 字符串

该字符串会作为服务器的应答。

示例：

~~~typescript
@SubscribeMessage('events')
handleEvent(
  @MessageBody() data: string,
  @ConnectedSocket() client: Socket,
): string {
  return data;
}
~~~

客户端中通过如下方式监听该应答（以 `Socket.IO` 为例）：

~~~typescript
// 用一个回调函数作为第三个函数以监听服务器的应答
socket.emit('events', { name: 'Nest' }, data => console.log(data));
~~~

### 事件对象

可以返回一个包含 `event` 和 `data` 属性的对象，这样会向客户端发送一个名称为 `event`，数据为 `data` 的事件。

示例：

~~~typescript
@SubscribeMessage('events')
handleEvent(@MessageBody() data: unknown): WsResponse<unknown> {
  const event = 'events';
  return { event, data };
}
~~~

为了侦听传入的响应，客户端必须应用另一个事件侦听器。

```typescript
socket.on('events', data => console.log(data));
```

### `Observable` 对象

可以返回一个 `RXJS` 中的 Observable 对象，这样可以异步地处理响应。

示例：

~~~typescript
@SubscribeMessage('events')
onEvent(@MessageBody() data: unknown): Observable<WsResponse<number>> {
  const event = 'events';
  const response = [1, 2, 3];

  return from(response).pipe(
    map(data => ({ event, data })),
  );
}
~~~

## 生命周期钩子

`@nestjs/websockets` 包提供了三个生命周期钩子的接口，它们是：

- `OnGatewayInit`：可以实现 `afterInit()` 方法；
- `OnGatewayConnection`： 可以实现 `handleConnection()` 方法；
- `OnGatewayDisconnect`：可以实现 `handleDisconnect()`方法。

示例：

~~~typescript
@WebSocketGateway()
export class AaaGateway implements OnGatewayInit, OnGatewayConnection, OnGatewayDisconnect{

  handleDisconnect(client: Server) {
  }

  handleConnection(client: Server, ...args: any[]) {
  }
  
  // afterInit 接收的参数是服务器端的 socket 实例，不同于前两个是客户端的 socket 实例
  afterInit(server: Server) {
  }
}
~~~

