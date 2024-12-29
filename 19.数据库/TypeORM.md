

`ORM` 是 `Object Relation Mapping`，即对象关系映射。可以将关系型数据库的表映射成面向对象的 `class`。

`TypeORM` 是一个支持多种数据库的 `ORM` 库，允许使用 `TS` 或 `JS` 编写数据库相关的代码，而不必直接操作 `SQL` 语句。

## `TypeORM` 基本使用

使用 `npm` 全局安装 `TypeORM`：

~~~bash
$ npm install typeorm --global
~~~

新建一个 `typeorm` 项目：

> ~~~bash
> $ typeorm init --name typeorm-test --database mysql
> ~~~
>
> `--name` 用来指定项目名称。
>
> 需使用 `--database` 用来指定数据库类型，因为 `TypeORM` 除了支持 `MySQL`，还支持 `postgres`、`oracle`、`sqllite` 等数据库。
>
> 执行命令后会在当前目录下新建一个项目文件夹 `typeorm-test`。

项目中 `src/data-source.ts` 中的 `AppDataSource` 是 `TypeORM` 的数据源，用于配置和初始化应用程序和数据库之间的连接：

>~~~typescript
>// 引入reflect-metadata库，这是为了使用装饰器和反射元数据的特性
>import "reflect-metadata";
>// 从typeorm库中引入DataSource类，用于配置和创建数据库连接
>import { DataSource } from "typeorm";
>// 引入User实体，表示我们的数据库中的一个用户表
>import { User } from "./entity/User";
>
>// 导出一个预配置的DataSource实例，用于应用程序的数据库连接
>export const AppDataSource = new DataSource({
>  type: "mysql", // 指定数据库类型为 MySQL
>  host: "localhost", // 数据库主机地址
>  port: 33061, // 数据库端口号
>  username: "root", // 数据库用户名
>  password: "test", // 数据库密码
>  database: "test_db", // 要连接的 database 名称
>  synchronize: true, // 设置为 true 则会将 Entity 类同步映射到数据库中表的结构
>  logging: true, // 启用日志记录，会在控制台输出执行的 SQL 语句
>  entities: [User], // 指定要使用的 entity 类，typeorm 将使用它们来创建数据库表
>  migrations: [], // 指定迁移脚本的数组，用于数据库迁移
>  subscribers: [], // 指定订阅者数组，用于实体监听
>  poolSize: 10, // 连接池大小
>  connectorPackage: "mysql2", // 指定数据库驱动为mysql2，对MySQL有更好的支持
>  extra: { // 额外的数据库连接配置
>    authPlugin: "sha256_password", // 指定认证插件为sha256_password
>  },
>});
>~~~
>
>`TypeORM` 默认使用 `mysql` 包来连接 `MySQL` 数据库，可以使用 `connectorPackage` 将数据库驱动指定为 `mysql2` 以获取更高的性能，需要在项目中安装 `mysql2` 依赖。
>
>新版的 `MySQL` 使用的认证插件是 `sha256_password`，需要在 `extra.authPlugin` 中指定。
>
>如果开启了 `synchronize`，则会将 `Entity` 映射到数据库的对应表中。也就是说，如果没有开启 `stnchronize`，`TypeORM` 就不会修改数据库中的表结构以匹配 `Entity` 类，但仍可以进行 `CRUD` 操作。在生产环境中不建议开启。

在 `src/entity` 中放置 `Entity` 类，它会定义表的结构并映射到数据库中：

> `src/entity` 中默认有一个示例的 `User.ts` 文件：
>
> ~~~typescript
> import { Entity, PrimaryGeneratedColumn, Column } from "typeorm";
> 
> // 使用 @Entity 装饰器定义一个类作为数据库表的映射
> @Entity()
> export class User {
> 
>     // 使用 @PrimaryGeneratedColumn 装饰器定义一个主键列，该列的值将自动生成
>     @PrimaryGeneratedColumn()
>     id: number
> 
>     // 使用 @Column 装饰器定义一个普通列，用于存储用户的名字
>     @Column()
>     firstName: string
> 
>     // 使用 @Column 装饰器定义一个普通列，用于存储用户的姓氏
>     @Column()
>     lastName: string
> 
>     // 使用 @Column 装饰器定义一个普通列，用于存储用户的年龄
>     @Column()
>     age: number
> 
> };
> ~~~
>
> `Entity` 类需要在实例化 `DataSource` 时同通过 `entities` 字段指定。
>
> `User` 类通过装饰器与数据库表进行映射。以上 `Entity` 生成的 `SQL` 语句如下：
>
> ~~~sql
> CREATE TABLE `user`
>     `id` INT NOT NULL AUTO_INCREMENT, 
>     `firstName` VARCHAR(255) NOT NULL,
>     `lastName` VARCHAR(255) NOT NULL,
>     `age` INT NOT NULL, 
>     PRIMARY KEY (`id`)
> ENGINE=InnoDB
> ~~~
>
> 这里这里装饰器工厂 `@Entity`、`@PrimaryGeneratedColumn`、`@Column` 中没有传入任何参数，因此都使用了默认配置。默认会将 `TS` 中的 `number`、`string` 类型映射为了 `SQL` 中的 `INT`、`VARCHAR(255)` 类型。默认会使用 `Entity` 类名的小写作为表名。

在 `src/index.ts` 中，会将 `AppDataSource` 初始化，并可以对数据库进行增删改查操作：

> ~~~typescript
> import { AppDataSource } from "./data-source"
> import { User } from "./entity/User"
> 
> // 初始化 AppDataSource，然后执行异步操作
> AppDataSource.initialize().then(async () => {
>     // 向 user 表中插入一条新的行
>     console.log("Inserting a new user into the database...")
>     const user = new User() // 实例化 Entity
>     user.firstName = "Timber" // 设置 firstName 字段
>     user.lastName = "Saw" // 设置 lastName 字段
>     user.age = 25 // 设置 age 字段
>     await AppDataSource.manager.save(user) // 将实例中的信息映射到数据库的表中
>     console.log("Saved a new user with id: " + user.id) // Entity 实例可以获取到自动生成的主键
> 
>     // 从数据库中加载用户的日志
>     console.log("Loading users from the database...")
>     const users = await AppDataSource.manager.find(User) // 查找并加载所有用户
>     console.log("Loaded users: ", users) // 输出加载的用户
> 
>     // 在这里可以设置并运行 express / fastify / 任何其他框架
>     console.log("Here you can setup and run express / fastify / any other framework.")
> 
> }).catch(error => console.log(error)) // 如果初始化或异步操作中有任何错误，捕获并打印错误
> ~~~

## `Entities`

### 基本使用

以下是一个示例的 `Entity` 类，涵盖了一些基本使用场景：

> ~~~typescript
> import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn, UpdateDateColumn, OneToMany, ManyToOne, OneToOne, JoinColumn } from 'typeorm';
> 
> @Entity('sample_table') // 'sample_table' 是这个实体对应的数据库表名
> export class SampleEntity {
> @PrimaryGeneratedColumn()
> id: number; // 主键，自动生成
> 
> @Column({ type: 'varchar', length: 255 })
> name: string; // 定义一个 VARCHAR(255) 类型的列
> 
> @Column({ type: 'int' })
> age: number; // 定义一个 INT 类型的列
> 
> @Column({ type: 'boolean', default: true })
> isActive: boolean; // 带有默认值为 true 的 BOOLEAN 类型列
> 
> @CreateDateColumn()
> createdAt: Date; // 自动生成的创建时间列
> 
> @UpdateDateColumn()
> updatedAt: Date; // 自动生成的更新时间列
>     
> @DeleteDateColumn()
> deleteAt: Date; // 自动生成的删除时间列，用于软删除
> 
> @Column({ type: 'text', nullable: true })
> description: string | null; // 可以为null的text类型列
> 
> @Column({ type: 'decimal', precision: 5, scale: 2 })
> salary: number; // 定义一个 DECIMAL(5,2) 类型的列，precision 表示总位数，scale 表示小数点后的位数
> }
> ~~~
>
> 以上 `Entity` 生成的 `SQL` 语句如下：
>
> ~~~sql
> CREATE TABLE `sample_table` (
> `id` INT NOT NULL AUTO_INCREMENT,
> `name` VARCHAR(255) NOT NULL,
> `age` INT NOT NULL,
> `isActive` TINYINT NOT NULL DEFAULT 1,
> `createdAt` DATETIME(6) NOT NULL DEFAULT CURRENT_TIMESTAMP (6),
> `updatedAt` DATETIME(6) NOT NULL DEFAULT CURRENT_TIMESTAMP (6) ON UPDATE CURRENT_TIMESTAMP (6),
> `description` TEXT NULL,
> `salary` DECIMAL(5 , 2 ) NOT NULL,
> PRIMARY KEY (`id`)
> )  ENGINE=INNODB
> ~~~
>
> 装饰器用法：
>
> - `@Entity()`：
>   - 作用于 `Entity` 类本身，声明这是一个 `Entity`。
>   - 可以通过以下方式为 `Entity` 另外指定表名：
>     - `@Entity('sample_table')`
>     - `@Entity({ name: 'sample_table' })`
>   - 如果 `DataSource` 中指定了多个数据库，可以在此指定数据库名称：
>     - `@Entity({ database: 'sample_db' })`
> - `@Column()`：
>   - 表示当前成员属性会被映射为一个字段。
>   - 可配置的选项有：
>     - `name`：另外指定字段名。
>     - `type`：指定字段类型。
>       - 如果 `type` 为 `varchar`，则使用 `length` 属性指定它的长度。
>       - 如果 `type` 为 `decimal`，则使用 `precision` 属性指定它的精度（总位数），使用 `scale` 属性指定小数点后的位数。
>     - `default`：指定默认值。
>     - `nullable`：设置 `NOT NULL` 约束，默认为 `true`。
>     - `unique`：设置 `UNIQUE` 约束，默认为 `false`。
> - `@PrimaryGeneratedColumn()`：
>   - 表示当前成员属性会被映射为一个自增的主键字段。
>   - 主键字段具有 `NOT NULL`，`UNIQUE` 约束，其余可配置的选项同 `@Column()`。
> - `@CreateDataColumn`、`@UpdateColumn`：
>   - 表示当前成员属性会被映射为自动生成的时间戳字段，分别用于记录创建和更新时间。
> -  `@DeleteDateColumn()`
>   - 表示当前成员属性会映射为自动生成的时间戳字段，用于记录该条记录被软删除的时间。

