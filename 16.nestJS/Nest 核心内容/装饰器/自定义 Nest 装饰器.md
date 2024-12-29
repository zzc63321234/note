## 封装 `@SetMetadata()` 方法装饰器

`Nest` 中存在一个 `@SetMetadata()` 装饰器，用于为 `Controller` 或 `Handler` 附加元数据。该装饰器工厂接收两个参数，第一个参数是元数据的键，第二个参数是元数据的值。相对于直接使用 `@SetMetadata()`，更推荐将其添加到自定义的装饰器工厂中。

使用 `nest-cli` 创建一个自定义装饰器：

~~~bash
$ nest g decorator roles --no-spec --flat
~~~

以上命令行会在 `src` 目录下创建 `roles.decorator.ts`：

```typescript
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);
```

以上 `@Role()` 装饰器可以用在 `Handler` 上，为其附加角色权限信息。

## 方法装饰器聚合

如果一系列装饰器组合存在复用的情况，则可以将它们聚合，比如可以将与身份验证相关的所有装饰器在自定义装饰器 `@Auth()` 中使用通过 `Nest` 提供的 `applyDecorators` 辅助方法来调用：

~~~typescript
import { applyDecorators } from '@nestjs/common';

export function Auth(...roles: Role[]) {
  return applyDecorators(
    SetMetadata('roles', roles),
    UseGuards(AuthGuard, RolesGuard),
    ApiBearerAuth(),
    ApiUnauthorizedResponse({ description: 'Unauthorized"' })
  );
}
~~~

之后就可以使用自定义的 `@Auth()` 装饰器来应用这一系列的装饰器：

~~~typescript
......
@Get('users')
@Auth('admin')
findAllUsers() {}
......
~~~

注意：来自 `@nestjs/swagger` 依赖中的 `@ApiHideProperty()` 装饰器无法聚合，因此此装饰器无法正常使用 `applyDecorators` 方法。

## 自定义参数装饰器

可以使用 `Nest` 提供的 `createParamDecorator` 辅助方法来创建一个自定义参数装饰器。                           

`createParamDecorator` 方法接收一个函数：

- 该函数接收两个参数：

  - `data`：应用该自定义参数装饰器时传入给装饰器工厂的参数；

  - `ctx`：`ExecutionContext` 对象。

- 该函数的返回值会传递给被装饰的参数。

示例：

~~~typescript
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const Test = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    return data;
  },
);
~~~

上述自定义参数装饰器将传入给装饰器工厂的参数直接返回，使其传递给被装饰的参数。

实际上，`Nest` 内置的参数装饰器也可以使用这种方式实现，比如 ：

~~~typescript
import { createParamDecorator, ExecutionContext } from '@nestjs/common';
import { Request } from 'express';

export const MyHeaders = createParamDecorator(
  (key: string, ctx: ExecutionContext) => {
    const request: Request = ctx.switchToHttp().getRequest();
    return key ? request.headers[key.toLowerCase()] : request.headers;
  },
);
~~~

~~~typescript
export const MyQuery = createParamDecorator(
    (key: string, ctx: ExecutionContext) => {
        const request: Request = ctx.switchToHttp().getRequest();
        return request.query[key];
    },
);
~~~

自定义实现的参数装饰器和内置参数装饰器一样支持传入 `Pipe` 进行参数验证和转换：

~~~typescript
@Get()
async findOne(@User(new ValidationPipe()) user: UserEntity) {
  console.log(user);
}
~~~

