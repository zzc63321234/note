## `IoC` 机制

 `IoC`（`Inverse of Control`），即控制反转。

程序设计，比如后端系统中，有很多对象：

- `Controller` 对象：用以接收 `http` 请求，调用 `Service`，返回响应。
- `Service` 对象：实现业务逻辑。
- `Repository` 对象：实现对数据库的增删改查。
- 其它对象，比如数据库链接对象 `DataSource`，配置对象 `Config` 等。

这些对象有着错综复杂的依赖关系，比如 `Controller` 依赖 `Service` 实现业务逻辑，`Service` 依赖 `Repository` 来做增删改查，`Repository` 依赖 `DataSource` 来建立连接，`DataSource` 又需要从 `Config` 对象拿到用户名密码等信息。

在传统程序设计中，需要由应用程序主体来控制创建这些对象的流程，比如以下这样：

~~~javascript
const config = new Config({ username: 'xxx', password: 'xxx'});

const dataSource = new DataSource(config);

const repository = new Repository(dataSource);

const service = new Service(repository);

const controller = new Controller(service);
~~~

而在 `IoC` 机制中，这种控制被反转了：原本是由应用程序主体创建各个依赖对象，然后组装起来；而在 `IoC` 机制中，在定义对象（类）时，声明依赖了什么，由一个外部的容器或框架来创建并组装对象。这样，程序员只需要关注于业务逻辑的实现，而无需手动创建和管理这些对象。

## 模块中 `Provider` 的声明与注入

### 基本声明方式

当声明的 `Provider` 是类时，该类需要使用 `@injectable()` 装饰，比如在 `src/app.service.ts` 中：

~~~typescript
import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }
}
~~~

以 `src/app.module.ts` 中的声明为例，使用 `@Module` 装饰器工厂加工 `AppModule` 类时，传入一个配置对象作为参数给装饰器工厂，其中的 `providers` 字段用以注册 `Provider`，使其可以注入模块内的其它对象：

~~~typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService], // 此处将 AppService 作为 provider
})
export class AppModule {}
~~~

以上是一种简写方式，完整写法如下：

~~~typescript
@Module({
  imports: [],
  controllers: [AppController],
  providers: [{ // providers 中每一项的完整写法
    provide: AppService,
    useClass: AppService
  }],
})
export class AppModule {}
~~~

其中：

- `provide` 字段：
  - 指定的是该 `Provider` 的 `token`。
  - `token` 是一个用于识别 `Provider`  的唯一标识符。
  - `token` 接收的值可以是一个字符串、一个 `symbol` 或一个类。此处使用 `AppService` 类本身作为这个唯一标识符。

- `useClass` 字段：
  - 指定类，`Nest` 注入 `Provider` 时会将其实例化后注入。

### 基本注入方式

对于需要被注入 `Provider` 的类，需要使用 `@Injectable()` 或 `@Controller()` 装饰，其中：

- `@Injectable()` 表示该类可以被注入 `Provider`，也可以作为 `Provider` 注入到其它地方。
- `@Controller()` 表示该类只需要被注入 `Provider`，这是 `Nest` 单独为 `Controller` 类定义的装饰器。

比如在 `src/app.controller.ts` 中，将 `AppService` 类的实例注入到 `AppController` 中，以下是构造器注入方式：

~~~typescript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  // 在构造器函数中接收 appService 参数即声明了注入
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
~~~

以上形式注入的 `AppService`，它在声明依赖时 `provide` 字段提供的 `token` 就是 `AppService` 类本身，因此 `Nest` 在注入时会自动识别这个 `token`。如果在声明依赖时提供的 `token` 是字符串 `"app_service"`，那么在构造器注入方式下需要给 `appService` 参数附加 `Inject` 装饰器工厂并传入 `token`：

