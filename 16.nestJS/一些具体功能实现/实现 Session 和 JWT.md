## `session` 实现

`Nest` 中可以使用 `express` 的中间件 `express-session` 实现 `session`。

安装 `express-session` 及其 `ts` 类型定义：

~~~bash
npm i express-session @types/express-session
~~~

在 `main.ts` 中全局注册 `express-session` 中间件：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import * as session from 'express-session';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.use(
    session({
      // 在生产环境中，srcret 应是一个长且随机的字符串
      secret: 'secret_key',
      // 当设置为 true 时，每次访问都会重新生成 cookie，当设为 false 时，只有 session 内容发生了改变才会更新 cookie
      resave: false,
      // 当设置为 true 时，即使没有设置 session，也会初始化一个空的 session 对象
      saveUninitialized: false,
      // cookie 相关的设置
      cookie: {
        // 表示 cookie 通过 HTTPS 传输，如果应用没有使用 HTTPS，则需要设置为 false
        secure: false,
        // cookie 的过期时间，默认为会话 cookie
        maxAge: 24 * 60 * 1000, // 表示 24 小时后过期
      },
    }),
  );
  await app.listen(3000);
}
bootstrap();
~~~

之后便可在 `Controller` 中通过 `@Session()` 参数装饰器将 `session` 对象作为参数注入：

~~~typescript
import { Controller, Get, Session } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get('get-session')
  getSession(@Session() session) {
    console.log(session);
    session.count = session.count ? session.count + 1 : 1;
    return session.count;
  }
}
~~~

## `JWT` 实现

`Nest` 可以使用 `@nestjs/jwt` 实现 `JWT`。

安装：

~~~bash
$ npm i @nestjs/jwt
~~~

在 `AppModule` 中将 `JwtModule` 作为动态模块，使用 `register` 或 `registerAsync` 方法注册：

~~~typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { JwtModule } from '@nestjs/jwt';

@Module({
  imports: [
    JwtModule.register({
      global: true, // 声明为全局模块
      useFactory() {
        return {
          secret: 'secret_key',
          signOptions: { expiresIn: '7d' },
        };
      },
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
~~~

可在 `Controller` 中注入 `JwtService`，使用它的 `sign` 方法生成 `token`，使用它的 `verify` 方法验证 `token` 并将其 `payload` 返回：

~~~typescript
import {
  Controller,
  Get,
  Headers,
  Inject,
  Res,
  Session,
  UnauthorizedException,
} from '@nestjs/common';
import { AppService } from './app.service';
import { JwtService } from '@nestjs/jwt';
import { Response } from 'express';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Inject(JwtService)
  private jwtService: JwtService;

  @Get('get-token')
  getToken(@Res({ passthrough: true }) response: Response) {
    // JwtService 的 sign 方法接收 payload 以生成一个 token
    const newToken = this.jwtService.sign({
      userName: 'zzc6332',
    });
    // 在响应头中添加 token 字段，其值为新生成的 token
    // 当然，也可以在响应体中返回
    response.setHeader('token', newToken);
    return '获取 token 成功，请查看响应头';
  }

  @Get('verify-token')
  verifyToken(@Headers('authorization') authorization: undefined | string) {
    if (authorization) {
      const token = authorization.split(' ')[1];
      console.log(token);
      try {
        const payload = this.jwtService.verify(token);
        return 'token 有效，用户名为 ' + payload.userName;
      } catch (error) {
        throw new UnauthorizedException(error.message);
      }
    }
    throw new UnauthorizedException('token 为空');
  }
}
~~~

## 双 `token`

采用双 `token` 的系统，服务端会返回两个 `token`：

- `access_token`：认证用户身份。
- `refresh_token`：刷新 `access_token` 和 `refresh_token`。

