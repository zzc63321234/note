`Swagger` 实现了 `OpenAPI` 规范。`OpenAPI` 是一种用于描述 `RESTful API` 的格式。

## 基本使用

`Nest` 提供了 `@nestjs/swagger` 来集成它。对于 `express`，需要安装 `swagger-ui-express`。对于 `fastify`，需要安装 `fastify-swagger`。

安装：

~~~bash
$ npm i @nestjs/swagger swagger-ui-express
~~~

在 `main.ts` 中使用 `SwaggerModule` 初始化 `Swagger`：

~~~typescript
import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { ApplicationModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(ApplicationModule);
  
  // 构建 Swagger 选项
  const options = new DocumentBuilder()
    .setTitle('Cats example') // 设置API的标题
    .setDescription('The cats API description') // 设置API的描述
    .setVersion('1.0') // 设置API的版本
    .addTag('cats') // 添加标签
    .build(); // 构建选项
  const document = SwaggerModule.createDocument(app, options);
  // 将 Swagger UI 挂载到路径 /api 上
  SwaggerModule.setup('api', app, document);

  await app.listen(3000);
}
bootstrap();
~~~

应用程序运行后，打开浏览器并导航到 `http://localhost:3000/api`，即可看到 `Swagger UI`。另外可以访问 `http://localhost:3000/api-json` 生成并下载 `Swagger` `JSON` 文件。

## 路由注解

`SwaggerModule` 会为 `Handler` 生成对应的 `API` 文档。

 `@ApiOperation()` 装饰器用于为 `Handler` 对应的  `API` 文档添加基本信息说明：

> ~~~typescript
> @ApiOperation({ summary: '这是概述', description: '这是描述' })
> ~~~

`@ApiResponse()` 装饰器用于为 `API` 添加响应说明：

> 添加响应码为 `200` 时的说明：
>
> ~~~typescript
> @ApiResponse({
> status: HttpStatus.OK,
> description: '成功',
> type: String
> })
> ~~~
>
> 添加响应码为 `401` 时的说明：
>
> ~~~typescript
> @ApiResponse({
>  status: HttpStatus.UNAUTHORIZED,
>  description: 'id 不合法'
> })
> ~~~
>
> 响应类型也可以使用一个 `VO`（`View Object`）类：
>
> ~~~typescript
> @ApiResponse({
>   status: HttpStatus.OK,
>   description: '成功',
>   type: TestVo
> })
> @Get('test')
> test(){
> const result =  new TestVo();
>   result.a = 'a'
>   result.n = 1
>   return result
> }
> ~~~
>
> ~~~typescript
> export class TestVo {
>   @ApiProperty()
>   a: string;
>     
>   @ApiProperty()
>   b: number;
> }
> ~~~
>

`@ApiQuery()` 、`@ApiParam()` 装饰器用于为 `API` 添加 `query` 、`param` 参数说明：

> ~~~typescript
> @ApiQuery({
>     name: 'a1',
>     type: String,
>     description: 'a1 param',
>     required: false,
>     example: '1111',
> })
> @ApiQuery({
>     name: 'a2',
>     type: Number,
>     description: 'a2 param',
>     required: true,
>     example: 2222,
> })
> @ApiParam({
>     name: 'id',
>     description: 'ID',
>     required: true,
>     example: 222,
> })
> ~~~

`@ApiBody()` 装饰器用于为 `API` 添加请求体说明：

> ~~~typescript
> @ApiBody({
>     type: TestDto
> })
> ~~~

`@ApiResponse()` 和 `@ApiBody()` 中使用的 `VO` 和 `DTO` 会被生成在文档的 `Schemas` 中。但是它们中的属性定义需要通过 `@ApiProperty()` 装饰器来注解，使得它们可以被 `SwaggerModule` 获取到：

> ~~~typescript
> import { ApiProperty } from '@nestjs/swagger';
> 
> export class CreateCatDto {
>   @ApiProperty()
>   name: string;
> 
>   @ApiProperty()
>   age: number;
> 
>   @ApiProperty()
>   breed: string;
> }
> ~~~
>
> `@ApiProperty()` 常见参数：
>
> - `description`：
>
>   描述属性的信息。
>
> - `required`：
>
>   是否是必须的，默认为 `true`。
>
> - `default`：
>
>   默认值。
>
> - `example`：
>
>   示例的值。
>
> - `minium`、`maxumum`：
>
>   最小值和最大值，适用于 `number` 类型。
>
> - `minLength`、`maxLength`：
>
>   最小长度和最大长度，适用于 `string` 类型。
>
> - `enum`：
>
>   描述枚举类型，可以是数值数组，或是 `TypeScript` 枚举。
>
> 当属性为一个数组时：
>
> > 需要显式指定 `type`：
> >
> > ~~~typescript
> > @ApiProperty({ type: [String] })
> > names: string[];
> > ~~~
> >
> > 或将 `isArray` 设置为 `true`：
> >
> > ~~~typescript
> > @ApiProperty({ isArray: true })
> > names: string[];
> > ~~~

