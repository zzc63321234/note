## 内置 `Pipe`

`Nest` 自带九个开箱即用的 `Pipe`，即：

- `ValidationPipe`
- `ParseIntPipe`
- `ParseFloatPipe`
- `ParseBoolPipe`
- `ParseArrayPipe`
- `ParseUUIDPipe`
- `ParseEnumPipe`
- `DefaultValuePipe`
- `ParseFilePipe`

他们从 `@nestjs/common` 包中导出。

### `Parse*Pipe`

在内置 `Pipe` 中，有很多是 `Parse*Pipe` 的形式，它们的主要作用是确保来自客户端的输入数据符合预期的格式，同时提供一种便捷的方法来处理和转换来自客户端的输入数据。

例如 `ParseIntPipe`，会将参数转换为 `Int` 数字类型：

~~~typescript
@Get(':id')
async findOne(@Param('id', ParseIntPipe) id: number) {
  return this.catsService.findOne(id);
}
~~~

这样在 `findOne()` 方法中接收的参数会是一个 `Int` 数，如果 `ParseIntPipe` 无法将其转换为 `Int` 数，则会抛出异常。例如请求路由是：`GET localhost:3000/abc`，那么 `Nest` 将返回如下错误响应：

~~~json
{
  "statusCode": 400,
  "message": "Validation failed (numeric string is expected)",
  "error": "Bad Request"
}
~~~

且 `fineOne()` 方法不会被执行。上述例子中，传递的是 `Parse*Pipe` 类，而不是它的实例，`Nest` 会处理它的实例化，使得可以实现依赖注入。如果自定义 `Parse*Pipe` 的行为，那么可以手动传入配置对象将其实例化：

~~~typescript
@Get(':id')
async findOne(
  @Param('id', new ParseIntPipe({ errorHttpStatusCode: HttpStatus.NOT_ACCEPTABLE }))
  id: number,
) {
  return this.catsService.findOne(id);
}
~~~

这样，如果参数无法符合预期，`Nest` 将返回如下响应错误：

~~~json
{
  "statusCode": 404,
  "message": "Validation failed (numeric string is expected)",
  "error": "Bad Request"
}
~~~

### `ValidationPipe`

见 `DTO`。

## 自定义 `Pipe`

自定义 `Pipe` 中的 `transform` 方法接收两个参数：`value` 和 `metadata`。

`value` 即是当前处理的源方法参数。`metadata` 是当前处理的方法参数的元数据对象，具有以下属性：

~~~typescript
export interface ArgumentMetadata {
  type: 'body' | 'query' | 'param' | 'custom';
  metatype?: Type<unknown>;
  data?: string;
}
~~~

这些属性描述了当前处理的参数：

- `type`：表示参数是来自 `@Body()`、`@Query()`、`@Param()` 还是自定义参数装饰器。
- `metaType`：表示参数的元类型，例如 `String`。取决于其在函数签名中声明的类型，如果省略了类型声明或使用的是原生 `JavaScript`，则为 `undefined`。由于 `TypeScript` 中的 `interface` 在转义后会消失，因此如果方法参数类型被声明为 `interface` 而非 `class`，或者其无法被转换为元类型，那么 `metaType` 的值将是 `Object`。
- `data`：传递给装饰器的字符串，如果没有传递则为 `undefined`。