~~~typescript
@Controller()
export class AppController {
  constructor(@inject('app_service') private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
~~~

还有一种方式是属性注入，声明成员属性 `appService` 并附加  `Inject` 装饰器工厂传入 `token`，如下：

~~~typescript
@Controller()
export class AppController {
  @inject(AppService)
  private readonly appService: AppService

  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
~~~

可以看出，使用类本身作为 `token`，并使用构造器注入可以省去 `@inject`，是最便捷的方式。

### `Provider` 的其它注册方式

`Provider` 注册时除了可以使用 `useClass` 指定注入的类，还可以指定其它内容：

#### `useValue`

使用 `useValue` 字段指定一个固定值，该值可以被注入，声明如下：

~~~typescript
@Module({
  imports: [],
  controllers: [AppController],
  providers: [
    AppService,
    {
      provide: 'person',
      useValue: {
        name: 'a',
        age: 20,
      },
    },
  ],
})
export class AppModule {}
~~~

将其注入到 `AppController` 中：

~~~typescript
@Controller()
export class AppController {
  constructor(
    private readonly appService: AppService,
    @Inject('person') private readonly person: { name: string; age: number },
  ) {}

  @Get()
  getHello(): string {
    console.log(this.person);
    return this.appService.getHello();
  }
}
~~~

以上代码中已经可以在 `AppController` 中取到注入的值，使用 `GET` 方式请求 `nest` 服务器根路径时，服务器控制台会打印出该注入值。

#### `useFactory`

使用 `useFactory` 字段指定一函数，函数的返回值可以被注入，声明如下：

~~~typescript
@Module({
  imports: [],
  controllers: [AppController],
  providers: [
    AppService,
    {
      provide: 'person',
      useFactory() {
        return {
          name: 'b',
          age: 30,
        };
      },
    },
  ],
})
export class AppModule {}
~~~

与注入固定值示例一样，可以将其注入到 `AppController` 中。

另外，还可以配合 `inject` 字段，将其它声明好的 `provider` 注入到 `useFactory` 函数中：

~~~typescript
@Module({
  imports: [],
  controllers: [AppController],
  providers: [
    AppService,
    { provide: 'person', useValue: { name: 'c', age: 20 } },
    {
      provide: 'person-from-factory',
      useFactory(
        person: { name: string; age: number },
        appService: AppService,
      ) {
        return {
          name: person.name,
          desc: appService.getHello,
        };
      },
      inject: ['person', AppService], // 通过 inject 字段将上面声明过的 token 为'person' 的 Provider 和 AppService Provider 注入到 useFactory 的参数中
    },
  ],
})
export class AppModule {}
~~~

`useFactory` 也支持接收一个异步函数，`Nest` 会在拿到异步方法的结果后再注入。

#### `useExisting`

`useExisting` 用来给已声明的 `provider` 起别名：

~~~typescript
@Module({
  imports: [],
  controllers: [AppController],
  providers: [
    AppService,
    { provide: 'person', useValue: { name: 'c', age: 20 } },
    {
      provide: 'person-alias',
      useExisting: 'person', // 为 'person' 起一个别名为 'person-alias'
    },
  ],
})
export class AppModule {}
~~~

## 模块间的依赖注入

在 `xxx.module.ts` 中，`@Module()` 接收的配置对象中的 `exports` 字段可以将模块内注册过的对象导出，使得其它模块可以注入这个对象。

示例：

> 新建两个模块：
>
> ~~~bash
> $ nest g resource a --no-spec
> $ nest g resource b --no-spec
> ~~~
>
> 在 `src/a/a.module.ts` 中使用 `exports` 字段将 `AService` 类导出：
>
> ~~~typescript
> import { Module } from '@nestjs/common';
> import { AService } from './a.service';
> import { AController } from './a.controller';
> 
> @Module({
>   imports: [],
>   controllers: [AController],
>   providers: [AService],
>   exports: [AService],
> })
> export class AModule {}
> ~~~
>
> 在 `src/b/b.module.ts` 中使用 `imports` 字段将 `AModule` 模块导入：
>
> ~~~typescript
> import { Module } from '@nestjs/common';
> import { BService } from './b.service';
> import { BController } from './b.controller';
> import { AModule } from '../a/a.module';
> 
> @Module({
> imports: [AModule],
>   controllers: [BController],
>   providers: [BService],
> })
> export class BModule {}
> ~~~
>
> 此时已经可以在 `BModule` 模块的对象中注入 `AModule` 中的 `AService` 了。
>
> 如果不希望每次使用 `imports` 字段时手动引入模块，可以设置全局模块：
>
> ~~~typescript
> import { Module, Global } from '@nestjs/common';
> import { AService } from './a.service';
> import { AController } from './a.controller';
> 
> @Global() // @Global() 装饰器将 AModule 装饰成了一个全局模块
> @Module({
> imports: [],
> controllers: [AController],
> providers: [AService],
> exports: [AService],
> })
> export class AModule {}
> ~~~
>
> 此时在其它模块中不需要再手动引入 `AModule`，即可注入 `AModule` 中暴露的对象。
>
> 全局模块不建议滥用，否则很多 `Provider` 无法追踪来源，会降低代码的可维护性。

## 循环依赖的处理

假设有一个模块 `A` 和一个模块 `B` 互相循环引用，则无法创建成功，可以使用 `forwardRef` 方法引入，使得它们在创建完成后再互相关联：

~~~typescript
import { Module, forwardRef } from '@nestjs/common';
import { BModule } from 'src/b/b.module';

@Module({
  imports: [
    forwardRef(() => BModule)
  ],
})
export class AModule {}
~~~

对于循环引用的 `Provider`，比如 `CService` 和 `DService` 之间存在互相注入，也无法成功创建对象，可以使用 `forwardRef` 方法注入：

~~~typescript
import { Injectable, forwardRef, Inject } from '@nestjs/common';
import { DService } from './d.service';

@Injectable()
export class CService {
  constructor(@Inject(forwardRef(() => DService)) private DService: CService){}
}
~~~

## 动态模块

动态模块可以在被其它模块使用 `imports` 字段引入时，传递一些参数，而动态地生成模块内容。

### 基本使用

示例：

> 生成一个 `Foo` 模块：
>
> ~~~bash
> $ nest g module foo --no-spec
> ~~~
>
> 将 `Foo` 模块改为动态模块。在 `FooModule` 类中定义一个静态方法，该静态方法返回一个 `DynamicModule`，`DynamicModule` 对象和在 `@Module()` 装饰器中使用的定义对象类似，多了一个 `module` 字段。在 `DynamicModule` 中定义一个 `token` 为 `'FOO_OPTIONS'` 的依赖，其值为传入的参数 `options`，并将其 `export`：
>
> ~~~typescript
> import { Module, DynamicModule } from '@nestjs/common';
> 
> @Module({})
> export class FooModule {
>   static register(options: Record<string, any>): DynamicModule {
>     return {
>       module: FooModule,
>       providers: [
>         {
>           provide: 'FOO_OPTIONS',
>           useValue: options,
>         },
>       ],
>       exports: ['FOO_OPTIONS'],
>     };
>   }
> }
> ~~~
>
> 在 `AppModule` 中引入使用上述 `FooModule` 的静态方法传入 `options` 参数创建的 `DynamicModule`：
>
> ~~~typescript
> import { Module } from '@nestjs/common';
> import { AppController } from './app.controller';
> import { AppService } from './app.service';
> import { FooModule } from './foo/foo.module';
> 
> @Module({
>   imports: [FooModule.register({ foo: 'test' })],
>   controllers: [AppController],
>   providers: [AppService],
> })
> export class AppModule {}
> ~~~
>
> 在 `AppController` 中可以注入动态模块中导出的内容：
>
> ~~~typescript
> import { Controller, Get, Inject } from '@nestjs/common';
> 
> @Controller()
> export class AppController {
>     @Inject('FOO_OPTIONS')
>     private readonly fooOptions;
> 
>     @Get('foo')
>     getFoo(): string {
>       return JSON.stringify(this.fooOptions);
>     }
> }
> ~~~
>
> 使用 `GET` 方式访问 `/foo` 路由，得到响应如下：
>
> ~~~json
> {
>       "foo": "test"
> }
> ~~~

### 约定命名

上述示例中，在动态模块类中定义的用于返回 `DynamicModule` 的静态方法名可以任意命名，不过 `Nest` 约定了 一些方法名，分别对应不同的情况：

- `register`/`registerAsync`：用于每次注册时都调用一次 `register(...args)` 的情况，使得模块可以根据不同的环境或条件具有不同的配置。
- `forRoot`/`forRootAsync`：用于在整个应用中只调用一次 `forRoot(...args)` 的情况，每次注册时使用的都是第一次调用后得到的动态模块，常用于需要全局配置（如数据库连接）的场景。
- `forFeature`/`forFeatureAsync`：配合 `forRoot` 系列使用，一般在 `AppModule` 中引入了 `forRoot(...args)` 得到的模块，在局部 `Module` 中，再引入 `forFeature(...args)` 得到的模块使得可以传递一些额外的配置。比如用 `forRoot` 指定了数据库链接信息，再用 `forFeature` 指定某个模块访问哪个数据库和表。

一般在指定的 `Providers` 中有使用 `useFactory` 方法异步地返回内容的时候，静态方法的命名会以 `Async` 结尾。