可以对 `Controller` 使用 `@ApiTags('分组名')` 装饰器来对装饰器中的 `Handler` 对应的 `API` 进行分组，也可以单独对 `Handler` 使用：

> ~~~typescript
> @ApiTags('user')
> @Controller('user')
> ~~~
>
> ~~~typescript
> @ApiTags('user-get')
> @Get('test')
> ~~~

对于需要权限的才能访问的 `API`，可以使用 `@ApiBearerAuth()`、`@ApiCookieAuth()`、`@ApiBasicAuth()` 等装饰器来进行注解：

> 文档中，使用了这些装饰器的 `Handler` 对应的 `API`，当在 `Swagger UI` 中执行它们时，会要求输入对应的凭证。
>
> [认证 (nestjs.cn)](https://docs.nestjs.cn/10/recipes?id=基础认证)
>
> 需要在 `main.ts` 中对 `Swagger` 选项添加对应认证配置：
>
> ~~~typescript
> const config = new DocumentBuilder()
>     .setTitle('Test example')
>     .setDescription('The API description')
>     .setVersion('1.0')
>     .addTag('test')
>     .addBasicAuth({
>       type: 'http',
>       name: 'basic',
>       description: '用户名 + 密码'
>     })
>     .addCookieAuth('sid', {
>       type: 'apiKey',
>       name: 'cookie',
>       description: '基于 cookie 的认证'
>     })
>     .addBearerAuth({
>       type: 'http',
>       description: '基于 jwt 的认证',
>       name: 'bearer'
>     })
>     .build();
> ~~~

## `Swagger` 插件

[插件 (nestjs.cn)](https://docs.nestjs.cn/10/recipes?id=插件)

`Nest` 提供了一个 `Swagger` 插件，可以增强 `TypeScript` 编译过程，以减少所需的样板代码量。

Swagger 插件会自动：

- 除非使用 `@ApiHideProperty`，否则用 `@ApiProperty` 注释所有 DTO 属性
- 根据问号标记设置 `required` 属性（例如，`name?: string` 将设置 `required: false`）
- 根据类型设置 `type` 或 `enum` 属性（也支持数组）
- 根据分配的默认值设置 `default` 属性
- 根据 `class-validator` 装饰器设置多个验证规则（如果 `classValidatorShim` 设置为 `true`）
- 向具有正确状态和 `type`（响应模型）的每个端点添加响应装饰器

请注意，你的文件名必须包含如下前缀之一：`[‘.dto.ts’, ‘.entity.ts’]` 从而能让插件对其进行分析。

为了启用该插件，只需打开 `nest-cli.json` 并添加以下`plugins`配置：

~~~json
{
  "collection": "@nestjs/schematics",
  "sourceRoot": "src",
  "compilerOptions": {
    "plugins": ["@nestjs/swagger/plugin"]
  }
}
~~~

还可以使用 `options` 属性自定义插件的行为：

~~~json
"plugins": [
  {
    "name": "@nestjs/swagger/plugin",
    "options": {
      "dtoFileNameSuffix": [".dto.ts", ".entity.ts", ".vo.ts"]
    }
  }
]
~~~

`options` 属性必须满足以下接口：

```typescript
export interface PluginOptions {
  dtoFileNameSuffix?: string[];
  controllerFileNameSuffix?: string[];
  classValidatorShim?: boolean;
}
```

| 选项(Option)               | 默认(Default)               | 描述(Description)                                            |
| :------------------------- | :-------------------------- | :----------------------------------------------------------- |
| `dtoFileNameSuffix`        | `['.dto.ts', '.entity.ts']` | `DTO`（数据传输对象）文件后缀                                |
| `controllerFileNameSuffix` | `.controller.ts`            | 控制器文件后缀                                               |
| `classValidatorShim`       | `true`                      | 如果设置为 `true`，则模块将重用 `class-validator` 验证装饰器 (例如 `@Max(10)` 会将 `max: 10` 添加到 schema 定义中) |
