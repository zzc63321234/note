## 参数

`Interceptor` 中的 `intercept` 方法接收 `2` 个参数。第一个参数 `context` 是一个 `ExecutionContext` 实例。第二个参数 `next` 是一个 `CallHandler` 实例。

### `ArgumentsHost`

`ExecutionContext` 继承自  `ArgumentsHost`，`ArgumentsHost` 是传递给原始 `Handler` 的参数的一个包装。例如在 `HTTP` 应用中使用 `@nextjs/platform-express` 时，调用 `ArgumentsHost` 的 `getArgs()` 方法，会返回一个 `[request, response, next]` 数组，其中的对象来自于 `Express`。注意这里的 `next` 不同于 `intercept` 方法的第二个参数 `next`。

当构建通用的 `AOP` 机制时，有时需要跨应用上下文运行，可以通过 `ArgumentsHost` 的 `getType()` 方法获取当前的应用类型：

~~~typescript
if (host.getType() === 'http') {
  // do something that is only important in the context of regular HTTP requests (REST)
} else if (host.getType() === 'rpc') {
  // do something that is only important in the context of Microservice requests
} else if (host.getType<GqlContextType>() === 'graphql') {
  // do something that is only important in the context of GraphQL requests
}
~~~

`ArgumentsHost` 也可以用来获取具体的应用上下文对象：

~~~typescript
/**
 * Switch context to RPC.
 */
switchToRpc(): RpcArgumentsHost;
/**
 * Switch context to HTTP.
 */
switchToHttp(): HttpArgumentsHost;
/**
 * Switch context to WebSockets.
 */
switchToWs(): WsArgumentsHost;
~~~

比如调用 `ArgumentsHost` 的 `switchToHttp` 方法，可以获得一个 `HttpArgumentsHost` 对象，它通过 `getRequest` 和 `getResponse` 方法分别提取 `Request` 和 `Response` 对象：

~~~typescript
const ctx = host.switchToHttp();
const request = ctx.getRequest<Request>();
const response = ctx.getResponse<Response>();
~~~

类似地，`WsArgumentsHost` 和 `RpcArgumentsHost` 也能获得对应的期望对象，以下是它们的方法：

~~~typescript
export interface WsArgumentsHost {
  /**
   * Returns the data object.
   */
  getData<T>(): T;
  /**
   * Returns the client object.
   */
  getClient<T>(): T;
}

export interface RpcArgumentsHost {
  /**
   * Returns the data object.
   */
  getData<T>(): T;

  /**
   * Returns the context object.
   */
  getContext<T>(): T;
}
~~~

总结：`ArgumentsHost` 可以获取到传递给 `Handler` 的参数、获取和切换上下文类型。

### `ExecutionContext`

`ExecutionContext` 继承自 `ArgumentsHost`，并扩展了以下方法：

~~~typescript
export interface ExecutionContext extends ArgumentsHost {
  /**
   * Returns the type of the controller class which the current handler belongs to.
   */
  getClass<T>(): Type<T>;
  /**
   * Returns a reference to the handler (method) that will be invoked next in the
   * request pipeline.
   */
  getHandler(): Function;
}
~~~

通过 `getHandler()` 方法可以获取当前路由匹配的 `Handler` 函数，通过 `getClass()` 方法可以获取当前路由匹配的 `Handler` 所属的 `Controller` 类（不是实例）。

可以获取到 `Handler` 和 `Controller`，也就意味着可以获取到通过  `@SetMetadata()` 装饰器附加给 `Handler` 或 `Controller` 的元数据。如何获取元数据：

- 从 `@nestjs/core` 包中导入 `Reflector` 类型，将其注入到类中。
- 在类的方法中调用 `this.reflector.get<T>()` 方法可获取单个目标的元数据。接收两个参数，第一个参数是要获取的元数据的键，第二个参数是元数据的来源目标（`Handler` 或 `Controller`）。
- 在类的方法中调用 `this.reflector.getAllAndMerge<T>()` 方法可获取多个目标的元数据并合并。接收两个参数，第一个参数是要获取的元数据的键，第二个参数是元数据的来源目标数组。返回一个包含对应元数据值的数组。
- 在类的方法中调用 `this.reflector.getAllAndOverride<T>()` 方法可以从多个目标中获取一个元数据，如果第一个目标不存在元数据，则获取下一个目标，以此类推，知道获取到元数据为止将它返回，否则返回 `undefined`。第一个参数是要获取的元数据的键，第二个参数是元数据的来源目标数组。

总结：`ExecutionContext` 在 `ArgumentsHost` 的基础上，另外拥有获取 `Handler` 和 `Controller` 的能力，使得可以获取它们的元数据。

