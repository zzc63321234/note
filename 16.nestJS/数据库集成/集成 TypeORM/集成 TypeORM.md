## 所需依赖

`@nestjs/typeorm` 提供了对 `Nest` 中使用 `TypeORM` 的封装。需要同时安装 `typeorm`。如果需要使用 `mysql2` 作为数据库驱动，则也一起安装：

~~~bash
$ npm i @nestjs/typeorm typeorm mysql2
~~~

## `DataSource` 配置

`@nestjs/typeorm` 提供一个 `TypeOrmModule`，作为动态模块引入 `Nest` 的 `AppModule` 中。它使用 `forRoot` 方法传入配置，表示这个动态模块生成后是全局有效的。使用的配置项和 `TypeORM` 中实例化 `DataSource` 的配置项相同。例：

~~~typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'test',
      database: 'test_db',
      synchronize: true,
      logging: true,
      entities: [],
      poolSize: 10,
      connectorPackage: 'mysql2',
      extra: {
        authPlugin: 'sha256_password',
      },
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

~~~

## 注入 `EntityManager` 和 `Repository`

可以在模块中使用 `@nest/typeorm` 提供的 `@InjectEntityManager()` 装饰器注入 `EntityManager`。

也可以使用 `@nest/typeorm` 提供的 `@InjectDataSource()` 装饰器注入 `DataSource`。

如果需要注入 `Repository`，则需要先在要使用 `Repository` 的 `Nest` 模块中，引入使用 `TypeOrmModule.forFeature()` 生成的动态模块，`TypeOrmModule.forFeature()` 接收一个数组，包含所需的 `Entity` 类。这样在这个模块中，`Nest` 可以识别到这些实体的存在。之后在这个模块中就可以使用 `@nest/typeorm` 提供的 `@InjectRepository()` 装饰器，传入 `Entity` 类作为参数以注入对应的 `Repository`。

什么时候需要使用 `TypeOrmModule.forFeature()` 注册 `Entity`：

> 如果你不使用`@InjectRepository()`来注入`Repository`，而是选择其他方式来使用`Entity`，那么是否需要在对应模块中引入`TypeOrmModule.forFeature()`取决于你的具体实现方法和需求。`TypeOrmModule.forFeature()`的主要作用是为了注册那些在模块中使用的实体，并为它们创建相应的提供者（`Provider`），以便可以在服务（`Service`）中通过依赖注入的方式使用这些实体对应的`Repository`。
>
> 1. **如果你的替代方法仍然需要通过NestJS的依赖注入系统来获取`Entity`的`Repository`**，那么你依然需要使用`TypeOrmModule.forFeature()`来注册这些实体。因为无论你是通过`@InjectRepository()`还是通过其他自定义装饰器或手动`@Inject()`注入`Repository`，`NestJS`都需要知道这些实体及其`Repository`的存在，并通过`TypeOrmModule.forFeature()`注册这些实体来创建对应的注入令牌。
> 2. **如果你的替代方案不依赖`NestJS`的依赖注入系统来获取`Entity`的`Repository`**，比如直接使用`TypeORM`的`getRepository`函数来手动获取`Repository`，那么理论上你不需要在模块中引入`TypeOrmModule.forFeature()`。这种情况下，你直接操作`TypeORM`层面的`API`，绕过了`NestJS`的依赖注入机制。
>
> 总的来说，是否需要在对应模块中引入`TypeOrmModule.forFeature()`主要取决于你的实现方式是否依赖NestJS的依赖注入机制来获取和使用`Entity`的`Repository`。如果是，那么无论是使用`@InjectRepository()`还是其他注入方式，引入`TypeOrmModule.forFeature()`都是必要的。如果不是，那么可能不需要引入。但要注意，即使不通过依赖注入使用`Repository`，直接操作`TypeORM`也应确保你的实体正确注册到了`TypeORM`中，这通常是通过`TypeOrmModule.forRoot()`或`TypeOrmModule.forRootAsync()`在全局范围内完成的。

