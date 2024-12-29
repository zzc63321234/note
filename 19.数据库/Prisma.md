## 安装

~~~bash
$ npm i prisma -D
~~~

踩坑：`win11` 中有时需要关闭网络防火墙，且需要使用国内镜像，否则容易安装失败。

## `Prisma CLI` 常用命令

[Prisma CLI 参考 | Prisma 中文网 (nodejs.cn)](https://prisma.nodejs.cn/orm/reference/prisma-cli-reference)

### `init`

用于初始化。

~~~bash
$ npx prisma init
~~~

这会在项目目录下生成一个 `prisma` 目录和一个 `.env` 文件。

`.env` 文件中设置 `DATABASE_URL`。

`prisma/schema.prisma` 文件中编写 `schema`。

需要在这两个文件中指定数据库类型。也可以在初始化时使用参数指定，如：

~~~bash
$ npx prisma init --datasource-provider mysql
~~~

### `db`

用于同步数据库和 `schema` 文件。

将数据库中的表同步给 `schema` 文件：

~~~bash
$ npx prisma db pull
~~~

将 `schema` 文件同步给数据库中的表，并生成 `Prisma Client`：

~~~bash
$ npx prisma db push
~~~

执行脚本，插入初始数据到数据库（即播种）：

~~~bash
$ npx prisma db seed
~~~

播种命令本质上就是执行 `package.json` 中的 `prisma` 键中的 `seed` 键中的命令，见 [播种 | Prisma 中文网 (nodejs.cn)](https://prisma.nodejs.cn/orm/prisma-migrate/workflows/seeding)。

###  `migrate`

用于迁移表结构，并且会生成迁移记录。

使用 `prisma migrate dev` 会根据 `schema` 的变化在 `prisma/migrations` 中生成 `sql` 文件，并执行这个 `sql`，还会生成 `Prisma Client`，数据库中会生成一个 `_prisma_migrations` 表，记录 `migration` 历史。使用 `--name` 选项来为这次迁移命名。

~~~shell
$ npx prisma migrate dev --name init
~~~

执行后，如果当前数据库表的状态与 `_prisma_migrations` 中的迁移历史的最新状态不一致，那么会提示是否需要 `reset`，如果 `reset`，那么就会重置表，并执行 `_prisma_migrations` 中的所有 `migration`。另外也可以使用命令手动触发 `reset`：

~~~bash
$ npx prisma migrate reset
~~~

### `generate`

用于生成 `Prisma Client`，它会根据 `schema` 文件的定义，在 `node_modules` 中的 `.prisma/client` 下生成代码，用于 `CRUD`。

### `studio`

用于开启一个图形界面。

### `validate`

用于检查 `schema.prisma` 文件是否有语法错误。可以在编辑器中安装 `prisma` 插件，可以直接在编辑器中检查错误而代替这个命令。

### `format`

用于格式化 `schema.prisma` 文件。同样可以用插件替代。

## `Prisma Schema`

`prisma` 文件中使用的语法是 `PSL`（`Prisma Schema Language`），它是一种 `DLS`（`Domain Specific Language`，领域特定语言）。

这是一个初始化后得到的 `schema.prisma` 文件：

~~~psl
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}
~~~

### `generator`

`generator` 用于定义生成器，以生成所需的资源：

~~~psl
generator client {
  provider = "prisma-client-js"
  output   = "./generated/prisma-client-js"
}
~~~

`provider` 属性定义生成什么类型的资源，目前官方提供的只有 `prisma-client-js`，即生成 `Prisma Client`。也可以使用社区提供的 `provider`，见 [生成器（参考） | Prisma 中文网 (nodejs.cn)](https://prisma.nodejs.cn/orm/prisma-schema/overview/generators#社区生成器)。

可选的 `output` 用于指定生成的 `client` 的目录，默认为 `node_modules` 中的 `.prisma/client` 目录。

一个 `schema` 中可以定义多个 `generator`：

~~~sql
generator client {
  provider = "prisma-client-js"
  output   = "../generated/client"
}

generator json {
  provider = "prisma-json-schema-generator"
  output   = "../generated/json"
}

generator docs {
  provider = "node node_modules/prisma-docs-generator"
  output   = "../generated/docs"
}
~~~

这样除了 `client` 以外，还可以使用社区提供的 `provider` 将 `schema` 转换为 `json` 和文档。

### `datasource`

`datasource` 用于配置数据库连接信息：

~~~psl
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}
~~~

`provider` 属性用于指定连接的数据库的类型。

`url` 属性用于指定连接的 `url`。

### `model` / `enum`

`model` 和 `enum` 用于定义映射数据库表结构的模型，其中 `enum` 定义的是一个枚举值，可以作为 `model` 的字段类型使用：

~~~psl
model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  name    String?
  role    Role     @default(USER)
  posts   Post[]
  profile Profile?
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String
  user   User   @relation(fields: [userId], references: [id])
  userId Int    @unique
}

