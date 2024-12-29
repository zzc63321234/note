## `Nest` 内建的 `Logger`

`Nest` 提供了用于在控制台中打印日志的 `logger` 对象。

从 `@nestjs/common` 中引入 `Logger` 构造函数，可以将其实例放入 `Controller` 或 `Service` 中使用：

~~~typescript
import { Controller, Get, Logger } from '@nestjs/common';

@Controller()
export class AppController {
  private logger = new Logger();

  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    this.logger.debug('debug', AppController.name);
    this.logger.error('error', AppController.name);
    this.logger.log('log', AppController.name);
    this.logger.verbose('verbose', AppController.name);
    this.logger.warn('warn', AppController.name);
    return this.appService.getHello();
  }
}
~~~

当 `Handler` 被调用时，控制台中会打印如下日志：

~~~
[Nest] 27216  - 2024/02/01 14:56:00   DEBUG [AppController] debug
[Nest] 27216  - 2024/02/01 14:56:00   ERROR [AppController] error
[Nest] 27216  - 2024/02/01 14:56:00     LOG [AppController] log
[Nest] 27216  - 2024/02/01 14:56:00 VERBOSE [AppController] verbose
[Nest] 27216  - 2024/02/01 14:56:00    WARN [AppController] warn
~~~

其中 `27216` 代表的是当前 `Node.js` 进程的 `PID`（`Process ID`，进程标识符）。`verbose`、`debug`、`log`、`warn`、`error` 表示日志级别。`AppController` 表示当前所在的上下文。最后一项显示的是日志的内容。

这个日志受 `Nest` 控制，可以在创建应用时指定是否开启（默认开启）：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: false, // 指定不使用 logger 打印日志
  });
  await app.listen(3000);
}
bootstrap();
~~~

也可以只输出指定级别的日志：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: ['warn', 'error'], // 只输出 warn 和 error 级别的日志
  });
  await app.listen(3000);
}
bootstrap();
~~~

## 自定义 `Logger`

可以定义一个实现了 `LoggerService` 接口的类，以自定义 `Logger` 中的方法：

~~~typescript
import { LoggerService } from '@nestjs/common';

export class MyLogger implements LoggerService {
  log(message: string, context: string) {
    console.log(`---log---[${context}]---`, message);
  };

  error(message: string, context: string) {
    console.log(`---error---[${context}]---`, message);
  };

  warn(message: string, context: string) {
    console.log(`---warn---[${context}]---`, message);
  };
}
~~~

也可以集成 `ConsoleLogger`，重写其中的一写方法：

~~~typescript
import { ConsoleLogger } from '@nestjs/common';

export class MyLogger2 extends ConsoleLogger{
  log(message: string, context: string) {
    console.log(`[${context}]`,message);
  };
}
~~~

在创建应用时指定自定义的 `Logger` 使得项目启动时默认使用自定义 `Logger` 打印日志，这种注册方式不支持往自定义 `Logger` 中注入依赖：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { MyLogger } from './MyLogger';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: new MyLogger()
  });
  await app.listen(3000);
}
bootstrap();
~~~

也可以使用 `app.usLogger()` 方法指定 `Logger`，这种注册方式支持依赖注入：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { MyLogger } from './MyLogger';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    buffrtLogs: true, // 表示先不打印日志，将它放到 buffer 缓冲区，直到使用 app.useLogger 指定了 Logger 并初始化完毕后再打印
  });
  // app.get 方法用于从容器中获取这个类的实例，获取到的实例仍是被容器管理的，因此支持依赖注入
  app.useLogger(app.get(MyLogger))
  await app.listen(3000);
}
bootstrap();
~~~