### 关联关系

`Entity` 类之间可以设置关联关系：

> `TypeORM` 提供了不同装饰器来定义 `Entity` 类之间的关联关系：
>
> - 其中，`@OneToOne()`、`@ManyToMany()` 等关系装饰器本身并不会生成对应的 `SQL` 建表语句来映射数据库中表的结构，而是用来帮助 `TypeORM` 理解不同 `Entity` 之间的关系，使得 `TypeORM` 能在进行数据库操作时能自动处理这些 `Entity` 之间的关系。
> - 而 `@JoinColumn()` 和 `@JoinTable()` 则会生成对应 `SQL` 建表语句来映射数据库中的表。`@JoinColumn()` 会在当前 `Entity` 对应的表中建立外键，而 `@JoinTable` 则会生成多对多关系的中间表。
>
> 关系装饰器的使用：
>
> > 关系装饰器可以用于关系的一侧（单向关系）或双侧（双向关系）。`QueryBuilder` 中可以从使用了关系装饰器的方向上连接关系，因此对于双向关系，在两个方向上都可以连接关系。无论是单向关系还是双向关系，`@JoinColumn` 都必须且仅在需要拥有外键的一侧上设置。
> >
> > 关系装饰器最多接收三个参数：
> >
> > - 定义关系的实体类：
> >
> >   第一个参数通常是一个返回关联实体类的函数。为了避免循环依赖，将实体类放在函数中返回，使得互相引用的实体类可以各自加载完成后再从函数中取到依赖。
> >
> >   例如在 `@OneToOne(() => User)` 中，`User` 是要关联的实体类。
> >
> > - 定义反向关系：
> >
> >   对于双向关系，第二个参数是一个函数，返回定义在关联实体上的相对于的关系属性。这使得两个实体都能通过关系属性访问对方。
> >
> >   例如 `@OneToOne(() => User, (user) => user.profile)` 表示 `User` 是要关联的实体类，而 `User` 中定义的关系属性 `profile` 用来反向关联当前实体。而在关联的 `User` 实体类中也需要这么定义：`@OneToOne(() => Profile, (profile) => profile.user)` 
> >
> > - 关系选项参数对象，可以为关系指定几个选项：
> >
> >   - `eager: boolean`
> >
> >     如果设置为 `true`，那么在使用 `find*` 相关方法或对此实体使用 `QueryBuilder` 时，关系实体将始终和主实体合并后一起加载（将自动开启 `find*()` 相关方法中的 `relations` 查找选项，查询构建器中会自动调用 `leftJoinAndSelect()` 方法）。
> >
> >   - `cascade: boolean | ("insert" | "update" | "remove" | "soft-remove" | "recover")[]`
> >
> >     用于设置级联操作。如果设置为 `true`，那么当在这个实体的实例中插入或更新了关联的实体实例，那么当使用 `save()` 保存当前实体实例时，插入或更新的关联实体的实例也会被同时保存，而不需要手动 `save` 它们，这就是级联操作。还可以用数组指定开启哪些级联操作，而 `true` 相当于开启了全部级联操作。
> >
> >     这里的 `cascade` 选项主要影响的是 `TypeORM` 层面的操作行为，与数据库层面的 `CASCADE` 并不是同一概念。
> >
> >   - `onDelete: "RESTRICT"|"CASCADE"|"SET NULL"`
> >
> >     这里指定的是数据库层面的外键的 `ON DELETE` 行为。
> >
> >   - `nullable: boolean`
> >
> >     此关系的列是否可为空，默认为 `true`。
> >
> >   - `orphanedRowAction:  "nullify" | "delete" | "soft-delete" | "disable"`
> >
> >     当启用了级联操作时，如果父实体实例被保存导致数据库中的一些子实体变成了孤立的（即不再被父实体引用），则此选项会控制对它们的处理：
> >
> >     - `nullify` 将移除父实体和孤立子实体之间的关联关系键，但不会删除子实体本身。
> >     - `delete` 将从数据库中完全删除这些子实体。
> >     - `soft-delete` 将这些子实体标记为软删除。
> >     - `disable` 将不会对这些子实体进行任何处理。
>
> 一对一和一对多关系：
>
> > 一对一关系：
> >
> > > `@OneToOne()`  用于设置一对一关系。如果是单向关系，则在关系的一侧使用，如果是双向关系，则在关系的双侧都使用。一对一关系中必须且仅在关系的一侧使用 `@JoinColumn()`，它代表设置外键的一侧。
> >
> > 一对多关系：
> >
> > > `@ManyToOne()` 和 `@OneToMany()` 装饰器共同用于定义一对多关系。
> > >
> > > `@ManyToOne()` 可以独立存在。它定义在“多”的一侧，需使用 `@JoinColumn()` 定义一个外键指向“一”的一侧。在数据库设计中，外键关系总是存在于多的一侧。此时定义的是一个单向关系。
> > >
> > > `@OneToMany()` 用于搭配 `@ManyToOne()` 使用以形成双向关系，此时可以省略 `@JoinColumn()`。使用 `@OneToMany()` 时，关联属性接收的是由关联的实体实例组成的数组，因为它可以接收多个实体实例。
> > >
> > > 示例见：[many-to-one-one-to-many-relations | TypeORM 中文文档](https://www.typeorm.org/many-to-one-one-to-many-relations)
> >
> > `@JoinColumn()` 的使用：
> >
> > > `@JoinColumn()` 一般用于一对一关系和一对多关系。
> > >
> > > 示例：
> > >
> > > ~~~typescript
> > > @ManyToOne(type => Category)
> > > @JoinColumn()
> > > category: Category;
> > > ~~~
> > >
> > > 以上代码片段位于一个 `Entity` 类的定义中。其中的成员属性 `category: Category` 的属性名并不代表在对应的表中定义一个 `category` 列，而是表示这个属性用来定义当前实体与 `Category` 实体之间的关系。
> > >
> > > 这里在 `@JoinColumn()` 中没有传入任何配置，那么 `TypeORM` 会将创建的外键列自动命名为`当前成员属性名 + 关联实体的主键列名`，采用小驼峰命名。如果 `Category` 实体中定义的主键列名默认为 `id`，那么这里生成的外键列名就为 `categoryId`。
> > >
> > > 也可以手动指定生成的外键列名：
> > >
> > > ~~~typescript
> > > @ManyToOne(type => Category)
> > > @JoinColumn({ name: "cat_id" })
> > > category: Category;
> > > ~~~
> > >
> > > 也可以手动指定外键关联的实体的列名：
> > >
> > > ~~~typescript
> > > @ManyToOne(type => Category)
> > > @JoinColumn({ referencedColumnName: "name" }) // 自动生成的外键列名会是：categoryName
> > > category: Category;
> > > ~~~
> > >
> > > 还可以建立多个外键，此时不会自动引用相关实体的主键列，需要提供引用列的名称：
> > >
> > > ~~~typescript
> > > @ManyToOne(type => Category)
> > > @JoinColumn([
> > >     { name: "category_id", referencedColumnName: "id" },
> > >     { name: "locale_id", referencedColumnName: "locale_id" }
> > > ])
> > > category: Category;
> > > ~~~
>
> 多对多关系：
>
> > `@JoinTable()` 的使用：
> >
> > > `@JoinTable()` 用于在多对多关系中建立中间表。
> > >
> > > 示例：
> > >
> > > ~~~typescript
> > > @ManyToMany(type => Category)
> > > @JoinTable({
> > >     name: "question_categories", // 为此关系建立的中间表的表名
> > >     joinColumn: { // 定义当前实体在中间表中的外键列
> > >         name: "question", // 外键列的名称为 question
> > >         referencedColumnName: "id" // 此外键列引用当前实体的 id 字段
> > >     },
> > >     inverseJoinColumn: { // 定义关系的另一端在中间表中的外键列
> > >         name: "category", // 外键列的名称为 category
> > >         referencedColumnName: "id" // 此外键列引用关系实体中的 id 字段
> > >     }
> > > })
> > > categories: Category[];
> > > ~~~
> >
> > 多对多关系可以是单向，也可以是双向的。多对多的双向关系中，`@JoinTable()` 仅在关系的拥有方中使用。关系的拥有方指的是用于维护两个实体间关系的一侧，关系的拥有方中添加或删除实例时，会影响到中间表。
> >
> > `@ManyToMany()` 和 `@JoinTable()` 建立的多对多关系中，中间表只会存在关联两个实体的外键，不会存在其它属性。如果需要存在其它属性，则需要自己新建一个实体，在该实体中使用 `@ManyToOne()` 分别向要建立多对多关系的实体建立多对一关系，用该实体生成中间表，并在该实体中定义其它列。

### 树结构存储

可以使用 `@Tree()` 装饰器将一个 `Entity` 定义为树形实体类。在树形实体中，可以分别使用 `@TreeChildren()` 和 `@TreeParent()` 定义子节点和父节点。

> `@Tree()` 接收的第一个参数是指定使用的树结构存储方案，支持以下值：
>
> - `"nested-set"`：嵌套集合
> - `"materialized-path"`：材料化路径（路径枚举）
> - `"closure-table"`：闭包表
>
> 对于邻接列表方案，可以直接在普通 `Entity` 中实现。例：
>
> ~~~typescript
> import {
>     Entity,
>     Column,
>     PrimaryGeneratedColumn,
>     ManyToOne,
>     OneToMany,
> } from "typeorm"
> 
> @Entity()
> export class Category {
>     @PrimaryGeneratedColumn()
>     id: number
> 
>     @Column()
>     name: string
> 
>     @Column()
>     description: string
> 
>     @ManyToOne((type) => Category, (category) => category.children)
>     parent: Category
> 
>     @OneToMany((type) => Category, (category) => category.parent)
>     children: Category[]
> }
> ~~~
>
> 对于闭包表方案，可以将一个可选的 `options` 对象作为 `@Tree("closure-table", options)` 的第二个参数传入，用以指定闭包表的名称和闭包表的字段名。例：
>
> > ~~~typescript
> > @Tree("closure-table", {
> >     closureTableName: "category_closure",
> >     ancestorColumnName: (column) => "ancestor_" + column.propertyName,
> >     descendantColumnName: (column) => "descendant_" + column.propertyName,
> > })
> > ~~~
> >
> > 其中 `ancestorColumnName` 和 `descandantColumnName` 回调函数接收一个参数 `column`，它接收当前实体中主键列的元数据，可以通过 `column.propertyName` 获取列的名称。
>
> 以下是一个闭包表树形实体的示例：
>
> ~~~typescript
> import {
>     Entity,
>     Tree,
>     Column,
>     PrimaryGeneratedColumn,
>     TreeChildren,
>     TreeParent,
>     TreeLevelColumn,
> } from "typeorm"
> 
> @Entity()
> @Tree("materialized-path")
> export class Category {
>     @PrimaryGeneratedColumn()
>     id: number
> 
>     @Column()
>     name: string
> 
>     @TreeChildren()
>     children: Category[]
> 
>     @TreeParent()
>     parent: Category
> }
> ~~~

树形实体的使用：

> 向树形实体中保存数据时，可以在子节点中设置其父节点，然后保存。例：
>
> ~~~typescript
> const a1 = new Category()
> a1.name = "a1"
> await dataSource.manager.save(a1)
> 
> const a11 = new Category()
> a11.name = "a11"
> a11.parent = a1
> await dataSource.manager.save(a11)
> 
> const a12 = new Category()
> a12.name = "a12"
> a12.parent = a1
> await dataSource.manager.save(a12)
> 
> const a111 = new Category()
> a111.name = "a111"
> a111.parent = a11
> await dataSource.manager.save(a111)
> 
> const a112 = new Category()
> a112.name = "a112"
> a112.parent = a11
> await dataSource.manager.save(a112)
> ~~~
>
> 要加载树形结构，需使用 `TreeRepository`：
>
> ~~~typescript
> const trees = await dataSource.manager.getTreeRepository(Category).findTrees()
> ~~~
>
> `trees` 如下所示：
>
> ~~~typescript
> [
>     {
>         "id": 1,
>         "name": "a1",
>         "children": [
>             {
>                 "id": 2,
>                 "name": "a11",
>                 "children": [
>                     {
>                         "id": 4,
>                         "name": "a111"
>                     },
>                     {
>                         "id": 5,
>                         "name": "a112"
>                     }
>                 ]
>             },
>             {
>                 "id": 3,
>                 "name": "a12"
>             }
>         ]
>     }
> ]
> ~~~
>
> `TreeRepository` 中处理树形实体的方法：
>
> - `findTrees()`
>
>   以树状结构返回树形实体中的数据。
>
>   ~~~typescript
>   // 返回包含子类别的根类别
>   const treeCategories = await dataSource.manager.getTreeRepository(Category).findTrees()
>   
>   // 返回包含子类别的根类别，深度限制为 2
>   const treeCategoriesWithLimitedDepth = await dataSource.manager.getTreeRepository(Category).findTrees({ depth: 2 })
>   ~~~
>
> - `findRoots()`
>
>   返回树形实体的根节点集合。根节点是那些没有父节点的顶级节点。
>
> - `findDescendants()`、`findAncestors()`
>
>   以扁平数组的形式返回指定树形实体的所有后代、祖先（无关层级关系）。
>
>   ~~~typescript
>   const children = await dataSource.manager.getTreeRepository(Category).findDescendants(parentCategory)
>   
>   const parents = await repository.findAncestors(childCategory)
>   ~~~
>
> - `findDescendantsTree()`、`findAncestorsTree()`
>
>   以树状结构返回指定树形实体的所有后代、祖先。
>
>   ~~~typescript
>   // 返回父类别的所有直接子类别（包括其嵌套类别）
>   const childrenTree = await repository.findDescendantsTree(parentCategory)
>   // 返回父类别的所有直接子类别（包括其嵌套类别），深度限制为 2
>   const childrenTreeWithLimitedDepth = await repository.findDescendantsTree(
>       parentCategory,
>       { depth: 2 },
>   )
>   
>   // 返回直接子类别的所有父级类别（包括 "父级的父级"）
>   const parentsTree = await dataSource.manager.getTreeRepository(Category).findAncestorsTree(childCategory)
>   ~~~
>
> - `createDescendantsQueryBuilder()`、`createAncestorsQueryBuilder()`
>
>   创建用于获取指定树形实体后代、祖先的查询构建器。
>
>   ~~~typescript
>   const children = await repository
>       .createDescendantsQueryBuilder(
>           "category",
>           "categoryClosure",
>           parentCategory,
>       )
>       .andWhere("category.type = 'secondary'")
>       .getMany()
>   
>   const parents = await repository
>       .createAncestorsQueryBuilder("category", "categoryClosure", childCategory)
>       .andWhere("category.type = 'secondary'")
>       .getMany()
>   ~~~
>
> - `countDescendants()`、`countAncestors()`
>
>   获取树形实体的后代、祖先的数量。
>
>   ~~~typescript
>   const childrenCount = await dataSource.manager.getTreeRepository(Category).countDescendants(parentCategory)
>         
>   const parentsCount = await dataSource.manager.getTreeRepository(Category).countAncestors(childCategory)
>   ~~~
>
> 对于以下方法可以传递一个 `relations` 选项，用以指示需要与树节点一起加载的实体关系（相当于执行了简化的左连接）：
>
> > - findTrees
> > - findRoots
> > - findDescendants
> > - findDescendantsTree
> > - findAncestors
> > - findAncestorsTree
> >
> > 示例：
> >
> > ~~~typescript
> > // 自动连接 'sites' 关系，如果 Category 实体有一个与 Sites 实体相关联的字段，则可以一并返回这些信息
> > const treeCategoriesWithRelations = await dataSource.manager.getTreeRepository(Category).findTrees({
> >     relations: ["sites"],
> > })
> > 
> > // 返回直接子类别的所有父级类别并连接 'members' 关系
> > const parentsWithRelations = await dataSource.manager.getTreeRepository(Category).findAncestors(childCategory, {
> >     relations: ["members"],
> > })
> > ~~~

## `DataSource`

只有在设置了 `DataSource` 后才能与数据库进行交互。

将配置对象传入 `DataSource` 后将其实例化。调用 `DataSource` 实例的 `initialize` 方法以建立初始的连接或连接池。调用它的 `destroy` 方法以断开连接或断开连接池中的所有连接。可以在应用程序的任何地方使用 `DataSource` 实例。

### `DataSource` 实例相关常用 `API`

- `initialize()`

  初始化 `dataSource` 并打开到数据库的连接池。

  ~~~typescript
  await dataSource.initialize()
  ~~~

- `destroy()`

  销毁 `dataSource` 是所有数据库连接，通常在应用程序关闭时调用此方法。

  ~~~typescript
  await dataSource.destroy()
  ~~~

- `manager`

  用于处理 `Entity` 的 `EntityManager`。

  ~~~typescript
  const manager: EntityManager = dataSource.manager
  // 可以调用 EntityManager 的方法，例如 find:
  const users = await manager.find()
  ~~~

- `transaction()`

  执行数据库事务。

  ~~~typescript
  async function executeTransaction() {
    try {
      await dataSource.transaction(async (manager) => {
        // 这里接收的 manager 参数是一个特殊的 EntityManager 实例，在这里必须使用该实例执行所有数据库操作
        const user = await manager.save(User, { name: '新用户' });
        await manager.save(Log, { userId: user.id, action: '创建用户' });
        // 如果这里抛出错误，之前的操作会被自动回滚
      });
  
      console.log('事务执行成功，所有操作已提交。');
    } catch (error) {
      console.error('事务执行失败，所有操作已回滚。', error);
    }
  }
  ~~~

- `query()`

  执行原始 `SQL` 查询。

  ~~~typescript
  const rawData = await dataSource.query(`SELECT * FROM USERS`)
  ~~~

- `createQueryBuilder()`

  创建一个查询构建器。

  ~~~typescript
  const users = await dataSource
      .createQueryBuilder()
      .select()
      .from(User, "user")
      .where("user.name = :name", { name: "John" })
      .getMany()
  ~~~

- `createQueryRunner()`

  创建一个查询运行器，每个新的 `QueryRunner` 实例会从连接池中获取一个连接。

  `QueryRunner` 有一系列可以使用的方法，它还有自己的 `EntityManager` 实例， 通过 `manager` 属性，可以在 `QueryRunner` 实例上运行 `EntityManager` 的方法，以在特定的数据库连接上执行操作。

  ~~~typescript
  const queryRunner = dataSource.createQueryRunner()
  
  // connect() 将执行实际的数据库连接
  await queryRunner.connect()
  
  // connect() 之后可以使用 queryRunner 的其它方法
  const users = await queryRunner.manager.find(User)
  
  // 完成操作后需要释放 queryRunner
  await queryRunner.release()
  ~~~

- `synchronize()` 

  将 `dataSource` 中指定的 `Entity` 类同步给数据库的表结构。如果 `DataSource` 选项中设置了 `synchronize: true`，那么在 `dataSource` 初始化时会自行调用此方法。不建议在生产环境中使用。

  ~~~typescript
  await dataSource.synchronize()
  ~~~

- `dropDatabase()`

  删除当前 `dataSource` 连接的数据库中的所有数据表及数据。不建议在生产环境中使用。

  ~~~typescript
  await dataSource.dropDatabase()
  ~~~

- `runMigrations()`

  运行所有待处理的迁移。

  ~~~typescript
  await dataSource.runMigrations()
  ~~~

- `undoLastMigration()`

  撤销最后执行的迁移。

  ~~~typescript
  await dataSource.undoLastMigration()
  ~~~

- `options`

  用于创建此 `dataSource` 的配置对象。

  ~~~typescript
  const dataSourceOptions: DataSourceOptions = dataSource.options
  ~~~

- `isInitialized`

  表示此 `dataSource` 是否已初始化并建立与数据库的初始连接。

  ~~~typescript
  const isInitialized: boolean = dataSource.isInitialized
  ~~~

- `driver`

  此 `dataSource` 中使用的底层数据库驱动的详细信息。

  ~~~typescript
  const driver: Driver = dataSource.driver
  ~~~

## `EntityManager` 与 `Repository`

通过 `dataSource` 的 `manager` 属性可以访问 `EntityManager`（实体管理器）。`EneityManager` 可以用来管理任何 `Entity`。

`Repository`（仓库）类似于 `EntityManager`，但其操作仅限于特定的 `Entity`。将 `Entity` 类传入 `dataSource` 或 `EntityManager` 的 `getRepository` 方法以获取作用于该 `Entity` 的 `Repository`。

### 查询

所有 `EntityManager` 和 `Repository` 的 `find*` 方法都可以接收一些查询选项，可以在不适用 `QueryBuilder` 的情况下使用这些查询选项查询数据。

`EntityManager` 使用 `find*` 方法时，第一个参数传入要查询的 `Entity`，第二个参数传入查询选项。而 `Repository`使用 `find*` 方法时则直接传入查询选项。 

详见：[查找选项 | TypeORM 中文文档](https://www.typeorm.org/find-options)

一些简单查询示例：

- 直接使用 `find()` 查询：

  ~~~typescript
  const users = await AppDataSource.manager.find(User);
  ~~~

  相当于执行：

  ~~~sql
  SELECT * FROM `user` `User`;
  ~~~

  还可以使用查询选项选择要查询的字段：

  ~~~typescript
  const users = await AppDataSource.manager.find(User, {
    select: { firstName: true },
  });
  ~~~

  相当于执行：

  ~~~sql
  SELECT 
      `User`.`firstName` AS `User_firstName`,
      `User`.`id` AS `User_id`
  FROM
      `user` `User`;
  ~~~

- 使用 `findBy()` 查询可以指定查询条件：

  ~~~typescript
  const users = await AppDataSource.manager.findBy(User, { age: 25 });
  ~~~

  相当于执行：

  ~~~sql
  SELECT 
      *
  FROM
      `user` `User`
  WHERE
      `User`.`age` = 25;
  ~~~
  
- 使用 `findAndCount()` 查询会在查询后额外使用 `COUNT()` 聚合函数计算所有的行数：

  ~~~typescript
  const [users, count] = await AppDataSource.manager.findAndCount(User);
  ~~~

  相当于执行：

  ~~~sql
  -- 先进行普通查询
  SELECT 
      *
  FROM
      `user` `User`;
  
  -- 计算行数，数字 1 代表该行存在，因此此处 COUNT(1) 相当于 COUNT(*)
  SELECT 
      COUNT(1) AS `cnt`
  FROM
      `user` `User`;
  ~~~

  也可以使用 `findAndCountBy()` 指定查询条件。

- 使用 `findOne()`、`findOneBy()` 可以查询单行数据，相比于 `find()`、`fineBy()` 多加了一个 `LIMIT 1` 的条件。另外，使用 `fineOne` 等方法时必须附加查询条件。

  ~~~typescript
  const users = await AppDataSource.manager.findOne(User, {
    select: { firstName: true },
    where: { age: LessThan(30) },
    order: {
      id: "DESC",
    },
  });
  ~~~

  相当于执行：

  ~~~sql
  SELECT 
      `User`.`firstName` AS `User_firstName`,
      `User`.`id` AS `User_id`
  FROM
      `user` `User`
  WHERE
      ((`User`.`age` < 30))
  ORDER BY `User`.`id` DESC
  LIMIT 1;
  ~~~

  `findOne` 系列方法还有两个变体：`findOneOrFail()` 和 `findOneByOrFail()`，使用它们时如果没有查询数据，则会抛出一个 `EntityNotFoundError` 异常。 

### 增删改

- `save()`

  传入 `Entity` 实例或 `Entity` 实例数组，将其持久化保存到数据库中。

  `save` 方法可以用来添加新的行（`CREATE` 操作），也可以用来更新已有的行（`UPDATE` 操作）。`save` 方法执行后会先进行一次查询，如果发现 `Entity` 实例中包含了数据库中已存在的主键（或复合主键）值，那么就会执行 `UPDATE` 操作，否则会执行 `CREATE` 操作。

  对于 `EntityManager`，可以有两种方式使用 `save` 方法：

  > 以下两种方式等同，第二种相当于多执行了一次 `create` 方法：
  >
  > ~~~typescript
  > const user = new User();
  > user.firstName = "Timber";
  > user.lastName = "Saw";
  > user.age = 25;
  > await AppDataSource.manager.save(user)
  > ~~~
  >
  > ~~~typescript
  > await AppDataSource.manager.save(User, {
  >   firstName: "Timber",
  >   lastName: "Saw",
  >   age: 25
  > })
  > ~~~

  对于 `Repository` 则是：

  > 以下两种方式等同：
  >
  > ~~~typescript
  > const user = new User();
  > user.firstName = "Timber";
  > user.lastName = "Saw";
  > user.age = 25;
  > await AppDataSource.getRepository(User).save(user)
  > ~~~
  >
  > ~~~typescript
  > await AppDataSource.getRepository(User).save({
  > firstName: "Timber",
  > lastName: "Saw",
  > age: 25,
  > });
  > ~~~

  也可以用数组批量保存 `Entity`：

  > 以下两种方式等同：
  >
  > ~~~typescript
  > const user1 = new User();
  > user1.firstName = "Walter";
  > user1.lastName = "White";
  > user1.age = 50;
  > 
  > const user2 = new User();
  > user2.firstName = "Jessie";
  > user2.lastName = "Pinkman";
  > user2.age = 27;
  > 
  > await AppDataSource.manager.save([user1, user2])
  > ~~~
  >
  > ~~~typescript
  > await AppDataSource.manager.save(User, [
  > {
  >  firstName: "Walter",
  >  lastName: "White",
  >  age: 50,
  > },
  > {
  >  firstName: "Jessie",
  >  lastName: "Pinkman",
  >  age: 27,
  > },
  > ]);
  > ~~~

- `insert()`

  将 `Entity` 实例或 `Entity` 实例数组插入到数据库中。

- `update()`

  根据给定的更新选项部分更新 `Entity` 实例并更新到数据库中的对应行。

  ~~~typescript
  await entityManager.update(User, { age: 18 }, { firstName: 'NewFirstName', lastName: 'NewLastName' });
  // 执行 UPDATE user SET firstName = 'NewFirstName' WHERE age = 18
  ~~~

  需要注意的是，如果传入的要更新的字段为 `null` 值，则 `update` 方法会忽略对该字段的更新。如果需要将某字段更新为 `null` 值本身，应该使用 `save` 方法处理。

- `delete()` / `remove()`

  删除数据库中的行。

  `delete` 会根据 `Entity` 实例的 `ID`、 `ID` 数组或给定的条件来删除数据库中对应行：

  ~~~typescript
  await repository.delete(1);
  await repository.delete([1, 2, 3]);
  await repository.delete({ firstName: "Timber" });
  ~~~

  `remove` 会根据传入的 `Entity` 实例或 `Entity` 实例数组来删除数据库中的对应行：

  ~~~typescript
  await repository.remove(user);
  await repository.remove([user1, user2, user3]);
  ~~~

- `softDelete()`、`softRemove()`、`restore()`、`recover()`

  软删除和恢复。需要在表中使用 `@DeletaeDateColumn()` 定义删除时间的字段。

  `softDelete()` 和 `softRemove()` 的用法同 `delete()` 和 `remove()`。

  `restore()` 可以通过 `ID`（数组）恢复软删除的数据，`revocer()` 可以通过 `Entity` 实例（数组）恢复软删除的数据。

### 其它常用 `API`

- `query()`

  同 `dataSource.query()`

- `transaction()`

  同 `dataSource.transaction()`

- `createQueryBilder()`

  同 `dataSource.createQueryBilder()`

- `create()`

  将 `Entity` 类或 `Entity` 类组成的数组传入 `create()`调用，会为其创建新实例。

  在一些增删改查方法中，如果传入的不是 `Entity` 实例而是普通对象，则会隐式地调用 `create` 方法为其创建实例。

  `EntityManager.create()` 的用法：

  > ~~~typescript
  > const user = manager.create(User); // 相当于 const user = new User();
  > ~~~
  >
  > ~~~typescript
  > const user = manager.create(User, {
  >     id: 1,
  >     firstName: "Timber",
  >     lastName: "Saw",
  > }); // 相当于 const user = new User(); user.firstName = "Timber"; user.lastName = "Saw";
  > ~~~

  `Repository.create()` 的用法：

  > ~~~typescript
  > const user = repository.create() // 相当于 const user = new User();
  > ~~~
  >
  > ~~~typescript
  > const user = repository.create({
  >     id: 1,
  >     firstName: "Timber",
  >     lastName: "Saw",
  > }) // 相当于 const user = new User(); user.firstName = "Timber"; user.lastName = "Saw";
  > ~~~


## `QueryBuilder`

`QueryBuilder` （查询构建器）用于构建 `SQL` 查询，执行它们并将数据转换为 `Entity` 返回。

有三种方式可以创建 `QueryBuilder`：

>- 使用 `DataSource`：
>
>  ~~~typescript
>  const user = await dataSource
>      .createQueryBuilder()
>      .select("user")
>      .from(User, "user")
>      .where("user.id = :id", { id: 1 })
>      .getOne();
>  ~~~
>
>- 使用 `EntityManager`：
>
>  ~~~typescript
>  const user = await dataSource.manager
>      .createQueryBuilder(User, "user")
>      .where("user.id = :id", { id: 1 })
>      .getOne();
>  ~~~
>
>- 使用 `Repository`：
>
>  ~~~typescript
>  const user = await dataSource
>      .getRepository(User)
>      .createQueryBuilder("user")
>      .where("user.id = :id", { id: 1 })
>      .getOne();
>  ~~~

`QueryBuilder` 分为五种不同的类型：

> 默认创建的 `QueryBuilder` 是 `SelectQueryBuilder`。
>
> 在任何查询构建器中切换类型时，将会获得一个新的查询构建器实例。
>
> 五种 `QueryBuilder` 分别是：
>
> - `SelectQueryBuilder`
>
>   用于构建和执行 `SELECT` 查询。例：
>
>   ~~~typescript
>   const user = await dataSource
>       .createQueryBuilder()
>       .select("user")
>       .from(User, "user")
>       .where("user.id = :id", { id: 1 })
>       .getOne();
>   ~~~
>
> - `InsertQueryBuilder`
>
>    用于构建和执行 `INSERT` 查询。例：
>
>   ~~~typescript
>   await dataSource
>       .createQueryBuilder()
>       .insert()
>       .into(User)
>       .values([
>           { firstName: "Timber", lastName: "Saw" },
>           { firstName: "Phantom", lastName: "Lancer" },
>       ])
>       .execute();
>   ~~~
>
> - `UpdateQueryBuilder`
>
>   用于构建和执行 `UPDATE` 查询。例：
>
>   ~~~typescript
>   await dataSource
>       .createQueryBuilder()
>       .update(User)
>       .set({ firstName: "Timber", lastName: "Saw" })
>       .where("id = :id", { id: 1 })
>       .execute();
>   ~~~
>
> - `DeleteQueryBuilder`
>
>   用于构建和执行 `DELETE` 查询。例：
>
>   ~~~typescript
>   await dataSource
>       .createQueryBuilder()
>       .delete()
>       .from(User)
>       .where("id = :id", { id: 1 })
>       .execute();
>   ~~~
>
> - `RelationQueryBuilder`
>
>   用于构建和执行关联特定操作的查询构建器。例：
>
>   ~~~typescript
>   await dataSource
>       .createQueryBuilder()
>       .relation(User, "photos")
>       .of(id)
>       .loadMany();
>   ~~~

### 查询操作

#### 获取值

- 获取结果：

  > 如果要从数据库获取单个结果，则使用 `getOne()` 或 `getOneOrFail()`，后者会在获取不到结果时抛出 `EntityNotFoundError`。如果要从数据库获取多个结果，则使用 `getMany()`。例：
  >
  > ~~~typescript
  > const timber = await dataSource
  >     .getRepository(User)
  >     .createQueryBuilder("user")
  >     .where("user.id = :id OR user.name = :name", { id: 1, name: "Timber" })
  >     .getOne();
  > 
  > const users = await dataSource
  >     .getRepository(User)
  >     .createQueryBuilder("user")
  >     .getMany();
  > ~~~
  >
  > 以上获取到的结果会被转换为 `Entity` 实例。如果希望获取到的结果不是 `Entity` 实例，而是原始数据直接转换的对象，那么可以使用 `getRawOne()` 和 `getRawMany()`。

- 获取结果行数的计数：

  > 使用 `getCount()` 可以获取查询返回的行数计数。例：
  >
  > ~~~typescript
  > const count = await dataSource
  >     .getRepository(User)
  >     .createQueryBuilder("user")
  >     .where("user.name = :name", { name: "Timber" })
  >     .getCount();
  > ~~~
  >
  > 将生成以下 `SQL` 语句：
  >
  > ~~~sql
  > SELECT count(*) FROM users user WHERE user.name = 'Timber'
  > ~~~

#### 条件查询

- 指定查询字段与查询主体：

  > 使用 `select()` 和 `from()` 指定查询的字段与主体，这两个方法调用后会返回新的 `QueryBuilder` 实例。
  >
  > 其中 `from()` 接收两个必选参数：
  >
  > - 第一个参数传入 `Entity` 类，以选定要查询的表。
  > - 第二个参数用以为表指定一个别名（该参数必须要提供，`TypeORM` 在一些情况下会根据表别名来自动构建查询返回的字段的别名，并以此将其转换为 `Entity` 实例）。
  >
  > `select()` 可接收两个参数：
  >
  > - 第一个参数是要 `SELECT` 的字段名字符串或它们组成的数组。
  >   - 如果字段名以 `"tableAlias.fieldName"` 的形式（`tableAlias` 是 `form()` 中传入的表的别名）指定，则生成 `SQL` 语句时会自动为该字段名起别名为 `"tableAlias_fieldName"`，使得字段可以被放进返回的 `Entity` 实例中。
  >   - 如果第一个参数传入的就是 `form()` 中传入的表的别名，那么将会 `SELECT` 该表中的所有字段，并为自动为这些字段构建别名，使得结果可以转换为 `Entity` 实例
  > - 第二个可选参数是手动指定为字段起的别名。
  >
  > 对于 `DataSource` 和 `EntityManager` 创建的 `QueryBuilder`，必须用 `select()` 和 `from()` 方法才能指定查询的字段与主体。而对于 `Repository` 创建的 `QueryBuilder` 是为特定的 `Entity` 类服务的，可以直接在调用 `createQueryBuilder()` 时传入一个别名，因此可以省略 `form()` 和 `select()` 的调用：
  >
  > ~~~typescript
  > dataSource.getRepository(User)
  >     .createQueryBuilder("user");
  > // 以上代码等同于以下代码
  > dataSource.createQueryBuilder()
  >     .createQueryBuilder()
  >     .select("user")
  >     .from(User, "user");
  > 
  > 
  > // select() 仍可以另外指定
  > dataSource.getRepository(User)
  >     .createQueryBuilder("user")
  >     .select("id");
  > // 以上代码等同于以下代码
  > dataSource.createQueryBuilder()
  >     .createQueryBuilder()
  >     .select("user")
  >     .select("id") // 多次链式调用 select() 时，后调用的会覆盖之前的
  >     .from(User, "user");
  > ~~~

- 查询构建器中的参数：

  > 类似于 `mysql2` 中的预编译语句中的参数，为了放置 `SQL` 注入，在 `TypeORM` 的查询构建器中的也需要另外传入。
  >
  > 例如 `.where()` 中的参数需要这样指定：
  >
  > > ~~~typescript
  > > .where("user.name = :name", { name: "Timber" })
  > > ~~~
  > >
  > > 以上写法是以下写法的简化形式：
  > >
  > > ~~~typescript
  > > .where("user.name = :name")
  > > .setParameter("name", "Timber")
  > > ~~~
  >
  > 需要注意 不能对不同的值使用同样的参数名，它们会被视为同一个参数，而后面的赋值会覆盖前面的值：
  >
  > > ~~~typescript
  > > const result = await dataSource
  > >     .createQueryBuilder('user')
  > >     .leftJoinAndSelect('user.linkedSheep', 'linkedSheep')
  > >     .leftJoinAndSelect('user.linkedCow', 'linkedCow')
  > >     .where('user.linkedSheep = :id', { id: sheepId })
  > >     .andWhere('user.linkedCow = :id', { id: cowId });
  > > // 最终用到的两个 :id 参数都将变为 cowId 值
  > > ~~~
  > >
  > > 这种情况下需要为这两个参数取独立的参数名：
  > >
  > > ~~~typescript
  > > const result = await dataSource
  > >     .createQueryBuilder('user')
  > >     .leftJoinAndSelect('user.linkedSheep', 'linkedSheep')
  > >     .leftJoinAndSelect('user.linkedCow', 'linkedCow')
  > >     .where('user.linkedSheep = :sheepId', { sheepId })
  > >     .andWhere('user.linkedCow = :cowId', { cowId });
  > > ~~~
  >
  > 另外，参数可以使用展开语法接收一个数组：
  >
  > > ~~~typescript
  > > .where("user.name IN (:...names)", { names: [ "Timber", "Cristal", "Lina" ] })
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > WHERE user.name IN ('Timber', 'Cristal', 'Lina')
  > > ~~~

- 添加 `WHERE` 条件表达式：

  > `where()` 方法用于添加 `WHERE` 条件表达式：
  >
  > >~~~typescript
  > >.where("user.name = :name", { name: "Timber" })
  > >~~~
  > >
  > >生成的 `SQL` 语句中的对应部分为：
  > >
  > >~~~sql
  > >WHERE user.name = 'Timber'
  > >~~~
  >
  > `andWhere()` 用于在 `WHERE` 条件表达式中使用 `AND`：
  >
  > > ~~~typescript
  > > .where("user.firstName = :firstName", { firstName: "Timber" })
  > > .andWhere("user.lastName = :lastName", { lastName: "Saw" })
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > WHERE user.firstName = 'Timber' AND user.lastName = 'Saw'
  > > ~~~
  >
  > `orWhere()` 用于在 `WHERE` 条件表达式中使用 `OR`：
  >
  > > ~~~typescript
  > > .where("user.firstName = :firstName", { firstName: "Timber" })
  > > .orWhere("user.lastName = :lastName", { lastName: "Saw" })
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > WHERE user.firstName = 'Timber' OR user.lastName = 'Saw'
  > > ~~~
  >
  > 使用展开语法接收参数进行 `IN` 查询：
  >
  > > ~~~typescript
  > > .where("user.name IN (:...names)", { names: [ "Timber", "Cristal", "Lina" ] })
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > WHERE user.name IN ('Timber', 'Cristal', 'Lina')
  > > ~~~
  >
  > `Brackets` 用于生成 `SQL` 语句中的括号，可将复杂的 `WHERE` 条件表达式添加到现有的 `WHERE` 条件中：
  >
  > > ~~~typescript
  > > .where("user.registered = :registered", { registered: true })
  > > .andWhere(
  > >     new Brackets((qb) => {
  > >         qb.where("user.firstName = :firstName", {
  > >             firstName: "Timber",
  > >         }).orWhere("user.lastName = :lastName", { lastName: "Saw" })
  > >     }),
  > > )
  > > // 这里的 qb 是一个 QueryBuilder 实例
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > WHERE user.registered = true AND (user.firstName = 'Timber' OR user.lastName = 'Saw')
  > > ~~~
  >
  > `NoBrackets` 用于在现有的 `WHERE` 条件中添加否定的嵌套逻辑：
  >
  > > ~~~typescript
  > > .where("user.registered = :registered", { registered: true })
  > > .andWhere(
  > >     new NotBrackets((qb) => {
  > >         qb.where("user.firstName = :firstName", {
  > >             firstName: "Timber",
  > >         }).orWhere("user.lastName = :lastName", { lastName: "Saw" })
  > >     }),
  > > )
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > WHERE user.registered = true AND NOT((user.firstName = 'Timber' OR user.lastName = 'Saw')) 
  > > ~~~
  >
  > `HAVING` 表达式的添加方式同 `WHERE` 表达式，将 `where()` 等方法换为 `having()` 即可。

- 添加 `ORDER BY` 表达式：

  > `orderBy()` 方法用于添加 `ORDER BY` 表达式：
  >
  > > ~~~typescript
  > > .orderBy("user.id")
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > ORDER BY user.id
  > > ~~~
  >
  > 默认为升序，也可以指定排序方向：
  >
  > > ~~~typescript
  > > .orderBy("user.id", "DESC")
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > ORDER BY user.id DESC
  > > ~~~
  >
  > 如果多次链式调用 `orderBy()`，将覆盖之前的所有 `ORDER BY` 表达式，可以使用 `addOrderBy()` 方法添加多个排序标准：
  >
  > > ~~~typescript
  > > .orderBy("user.age")
  > > .addOrderBy("user.id")
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > ORDER BY `user`.`age` ASC, `user`.`id` ASC
  > > ~~~
  >
  > 也可以在 `orderBy()` 方法中通过排序字段映射添加多个排序标准：
  >
  > > ~~~typescript
  > > .orderBy({
  > >     "user.age": "ASC",
  > >     "user.id": "DESC",
  > > })
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > ORDER BY `user`.`age` ASC, `user`.`id` DESC
  > > ~~~
  
- 添加 `GROUP BY` 表达式：

  > 使用 `groupBy()` 方法添加 `GROUP BY` 表达式：
  >
  > > ~~~typescript
  > > .groupBy("user.age")
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > GROUP BY user.age
  > > ~~~
  >
  > 如果多次链式调用 `groupBy()`，将覆盖之前的所有 `GROUP BY` 表达式，可以使用 `addGroupBy()` 方法添加多个分组标准：
  >
  > > ~~~typescript
  > > .groupBy("user.age")
  > > .addGroupBy("user.id")
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > GROUP BY `user`.`age`, `user`.`id`
  > > ~~~

- 添加 `LIMIT`、`OFFSET` 表达式：

  > 使用 `limit()`、`offset()` 方法添加 `LIMIT`、`OFFSET` 表达式：
  >
  > > ~~~typescript
  > > .limit(10)
  > > .offset(10)
  > > ~~~
  > >
  > > 生成的 `SQL` 语句中的对应部分为：
  > >
  > > ~~~sql
  > > LIMIT 10 OFFSET 10
  > > ~~~
  >
  > 注意，如果要进行分页操作，对于复杂的带有连接或子查询的查询中使用 `limit()` 和 `offset()`，会使得在连接等操作应用之前就进行了 `LIMIT` 和 `OFFSET` 操作，使得最终获取到的结果不符合分页的预期。在这种情况下，可以使用 `take()` 和 `skip()` 方法，它们会将 `LIMIT` 和 `OFFSET` 表达式作用到最终的结果集中，以确保分页操作符合预期。

#### 关联查询

连接和选择：

> 假设有以下 `User` 实体和 `Photo` 实体之间建立了一对多关系关联：
>
> ~~~typescript
> import { Entity, PrimaryGeneratedColumn, Column, OneToMany } from "typeorm"
> import { Photo } from "./Photo"
> 
> @Entity()
> export class User {
>     @PrimaryGeneratedColumn()
>     id: number
> 
>     @Column()
>     name: string
> 
>     @OneToMany((type) => Photo, (photo) => photo.user)
>     photos: Photo[]
> }
> ~~~
>
> ~~~typescript
> import { Entity, PrimaryGeneratedColumn, Column, ManyToOne } from "typeorm"
> import { User } from "./User"
> 
> @Entity()
> export class Photo {
>     @PrimaryGeneratedColumn()
>     id: number
> 
>     @Column()
>     url: string
> 
>     @ManyToOne((type) => User, (user) => user.photos)
>     user: User
> }
> ~~~
>
> 现在要加载名称为 `Timber` 的用户以及他的所有照片：
>
> ```typescript
> const user = await createQueryBuilder("user")
>     .leftJoinAndSelect("user.photos", "photo")
>     .where("user.name = :name", { name: "Timber" })
>     .getOne()
> ```
>
> 将获得以下结果：
>
> ~~~typescript
> {
>     id: 1,
>     name: "Timber",
>     photos: [{
>         id: 1,
>         url: "me-with-chakram.jpg"
>     }, {
>         id: 2,
>         url: "me-with-trees.jpg"
>     }]
> }
> ~~~
>
> 在以上查询构建器的操作中，`leftJoinAndSelect()` 进行了左连接：
>
> - 第一个参数是要加载的关联关系。
>
>   这里的 `"user.photo"` 中：
>
>   - `user` 是在该查询构建器中其它地方为 `User` 实体类起的别名，表示指定 `User` 实体为主表。
>   - `photos` 是 `User` 实体类中定义的关联属性，表示指定该关联属性对应的表为关系表。
>
> - 第二个参数会为该关联关系的关联表起的别名，可以在查询构建器中的任何位置使用此别名。
>
> 如果要获取所有未删除的 `Timber` 的照片，可以用以下两种方式：
>
> - 可以将关联表的条件放到 `WHERE` 条件表达式中：
>
>   ~~~typescript
>   const user = await createQueryBuilder("user")
>       .leftJoinAndSelect("user.photos", "photo")
>       .where("user.name = :name", { name: "Timber" })
>       .andWhere("photo.isRemoved = :isRemoved", { isRemoved: false })
>       .getOne()
>   ~~~
>
>   对应的 `SQL` 语句为：
>
>   ~~~sql
>   SELECT user.*, photo.* FROM users user
>       LEFT JOIN photos photo ON photo.user = user.id
>       WHERE user.name = 'Timber' AND photo.isRemoved = FALSE
>   ~~~
>
> - 也可以将关联表的条件放到 `ON` 条件表达式中：
>
>   ~~~typescript
>   const user = await createQueryBuilder("user")
>       .leftJoinAndSelect(
>           "user.photos",
>           "photo",
>           "photo.isRemoved = :isRemoved",
>           { isRemoved: false },
>       )
>       .where("user.name = :name", { name: "Timber" })
>       .getOne()
>   ~~~
>
>   对应的 `SQL` 语句为：
>
>   ~~~sql
>   SELECT user.*, photo.* FROM users user
>       LEFT JOIN photos photo ON photo.user = user.id AND photo.isRemoved = FALSE
>       WHERE user.name = 'Timber'
>   ~~~
>
> 以上是使用 `leftJoinAndSelect()` 来实现左连接的示例，还可以使用 `innerJoinAndSelect()` 来实现内连接，用法相同，它们都会在连接后将关联表中的数据选中。
>
> 也可以使用 `leftJoin()` 和 `innerJoin()` 来实现左连接和内连接，但不会自动将关联表的数据选中。
>
> 以上方式连接的都是在实体类中建立了关系的表，也可以连接没有建立关系的实体。假设 `User` 和 `Photo` 之间没有建立关联，仍可以这样将它们关联查询：
>
> ~~~typescript
> const user = await createQueryBuilder("user")
>     .leftJoinAndSelect(Photo, "photo", "photo.userId = user.id")
>     .getMany()
> ~~~
>
> ~~~typescript
> const user = await createQueryBuilder("user")
>     .leftJoinAndSelect("photos", "photo", "photo.userId = user.id")
>     .getMany()
> ~~~
>
> 没有建立关系的实体在连接时需要手动指定连接条件。

连接和映射：

> 以下是一个示例，目标是关联查询出 `User` 实体和 `Photo` 实体连接的数据，并将 `Photo` 实体中符合条件的数据映射到查询出的 `User` 实体实例的 `profilePhoto` 属性中。
>
> ~~~typescript
> export class User {
>     // ...
>     profilePhoto: Photo;
> }
> ~~~
>
> ~~~typescript
> // 使用TypeORM的查询构建器创建一个查询，目标是"user"实体。
> const user = await createQueryBuilder("user")
>     // 使用leftJoinAndMapOne方法左连接"user"实体和"user.photos"关系，
>     // 并将结果映射到"user.profilePhoto"属性上。
>     // "user.photos"是"user"实体中定义的一对多或多对多关系。
>     // "photo"是这个关系中，用于此次查询的别名。
>     // "photo.isForProfile = TRUE"是连接条件，表示只关联标记为头像的照片。
>     .leftJoinAndMapOne(
>         "user.profilePhoto", // 映射目标：用户实体的"profilePhoto"属性。
>         "user.photos", // 关系的源：用户的照片集合。
>         "photo", // 在此查询中用于"user.photos"关系的别名。
>         "photo.isForProfile = TRUE", // 连接条件：只选择作为头像的照片。
>     )
>     // 添加一个where条件，限制查询结果为用户名为"Timber"的用户。
>     .where("user.name = :name", { name: "Timber" })
>     // 执行查询并返回单个结果。
>     .getOne();
> ~~~
>
> 这里会将匹配到的第一个数据映射到查询到的 `User` 实体示例的 `profilePhoto` 属性中，如果需要映射匹配到的所有符合条件的数据，则使用 `leftJoinAndMapMany()`。

#### 子查询

[使用查询构建器进行查询 | TypeORM 中文文档](https://www.typeorm.org/select-query-builder#使用子查询)

### 插入操作

示例：

~~~typescript
await datasource
    .createQueryBuilder()
    .insert() // 指定操作类型为插入
    .into(User) // 指定目标表为User
    .values({
        firstName: "Timber", // 设置要插入的数据的firstName字段
        lastName: "Saw", // 设置要插入的数据的lastName字段
        externalId: "abc123", // 设置要插入的数据的externalId字段
    })
    .orUpdate(
        ["firstName", "lastName"], // 指定在发生冲突时需要更新的字段
        ["externalId"], // 指定哪个字段的冲突会触发更新操作
        {
            skipUpdateIfNoValuesChanged: true, // 如果指定字段的值没有变化，则跳过更新
        }
    )
    .execute(); // 执行构建好的查询
~~~

该示例中会尝试将一条包含 `firstName`、`lastName`、`externalId` 字段的数据插入到 `User` 表中。假设 `externalId` 字段是一个唯一索引，如果插入的 `externalId` 字段的数据在表中已存在，则会变成更新操作，更新这条记录的  `firstName` 和 `lastName` 字段。如果这两个字段的值没有变化，则跳过更新。

如果需要执行原始 `SQL` 查询，则需要使用函数形式的值：

~~~typescript
await dataSource
    .createQueryBuilder()
    .insert()
    .into(User)
    .values({
        firstName: "Timber",
        lastName: () => "CONCAT('S', 'A', 'W')",
    })
    .execute()
~~~

### 更新操作

示例：

~~~typescript
await dataSource
    .createQueryBuilder()
    .update(User) // 指定进行更新操作的目标实体是User
    .set({
        firstName: "Timber", // 更新用户的名字为"Timber"
        lastName: "Saw", // 更新用户的姓氏为"Saw"
        age: () => "age + 1", // 使用函数式更新，将用户的年龄增加1
    })
    .where("id = :id", { id: 1 }) // 指定更新条件，这里是ID等于1的用户
    .execute(); // 执行构建的更新操作
~~~

### 删除操作

#### `Delete`

示例：

~~~typescript
await myDataSource
    .createQueryBuilder('users')
    .delete() // 指定操作类型为删除。
    .from(User) // 指定目标表为User。
    .where("id = :id", { id: 1 }) // 添加条件，只删除ID为1的记录。
    .execute(); // 执行构建的删除操作。
~~~

#### `Soft-Delete`

示例：

~~~typescript
await myDataSource
  .createQueryBuilder('users')
  .softDelete() // 指定操作类型为软删除。
  .where("id = :id", { id: 1 }) // 指定软删除条件，即ID为1的用户。
  .execute(); // 执行构建的软删除操作。
~~~

软删除的数据可以进行恢复操作：

~~~typescript
await myDataSource
  .createQueryBuilder('users')
  .restore()
  .where("id = :id", { id: 1 })
  .execute();
~~~

### 处理关联关系

假设有一个实体 `Post`，它是关联属性 `categories` 定义了和和实体 `Category` 之间的多对多关系。如果要将一个新的 `category` 实体实例添加到某个 `post` 实例的 `categories` 列表中，可以这样做：

~~~typescript
const postRepository = dataSource.manager.getRepository(Post)
const post = await postRepository.findOne({
    where: {
        id: 1,
    },
    relations: {
        categories: true,
    },
})
post.categories.push(category)
await postRepository.save(post)
~~~

而如果使用 `RelationQueryBuilder`，可以等价实现以上操作：

~~~typescript
await dataSource
    .createQueryBuilder()
    .relation(Post, "categories") // 指定操作的是 Post 实体与 categories 关系的关联。
    .of(post) // 指定操作的源实体，即哪个Post实体被关联。
    .add(category) // 添加一个新的关联到 categories。
~~~

也可以用来删除实体实例：

~~~typescript
await dataSource
    .createQueryBuilder()
    .relation(Post, "categories")
    .of(post) // 也可以只使用帖子的 ID
    .remove(category) // 也可以只使用类别的 ID
~~~

添加和删除相关实体适用于多对多和一对多关系。对于一对一和多对一关系，可以使用 `set()`：

~~~typescript
await dataSource
    .createQueryBuilder()
    .relation(Post, "categories")
    .of(post) // 也可以只使用帖子的 ID
    .set(category) // 也可以只使用类别的 ID
// 如果要取消关系，则可以将 `null` 传递给 `set()` 方法。
~~~

`RelationQueryBuilder` 还可以用来加载关系实体：

~~~typescript
// 使用数据源的管理器查找ID为1的Post实体。
const post = await dataSource.manager.findOneBy(Post, {
    id: 1,
});

// 加载与这个Post实体相关联的所有categories。
post.categories = await dataSource
    .createQueryBuilder()
    .relation(Post, "categories") // 指定操作的是Post实体与categories关系的关联。
    .of(post) // 指出要加载关联的源实体，这里是之前查询到的post实例。
    .loadMany(); // 加载多个实体，因为一个帖子可以有多个分类。

// 加载与这个Post实体相关联的author。
post.author = await dataSource
    .createQueryBuilder()
    .relation(Post, "user") // 指定操作的是Post实体与user关系的关联。
    .of(post) // 指出要加载关联的源实体，这里是同一个post实例。
    .loadOne(); // 加载单个实体，因为一个帖子只有一个作者。
~~~

## `Migrations`

在生成环境中，使用 `synchronize: true` 配置项进行同步是不安全的，需要使用 `Migrations` 同步数据库架构。

### 生成 `Migrations`

`Migrations` 的原理是一个包含 `SQL` 查询的单个文件，可以使用以下命令生成：

> ~~~bash
> $ typeorm migration:create ./src/migration/PostRefactoring
> ~~~
>
> 该命令中的 `PostRefactoring` 是为 `migration` 指定的一个名称。该命令执行后会在 `./src/migration` 目录下生成一个名为 `{TIMESTAMP}-PostRefactoring.ts` 的文件，其中 `{TIMESTAMP}` 是生成迁移时的当前时间戳。
>
> 文件内容如下：
>
> ~~~typescript
> import { MigrationInterface, QueryRunner } from "typeorm"
> 
> export class PostRefactoringTIMESTAMP implements MigrationInterface {
>     async up(queryRunner: QueryRunner): Promise<void> {}
> 
>     async down(queryRunner: QueryRunner): Promise<void> {}
> }
> ~~~
>
> 其中 `up` 方法包含执行迁移所需代码；`down` 方法包含撤销迁移所作更改的代码，用于还原最后一个迁移。

`TypeORM` 可以根据 `DataSource` 中 `Entity` 的更改自动生成迁移文件：

> ~~~bash
> $ typeorm migration:generate  ./src/migration/PostRefactoring -d ./src/datasource.ts
> ~~~
>
> 这将会根据指定的 `DataSource` 中 `Entity` 的更改自动在 `./src/migration` 目录下生成 `{TIMESTAMP}-PostRefactoring.ts` 文件，且其中包含了要执行的 `SQL` 语句，如：
>
> ~~~typescript
> const { MigrationInterface, QueryRunner } = require("typeorm")
> 
> module.exports = class PostRefactoringTIMESTAMP {
>     async up(queryRunner) {
>         await queryRunner.query(
>             `ALTER TABLE "post" ALTER COLUMN "title" RENAME TO "name"`,
>         )
>     }
> 
>     async down(queryRunner) {
>         await queryRunner.query(
>             `ALTER TABLE "post" ALTER COLUMN "name" RENAME TO "title"`,
>         )
>     }
> }
> ~~~

### 运行和撤销 `Migrations`

需要在 `DataSource` 中进行配置：

> ~~~typescript
> {
>     type: "mysql",
>     host: "localhost",
>     port: 3306,
>     username: "test",
>     password: "test",
>     database: "test",
>     entities: [/*...*/],
>     migrations: [/*...*/],
>     migrationsTableName: "custom_migration_table",
> }
> ~~~
>
> 其中 `migrations` 选项代表要使用的 `Migration` 列表。
>
> 进行迁移后，会在数据库中创建一个迁移表，用以记录迁移信息。默认会使用 `Migration` 文件名，也可以使用 `migrationsTableName` 选项指定迁移表的名称。

`typeorm migration:create` 和 `typeorm migration:generate` 会创建 `.ts` 文件，除非使用 `--outputJS` 标志（可简写为 `--o`）表示输出 `.js` 文件。

 命令用于执行与撤销 `Migrations`。它们仅适用于 `.js` 文件，如果指定的 `Migrations` 文件是 `.ts` 文件，可以先将其编译为 `.js` 文件，或者在运行迁移文件时使用 `ts-node`：

> `CJS` 项目中使用 `ts-node` 的示例：
>
> ```bash
> $ npx typeorm-ts-node-commonjs migration:run -d path-to-datasource-config
> ```
>
> `ESM` 项目中使用 `ts-node` 的示例：
>
> ```bash
> $ npx typeorm-ts-node-esm migration:run -d path-to-datasource-config
> ```

`migration:run` 和 `migration:revert` 命令都可以使用 `--fake` 标志（可简写为 `-f`） 来模拟运行迁移或撤销迁移。迁移信息将添加到迁移表中，但不会真正执行，适用于在数据库中进行了手动更改，或使用其它工具运行了迁移后，希望保持一致的迁移历史记录时使用。
