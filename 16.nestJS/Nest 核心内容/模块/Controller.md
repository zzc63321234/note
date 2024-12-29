`Controller` 即控制器，负责处理传入的请求和向客户端返回响应。

## 定义 `Controller`

路由机制控制哪个 `Controller` 接收哪些请求。使用 `@Controller()` 装饰器装饰附加给一个 `Class` 以定义一个 `Controller`，`@Controller()` 装饰器会将 `Class` 与所需的元数据相关联，使得 `Nest` 能创建路由映射（将请求绑定到相应的 `Controller`）。

## 匹配路由

`@Controller()` 装饰器工厂可以接收一个可选的字符串参数，它会作为该 `Controller` 匹配的路由路径前缀。

`Controller` 类中定义的方法是 `Handler`。`Handler` 的命名和路由无关，通常会附加装饰器来标示不同的 `HTTP` 请求方法，这些装饰器分别是：`@Get()`、`@Post()`、`@Put()`、`@Delete()`、`@Patch()`、`@Options()`、`@Head()`。此外，还可以用 `@All()` 定义一个用于处理所有 `HTTP` 请求方法的 `Handler`。这些装饰器工厂中可以传入一个可选的字符串参数，它会结合 `@Controller` 中传入的路径前缀（如果存在）作为该 `Handler` 匹配的路径。

在请求方法装饰器中定义的路由支持模式匹配：

- 通配符：

  字符串参数中可以使用 `*` 作为通配符。例如 `@All('ab*cb)'` 表示匹配任何 `'ad'` 开头 `'cd'` 结尾的路径。

- 正则表达式：

  `Nest` 中路由定义时能配合动态路由实现部分正则表达式的功能，支持的正则表达式符号有：`？`、`+`、`*`、`(`、`)`。例如 `@All(':param(a.*b)')`  表示匹配任何 `'ad'` 开头 `'cd'` 结尾的路径。

`Controller` 示例：

~~~typescript
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
~~~

以上示例中，使用 `GET` 请求访问 `/cats` 路径时，`Nest` 会将请求路由到 `findAll` 方法中，并将该方法的返回值作为响应返回给客户端。

## 返回响应

`Handler` 中返回响应有两种方式：

- 使用 `Handler` 的返回值作为响应：

  - 当 `Handler` 返回一个 `JS` 对象或数组时，它将自动序列化为 `JSON` 格式作为响应返回。
  - 当 `Handler` 返回一个 `JS` 基本类型（如 `string`、`number`、`boolean`）时，`Nest` 会将值本身作为响应返回。

- 使用 `HTTP` 库特有的方法返回响应：

  在 `Handler` 参数中通过 `@Res()` 参数装饰器引入 `HTTP` 库中特认定的响应对象，例如 `Express` 中的 `response.send()` 来构建响应。

  注意：如果在 `Handler` 中使用了 `@Res()` 或 `@Next()` 参数装饰器，则默认会忽略 `Handler` 的返回值，将返回响应的构建交给 `HTTP` 库。如果需要在 `Handler` 中使用这两种注入，却仍需要 `Nest` 将 `Handler` 的返回值作为响应（例如需要注入的 `HTTP` 库响应对象设置 `header` 等，但其余的部分仍需交给 `Nest` 处理），则要使用 `@Res({ passthrough: true })` （如果是使用了 `@Next()` 注入 `next`，也一样是通过 `@Res({ passthrough: true })` 来配置）的方式注入。

## 使用 `HTTP` 库中的对象

`Handler` 中可以通过以下参数装饰器注入 `HTTP` 库中的对象：

| 参数装饰器                | 注入的 `HTTP` 库中的特定对象        |
| ------------------------- | ----------------------------------- |
| `@Request()` / `@Req()`   | `req`                               |
| `@Response()` /  `@Res()` | `res`                               |
| `@Next()`                 | `next`                              |
| `@Session()`              | `req.session`                       |
| `@Param(key?: string)`    | `req.params` / `req.params[key]`    |
| `@Body(key?: string)`     | `req.body` / `req.body[key]`        |
| `@Query(key?: string)`    | `req.query` / `req.query[key]`      |
| `@Headers(name?: string)` | `req.headers` / `req.headers[name]` |
| `@Ip()`                   | `req.ip`                            |
| `@HostParam()`            | `req.hosts`                         |

## 状态码

默认情况下，`POST` 请求响应状态码是 `201`，其它请求响应状态码是 `202`。

可以在 `Handler` 上添加 `@HttpCode()` 装饰器来快速设置响应状态码：

~~~typescript
...
@Get()
@HttpCode(204)
Test(){}
...
~~~

## 响应头

可以在 `Handler` 上添加 `@Header()` 装饰器来快速指定响应头：

~~~typescript
...
@Get()
@Header('Cache-Control', 'none')
Test(){}
...
~~~

## 重定向

可以在 `Handler` 上添加 `@Redirect()` 装饰器来快速指定重定向的 `URL`:

~~~typescript
...
@Get()
@Redirect('https://nestjs.com', 301)
Test(){}
...
~~~

`@Redirect()` 装饰器工厂接收两个参数：

- 第一个参数是重定向 `URL`，可以是完整 `URL` 或路由中的路径。
- 第二个是状态码，如果省略则默认是 `302`。

如果想要动态地决定重定向的 `URL` 或状态码，则可以使用 `@Redirect()` 的同时在 `Handler` 中返回一个如下格式的对象：

~~~typescript
{
  "url": string,
  "statusCode": number
}
~~~

`Handler` 的返回值会覆盖 `@Redirect()` 中的所有参数：

~~~typescript
...
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version) {
  if (version && version === '5') {
    return { url: 'https://docs.nestjs.com/v5/' };
  }
}
...
~~~

## 子域路由

 `@Controller()` 装饰器可以接受一个配置对象作为参数，在 `host` 字段中可以指定一个值，要求传入请求的 `HTTP` 主机与其匹配：

~~~typescript
@Controller({ host: 'admin.example.com' })
export class AdminController {
  @Get()
  index(): string {
    return 'Admin page';
  }
}
~~~

也支持类似动态路由参数的方式动态接收传入请求的 `HOST`，接收到的值可以在 `Handler` 中使用 `@HostParam()` 参数装饰器接收：

~~~typescript
@Controller({ host: ':account.example.com' })
export class AccountController {
  @Get()
  getInfo(@HostParam('account') account: string) {
    return account;
  }
}
~~~

注意：由于 `Fastify` 缺乏对嵌套路由器的支持，因此使用子域路由时，应使用 `Express` 适配器。