model Post {
  id         Int        @id @default(autoincrement())
  createdAt  DateTime   @default(now())
  updatedAt  DateTime   @updatedAt
  title      String
  published  Boolean    @default(false)
  author     User       @relation(fields: [authorId], references: [id])
  authorId   Int
  categories Category[]
}

model Category {
  id    Int    @id @default(autoincrement())
  name  String
  posts Post[]
}

enum Role {
  USER
  ADMIN
}
~~~

`model` 中定义的属性称为字段，其中包括：

- 一个字段名称

- 一个字段类型

  - 字段的类型可以是数据库中的类型，也可以是一个其它的 `model`，这样该字段就成为了一个关系字段。例如上面的 `posts Post[]`

- 可选的类型修饰符

  - 可以使用修饰符 `[]` 将字段设为列表（写入数据时会存储为 `JSON` 形式的数组）
  - 使用修饰符 `?` 将字段设为可选的

- 可选的表示原始数据库类型的属性

  - 见 [Prisma 架构 API | Prisma 中文网 (nodejs.cn)](https://prisma.nodejs.cn/orm/reference/prisma-schema-reference#attributes)

  - 分为以 `@` 为前缀的字段属性和以 `@@` 为前缀的块属性
  - 可以使用 `@db` 提供的属性定义原生数据库的字段类型，如 `@db.VarChar(50)`
  - 常用的有：
    - `@id` 定义主键
    - `@default` 定义默认值
    - `@map` 定义字段在数据库中的名字
    - `@db.xx` 定义对应的具体类型
    - `@updatedAt` 定义更新时间的列 
    - `@unique` 添加唯一约束
    - `@relation` 定义外键引用
    - `@@map` 定义表在数据库中的名字
    - `@@index` 定义索引
    - `@@id` 定义联合主键

创建一对多关系示例：

~~~psl
model Department {
  id        Int    @id @default(autoincrement())
  name      String  @db.VarChar(20)
  createTime DateTime @default(now())
  updateTime DateTime @updatedAt
  employees     Employee[]
}

model Employee {
  id         Int       @id @default(autoincrement())
  name      String     @db.VarChar(20)
  phone     String     @db.VarChar(30)

  deaprtmentId Int
  department     Department      @relation(fields: [deaprtmentId], references: [id])
}
~~~

创建多对多关系示例：

~~~psl
model Post {
  id        Int          @id @default(autoincrement())
  title     String
  content   String?
  published Boolean      @default(false)
  tags      TagOnPosts[]
}

model Tag {
  id    Int          @id @default(autoincrement())
  name  String
  posts TagOnPosts[]
}

model TagOnPosts {
  post   Post @relation(fields: [postId], references: [id])
  postId Int

  tag    Tag @relation(fields: [tagId], references: [id])
  tagId  Int

  @@id([postId, tagId])
}
~~~

多对多需要创建一个中间表，中间表里有两个外键分别关联这两个表，需要使用 `@@id()` 创建一个块属性来定义一个联合主键。

##  `Prisma Client`

### 实例化 `PrismaClient`

使用命令创建了 `Prisma Client` 后，就能在代码中引入它并实例化 `PrismaClient`：

~~~typescript
import { PrismaClient } from '@prisma/client'
const prisma = new PrismaClient()
// use `prisma` in your application to read and write data in your DB
~~~

`@prisma/client/index.d.ts` 导出了 `.prisma/client`：

~~~typescript
export * from '.prisma/client'
~~~

每次对 `Prisma` 架构进行更改后，都需要重新运行 `prisma generate` 命令以更新生成的 `Prisma` 客户端代码。

`PrismaClient` 类也可以接收参数，比如如下方式会将所有的查询日志答应到标准输出（控制台）中：

~~~typescript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient({
  log: [
    {
      emit: 'stdout',
      level: 'query'
    },
  ],
});
~~~

相关 `API` 可以在 `prisma-docs-generator` 生成的文档中查看。文档中涉及到的类型来自 `@prisma/client` 导出的 `Prisma` 命名空间。

