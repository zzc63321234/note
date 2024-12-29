`Nest` 在启动时，会递归解析 `Module` 依赖，扫描其中的 `Provider`、`Controller`，注入它的依赖。全部解析完成后，会监听网络端口，开始处理请求。

## 在模块中使用生命周期函数 

比如 `onModuleInit` 和 `onApplicationBootstrap`，在 `@nestjs/common` 中提供了这样两个接口：

~~~typescript
export interface OnModuleInit {
  onModuleInit(): any;
}

export interface OnApplicationBootstrap {
  onApplicationBootstrap(): any;
}
~~~

如果想在 `AppController` 中使用这两个生命周期函数，则可以让 `AppController` 类实现这两个接口，并在类中定义生命周期方法：

~~~typescript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';
import { OnModuleInit, OnApplicationBootstrap } from '@nestjs/common';

@Controller()
export class AppController implements OnModuleInit, OnApplicationBootstrap {
  constructor(private readonly appService: AppService,) {}

  onModuleInit() {
    console.log('AppController OnModuleInit');
  }

  onApplicationBootstrap() {
    console.log('AppController OnApplicationBootstrap');
  }

  @Get()
  getHello(): string {
    console.log(this.person);
    return this.appService.getHello();
  }
}
~~~

## 常见生命周期函数

`Nest` 中每一个模块都有自己的生命周期，且所有的生命周期函数都支持异步。

应用启动时，会递归解析 `Module` 依赖，初始化各个模块。初始化时，会先调用各个模块内的 `Controller`、`Provider` 的 **`onModuleInit`** 方法，再调用 `Module` 的 **`onModuleInit`** 方法。全部初始化完成后，会依次调用模块内 `Controller`、`Provider` 的 **`onApplicationBootstrap`** 方法，再调用 `Module` 的 **`onApplicationBootstrap`** 方法。之后应用就启动完成，开始监听网络端口。

应用销毁时，会先调用各个模块内的 `Controller`、`Provider` 的 **`onModuleDestroy`** 方法，再调用 `Module` 的 **`onModuleDestroy`** 方法。之后调用各个模块内的 `Controller`、`Provider` 的 **`beforeApplicationShutdown`** 方法，再调用 `Module` 的 **`beforeApplicationShutdown`** 方法。之后会停止监听网络端口，并调用各个模块内的 `Controller`、`Provider` 的 **`onApplicationShutdown`** 方法，再调用 `Module` 的 **`onApplicationShutdown`** 方法。

总的来说，应用启动和应用销毁时的常用生命周期函数有：

- `onModuleInit`
- `onApplicationBootstrap`
- `onModuleDestroy`
- `beforeApplicationShutdown`
- `onApplicationShutdown`

其中，`onModuleDestroy` 和 `beforeApplicationShutdown` 的执行时机类似，但是作用有所区别。以下是这两个生命周期对应的接口：

~~~typescript
export interface OnModuleDestroy {
    onModuleDestroy(): any;
}

export interface BeforeApplicationShutdown {
    beforeApplicationShutdown(signal?: string): any;
}
~~~

可以看到，`beforeApplicationShutdown` 函数可以接收一个 `signal` 参数，这是由别的进程传过来的终止信号，可以使用它执行一些销毁相关的逻辑。