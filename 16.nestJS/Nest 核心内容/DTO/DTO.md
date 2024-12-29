## 什么是 `DTO`

`DTO`（`Data Transfer Object`）是一种用于描述数据模型和数据传输的对象。它是一种在不同应用程序之间，或在应用程序的不同层之间传输数据的设计模式。

在 `Nest` 中，`DTO` 主要用于在 `Controller` 和 `Service` 中。对于 `Controller` 而言，`DTO` 是用于接收 `HTTP` 请求数据的一种数据结构，用于将 `HTTP` 请求中的数据转换成应用程序需要的数据格式。对于 `Service` 而言，`DTO` 是用于传递数据模型的一种数据结构，用于将 `Service` 返回的数据转换成适合客户端应用程序的格式。

## `DTO` 基本使用

在 `Nest` 中通常使用 `class` 定义 `DTO`，可以使用 `nest-cli` 快速创建一个 `class`：

~~~bash
$ nest g class test.dto --no-spec --flat
~~~

在生成的 `class` 中这样定义：

~~~typescript
export class TestDto {
  readonly name: string;
  readonly age?: number;
}
~~~

在处理 `Post` 请求的 `Handler` 中为 `@Body()` 注入的参数的类型定义指定为 `DTO` 类：

~~~typescript
  @Post()
  Test(@Body() body: TestDto): string {
    return JSON.stringify(body);
  }
~~~

这样，在 `handler` 中，`body` 的类型会被定义为 `TestDto` 的实例类型。

## `DTO` 转换工具

`@nestjs/mapped-types` 包中提供了一些 `DTO` 类型转换工具。

例如 `PartialType`，可以在一个 `DTO` 的基础上创建一个成员属性全是可选类型的 `DTO`：

~~~typescript
// partial-test.dto.ts
import { PartialType } from '@nestjs/mapped-types';
import { Test } from './test.dto';
// PartialType代表所有参数可选
export class PartialTest extends PartialType(Test){}
~~~

## 验证参数

`DTO` 可以配合 `ValidationPipe` 进行参数验证。 

`ValidationPipe` 还依赖这两个库，需自行安装：

~~~bash
$ npm i class-validator class-transformer@0.3.1
~~~

- 测试中发现，`class-transformer 0.4`   以上版本与 `nest` 脚手架中自带的 `@nestjs/mapped-types 0.1.0` 版本配合使用会报错，需要自行升级或降级至以下组合：
  - `class-transformer 0.3.1` 和  `@nestjs/mapped-types 0.1.0` （都使用老版本）
  - `class-transformer 0.4` 以上版本和  `@nestjs/mapped-types 0.3.0` 以上版本（都使用新版本）

在 `DTO` 中添加规则：

~~~typescript
import { IsString, IsNumber } from 'class-validator';

export class TestDto {
  @IsString()
  readonly name: string;

  @IsNumber()
  readonly age: number;
}
~~~

在 `Handler` 中使用  `@Body()` 时传入 `ValidationPipe`，使用的 `DTO` 和之前例子中的相同：

~~~typescript
  @Post()
  Test(@Body(ValidationPipe) body: TestDto): string {
    return JSON.stringify(body);
  }
~~~

或者也可以全局应用 `ValidationPipe` 使得所有使用 `DTO` 接收的参数都进行类型验证。这样如果传入的参数不符合预期，`Nest` 就会返回错误响应。

默认情况下，如果接收的 `body` 中有 `DTO` 中没有覆盖的字段，也能被正常接收。例如为上面 `Handler` 的路由发送 `POST` 请求，请求体如下：

~~~json
{
    "name": "z",
    "age": 1,
    "foo": "foo"
}
~~~

那么该 `Handler` 返回的响应也是这个 `json`。

如果希望过滤掉 `DTO` 中没有覆盖的字段，可开启 `ValidationPipe` 的白名单模式：

~~~typescript
  @Post()
  Test(@Body(new ValidationPipe({ whitelist: true })) body: TestDto): string {
    return JSON.stringify(body);
  }
~~~

这样，再次发送同样的亲求，`Handler` 返回的响应如下：

~~~json
{
    "name": "z",
    "age": 1
}
~~~

没有被 `DTO` 包含的 `foo` 字段被过滤掉了。

如果希望当传入数据包含 `DTO` 中没有覆盖的字段时抛出异常，可以再开启 `forbidNonWhitelisted` 模式：

~~~typescript
  @Post()
  Test(
    @Body(new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true }))
    body: TestDto,
  ): string {
    return JSON.stringify(body);
  }
~~~

这样再次发送同样的请求，`Nest` 会返回如下异常响应：

~~~json
{
    "message": [
        "property foo should not exist"
    ],
    "error": "Bad Request",
    "statusCode": 400
}
~~~

## 参数转换

默认情况下，即使传入的参数符合 `DTO` 的预期，但该参数并不是 `DTO` 类的实例，通过设置 `ValidationPipe` 的 `transform` 选项为 `true`，可以将参数对象转换为 `DTO` 类的实例：

~~~typescript
  @Post()
  Test(
    @Body(
      new ValidationPipe({
        transform: true,
      }),
    )
    body: TestDto,
  ): string {
    console.log(body instanceof TestDto); // true
    return JSON.stringify(body);
  }
~~~

`transform` 模式除了可以将参数转换为 `DTO` 实例，也可以转换一些基础类型：

~~~typescript
  @Get('/:a')
  getHello(
    @Param('a', new ValidationPipe({ transform: true })) a: number,
  ): string {
    console.log(typeof a); // 'number'
    return this.appService.getHello();
  }
~~~

上述 `Handler` 中，通过路由参数传递进的参数 `a` 会被转换为 `number` 类型。但如果传入的参数无法被转换为数字，则会变成 `NaN`。

## `class-validator`

[class-validator 校验器的使用 - 掘金 (juejin.cn)](https://juejin.cn/post/7083505497021612068?searchId=2024012620410982ED4FF940335A3ED565)

以下示例 `DTO` 包含一些 `class-validator` 库提供的常见验证方式：

~~~typescript
import {
  Contains,
  IsDate,
  IsEmail,
  IsFQDN,
  IsInt,
  Length,
  Max,
  Min,
} from 'class-validator';

import { Transform } from 'class-transformer';

export class TestDto {
  @Length(10, 20)
  title: string;

  @Contains('hello')
  text: string;

  @IsInt()
  @Min(0)
  @Max(10)
  rating: number;

  @IsEmail()
  email: string;

  @IsFQDN()
  site: string;

  /*
   * @Transform() 会将接收的对象的值进行转换。
   * toClassOnly 表示这个转换只会在将普通对象转换为类示例时应用。
   * Nest 中通过 ValidationPipe 和 DTO 对参数进行验证时，会通过 class-transformer 将参数转换为 DTO 类的实例，因此可以开启 toClassOnly 选项。
  */
  @IsDate()
  @Transform(({ value }) => new Date(value), { toClassOnly: true })
  createDate: Date;
}
~~~

当参数中的字段校验不通过时会返回错误响应。通过向这些装饰器中传入配置对象，在 `message` 字段中可以自定义错误消息。`message` 字段可以直接传入错误消息字符串，也可以传入一个函数，接收一个 `arg` 对象，对象中包含是字段有：`targetName`、`property`、`object`、`value`、`constraints`，可以使用这些字段的值组织返回的错误消息字符串，让 `message` 函数返回。