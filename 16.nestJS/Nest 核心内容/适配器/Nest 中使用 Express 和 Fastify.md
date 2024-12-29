`Nest` 内部没有直接依赖任何一个 `http` 处理的库，只是依赖了抽象的接口。`Nest` 通过适配器与对应的 `http` 处理库连接。`Nest` 官方提供了 `Express` 和 `Fastify` 适配器，它们分别在 `@nestjs/platform-express` 和 `@nestjs/platform-fastify` 包中。

`Nest` 默认使用的是 `platform-express` 包，在创建 `app` 时给 `NestFactory.create()` 方法指定泛型 `NestExpressApplication`，那么创建的 `app`对象在编辑器中会提示 `Express` 平台特有的方法：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { NestExpressApplication } from '@nestjs/platform-express';

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  app.useStaticAssets('public', { prefix: '/static' }); // useStaticAssets 是 Express 平台特有的方法
  await app.listen(3000);
}
bootstrap();
~~~

如果要切换到 `Fastify`，则需要安装 `fastify` 和 `@nestjs/platform-fastify`：

~~~bash
$ npm install fastify @nestjs/platform-fastify
~~~

在 `NestFactory.create()` 方法中传入 `Fastify` 适配器：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { NestFastifyApplication, FastifyAdapter } from '@nestjs/platform-fastify';

async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter(),
  );
  await app.listen(3000);
}
bootstrap();
~~~

这样创建出来的 `app` 就是使用 `Fastify` 作为 `http` 处理库。