### `CallHandler`

`CallHandler` 实例是一个包装执行流的对象。调用 `CallHandler` 的 `handle()` 方法会返回一个 `RxJS` 中的 `Observable` 对象。该 `Observable` 会同步或异步地产出请求路由对应的 `Handler` 执行后返回的值。只有当该 `Observable` 对象在 `intercept` 方法中被 `return` 出去时，它才会被 `subscribe`，并开启流。因此如果没有将他 `return`，那么 `Handler` 就不会执行。可以对这个 `Observable` 使用 `pipeable operation` 进行修改再返回，这样可以修改最终返回的响应。如果 `Observable` 流中发出了多个值，那么会在 `Observable` 流发出 `complete` 通知时将最后发出的值作为响应返回。 

## `Interceptor` 中处理响应

在 `Interceptor` 中适合使用的 `RxJS` 中的 `operator` 并不多， 常见的用法如下。

### 响应映射（`map`）

`map` 操作符用法：

- 参数：

  >`project`：一个函数。
  >
  >- 第一个参数是 `value`，接收源 `Observable` 发送的每个值。
  >- 第二个参数是 `index`，表示这是自订阅以来的第 `i` 次发送，注意 `i` 从 `0` 开始计数。
  >- `project` 函数返回要映射的值。
  >
  >`thisArg`：一个可选参数，用于定义 `project` 函数中的 `this` 是什么，默认是 `undefined`。

- 返回修改后的 `Observable`。

创建一个 `TransformInterceptor`，它将打包响应并将其分配给 `data` 属性：

~~~typescript
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

export interface Response<T> {
  data: T;
}

@Injectable()
export class TransformInterceptor<T> implements NestInterceptor<T, Response<T>> {
  intercept(context: ExecutionContext, next: CallHandler): Observable<Response<T>> {
    return next.handle().pipe(map(data => ({ data })));
  }
}
~~~

创建一个 `ExcludeNullInterceptor`，将响应的 `null` 值转换为空字符串 `''`：

~~~typescript
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

@Injectable()
export class ExcludeNullInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next
      .handle()
      .pipe(map(value => value === null ? '' : value ));
  }
}
~~~

### 异常映射（`catchError`）

`catchError` 操作符用法：

- 参数：

  > `selector`：一个函数。
  >
  > - 第一个参数是 `err`，接收当 `Observable` 流发生异常时的错误对象。
  > - 第二个参数是 `caught`，这是源 `Observable`，可以再次返回它以重试。
  > - `selector` 返回的任何 `Observable` 都将用作后续的 `Observable` 链。

- 返回修改后的 `Observable`。

创建一个 `ErrorsInterceptor`，用以覆盖抛出的异常：

~~~typescript
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  BadGatewayException,
  CallHandler,
} from '@nestjs/common';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Injectable()
export class ErrorsInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next
      .handle()
      .pipe(
        catchError(err => throwError(new BadGatewayException())),
      );
  }
}
~~~

上面的 `throwError` 是一个创建操作符，它将创建一个错误实例，并在订阅后立即将其作为错误推送给 `Consumer`。

### 截取切面（`tap`）

`tap` 操作符用法：

- `tap` 操作符接收的参数与 `Observable.subscribe()` 方法接收的参数相同，都可以接收一个 `Observer` 或单独传入一个 `Next` 通知处理函数。
- `tap` 操作符返回的 `Observable` 与源 `Observable` 发出的值相同。但是当其发出值时，会为其额外运行指定的回调，可以执行一些副作用，且不会影响源 `Observable` 流的执行。

创建一个 `LoggingInterceptor`，用以记录处理请求所花费的时间：

~~~typescript
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    console.log('Before...');

    const now = Date.now();
    return next
      .handle()
      .pipe(
        tap(() => console.log(`After... ${Date.now() - now}ms`)),
      );
  }
}
~~~

### 处理超时（`timeout`）

`timeout` 操作符主要用于指定一个时间，如果 `Observable` 在给定时间范围内没有发送值，则会报错。

创建一个 `TimeoutInterceptor`，如果 `Handler` 在指定时间内没有完成响应，则会取消请求并以错误响应终止：

~~~typescript
import { Injectable, NestInterceptor, ExecutionContext, CallHandler, RequestTimeoutException } from '@nestjs/common';
import { Observable, throwError, TimeoutError } from 'rxjs';
import { catchError, timeout } from 'rxjs/operators';

@Injectable()
export class TimeoutInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      timeout(5000),
      catchError(err => {
        if (err instanceof TimeoutError) {
          return throwError(new RequestTimeoutException());
        }
        return throwError(err);
      }),
    );
  };
};
~~~

