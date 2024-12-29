## 云数据库

基于 `MongoDB`

在云函数中通过 `cloud.database()` 新建 `DB` 实例去操作数据库

### 基本概念

#### 文档

数据库的每条记录都是一个 `JSON` 格式的文档

#### 集合

集合是一组文档的集合，每个文档都在一个集合里面

如所有的用户都放在 `users` 集合里

#### 数据库

- 每个 `Laf` 应用有且仅有一个数据库，一个数据库可以创建多个集合

### 数据类型

**常用数据类型**

- `String` 字符串类型，存储任意长度的 UTF-8 编码的字符串
- `Number` 数字类型，包括整数和浮点数
- `Boolean` 布尔类型，包括 true 和 false
- `Array` 数组类型，可以包含任意数量的值，包括其他数据类型和嵌套数组
- `Object` 对象类型，可以包含任意数量的键值对，其中值可以是任何数据类型，包括其他对象和嵌套数组
- `Date` 日期类型，存储日期和时间
- `ObjectId` 对象 ID 类型，用于存储文档的唯一标识符

**其他数据类型**

- `Null` 相当于一个占位符，表示一个字段存在但是值为空
- `GeoPoint` 地理位置点
- `GeoLineStringLine` 地理路径
- `GeoPolygon` 地理多边形
- `GeoMultiPoint` 多个地理位置点
- `GeoMultiLineString` 多个地理路径
- `GeoMultiPolygon` 多个地理多边形

#### Date 类型

Date 类型用于表示时间，精确到毫秒，可以用 JavaScript 内置 Date 对象创建。

注意：当前服务端时间可能会有时区问题，可能不是东八区时间，在存储数据库的时候，可以先把时区转换成东八区或者使用时间戳

在云函数中，可以直接使用 `new Date()` 获取当前服务端时间

```typescript
//服务端当前时间
console.log(new Date())
// 输出结果：2023-04-21T14:47:32.697Z

const date = new Date();
const timestamp = date.getTime();
console.log(timestamp); 
// 输出一个以毫秒为单位的时间戳，例如：1650560477427
```

获取东八区时间

```typescript
// 获取当前时间的东八区时间
const date = new Date();
const offset = 8; // 东八区偏移量为 +8

// 计算当前时间的 UTC 时间，再加上偏移量得到东八区时间
const utcTime = date.getTime() + (date.getTimezoneOffset() * 60 * 1000);
const beijingTime = new Date(utcTime + (offset * 60 * 60 * 1000));
console.log(beijingTime); // 输出东八区时间的 Date 对象
```

下面演示云函数在 `test` 集合中添加一条记录，并添加创建时间 `createTime` 和创建时间戳 `createTimestamp` 2 个字段

```typescript
import cloud from '@lafjs/cloud'
const db = cloud.database()

export async function main(ctx: FunctionContext) {

  // 获取当前时间的东八区时间
  const date = new Date();
  const offset = 8; // 东八区偏移量为 +8

  // 计算当前时间的 UTC 时间，再加上偏移量得到东八区时间
  const utcTime = date.getTime() + (date.getTimezoneOffset() * 60 * 1000);
  const beijingTime = new Date(utcTime + (offset * 60 * 60 * 1000));
  console.log(beijingTime); // 输出东八区时间的 Date 对象

  await db.collection('test').add({
    name: 'xiaoming',
    createTime: beijingTime,
    createTimestamp: date.getTime()
  })
}
```

### 使用云函数操作数据库

#### 插入文档

- 插入单条文档

  ~~~typescript
  const res = await db.collection('user').add({
      name: 'Jack'
  })
  ~~~

  ~~~json
  // 集合 test 中会新增数据，_id 为自动生成
  {
    "_id": "6442b2cac4f3afd9a186ecd9",
    "name": "Jack"
  }
  ~~~

- 批量插入文档

  ~~~typescript
  const list = [
    { name: "jack" },
    { name: "rose" }
  ]
  // 向 user 集合中加入多条记录
  const res = await db.collection('user').add(list, { multi: true })
  console.log(res)
  ~~~

#### 查询数据

- 获取数据
  - `get()`
    - 获取所有数据
  - `getOne()`
    - 获取一条数据

- 过滤器

  - 过滤器不会返回查询的内容，只作过滤

  - `where()`

    - 设置查询条件条件 where 可接收对象作为参数，表示筛选出拥有和传入对象相同的 key-value 的文档。支持多个条件同时筛选

    - 比如筛选出所有名字叫 jack 的用户

      ~~~typescript
      // 查询 user 集合中 name 字段等于 jack 的记录
      await db.collection("user").where({
       name:"jack"
      });
      ~~~

    - 嵌套查询

      ~~~typescript
      import cloud from '@lafjs/cloud'
      const db = cloud.database()
      
      export async function main(ctx: FunctionContext) {
        // 查询 userInfo.name = 'Jack' 且 userInfo.age = 10 的数据
        // 写法 1
        const result1 = await db.collection('user')
        .where({
          userInfo: {
            name: "Jack",
            age: 10
          }
        }).get()
        // 写法 2
        const result2 = await db.collection('user')
        .where({
          'userInfo.name': 'Jack',
          'userInfo.age': 10
        }).get()
      }
      ~~~

    - 数组嵌套查询

      - 如数据库中 `test` 集合中有如下数据

        ~~~typescript
        [
          {
            "arr":[{
              "name": "item-1"
            },{
              "name": "item-2"
            }]
          },
          {
            "arr":[{
              "name": "item-3"
            },{
              "name": "item-4"
            }]
          }
        ]
        ~~~

        ~~~typescript
        // 查询 arr[0].name = 'item-1'  的数据
        const result1 = await db.collection('test')
        .where({
          'arr.0.name': "item-1"
        }).get()
        // 查询 arr 内某个元素的 name 为 'item-2' 的文档
        const result2 = await db.collection('test')
        .where({
          'arr.name': "item-2"
        }).get()
        ~~~

    - 高级查询指令见 [查询数据 | laf 云开发](https://doc.laf.run/guide/db/find.html#高级查询指令)

  - `doc()`

    - 跟 `where` 的区别是，`doc` 只根据 `_id` 筛选

    - 如果是用 `cloud.database()` 新增的文档，`_id` 类型为字符串 如果是用 `cloud.mongo.db` 新增的文档，`_id` 类型一般为 `ObjectId`

    - 示例

      ~~~typescript
      // 查询 user 集合中 _id 为 '644148fd1eeb2b524dba499e' 的文档
      await db.collection("user").doc('644148fd1eeb2b524dba499e');
      
      // 其实等同于 where 的筛选条件只有 _id
      await db.collection("user").where({
        _id: '644148fd1eeb2b524dba499e'
      });
      ~~~

      ~~~typescript
      // _id 的类型为 ObjectId 的情况
      import { ObjectId } from 'mongodb' // 需要在云函数顶部引入 ObjectId 类型
      
      await db.collection("user").doc(ObjectId('644148fd1eeb2b524dba499e'));
      ~~~

- `count()`

  - 获取符合条件的数量

    ~~~typescript
    await db.collection("goods").where({
      category: "computer",
      type: {
        memory: 8,
      },
    }).count()
    ~~~

  - 响应参数

    | 字段      | 类型    | 必填 | 说明                     |
    | :-------- | :------ | :--- | :----------------------- |
    | code      | string  | 否   | 状态码，操作成功则不返回 |
    | message   | string  | 否   | 错误描述                 |
    | total     | Integer | 否   | 计数结果                 |
    | requestId | string  | 否   | 请求序列号，用于错误排查 |

- 分页查询

  - `limit()`

    - 限制展示数量，最大为 `1000`， 如不使用，则 `get` 默认最多查询 `100` 条数据

      ~~~typescript
      await db.collection("user").limit(1000).get()
      ~~~

  - `skip()`

    - 跳过展示的数据，可以理解为设置起始位置

      ~~~typescript
      await db.collection("user").skip(4).get()
      ~~~

  - 分页查询示例

    ~~~typescript
    import cloud from '@lafjs/cloud'
    const db = cloud.database()
    
    export async function main(ctx: FunctionContext) {
      // 每页显示数量
      const pageSize = 3;
      // 第几页
      const page = 2;
      const res = await db.collection('user')
      .skip((page - 1) * pageSize)
      .limit(pageSize)
      .get()
    }
    ~~~

- `orderBy()`

  - 对结果排序

  - 参数

    | 参数      | 类型   | 必填 | 说明                                  |
    | :-------- | :----- | :--- | :------------------------------------ |
    | field     | string | 是   | 排序的字段                            |
    | orderType | string | 是   | 排序的顺序，升序 (asc) 或 降序 (desc) |

  - 示例

    ~~~typescript
    // 按照创建时间 createAt 的升序排序
    await db.collection("user").orderBy("createAt", "asc").get()
    ~~~

- `filed()`

  - 指定要返回的字段或指定不要返回的字段

  - 参数

    | 参数 | 类型   | 必填 | 说明                               |
    | :--- | :----- | :--- | :--------------------------------- |
    | -    | object | 是   | 要过滤的字段，不返回传 0，返回传 1 |

  - 注意不能同时指定返回的字段和不要返回的字段

  - 示例

    ~~~typescript
    await db.collection("user").field({ age: 1 }).get()
    ~~~

- 聚合操作

  https://doc.laf.run/guide/db/aggregate.html

#### 更新数据

- `Laf` 云数据库更新数据，实际上就是针对集合中的文档进行修改，通过 `where` 等操作符设置查询条件，然后通过 `update` 或 `set` 等更新操作符进行修改。

- 更新文档

  - `update()`

  - 示例

    - 更新单个文档

      ~~~typescript
      await db.collection('user')
      .where({ name: 'jack' })
      .update({ name: "Tom"，age:'18' })
      // 只会更新匹配到的第一个文档
      ~~~

    - 批量更新文档

      ~~~typescript
      await db.collection('user')
      .update({ name: "Tom" },{ multi:true })
      // 传入 {multi:true} 时，会更新匹配到的所有文档
      ~~~

- 更新指令

  [更新数据 | laf 云开发](https://doc.laf.run/guide/db/update.html#更新指令)

#### 删除数据

- 通过指定条件删除单个文档

  ~~~typescript
  // 删除 user 集合中，name 为 jack 的一条记录
    const res = await db.collection('user').where({ name: "jack" }).remove()
    console.log(res)
  ~~~

- 通过指定条件批量删除文档

  ~~~typescript
  // 删除 user 表中所有 name 为 jack 的记录
  await db.collection('user')
  .where({ name: "jack" })
  .remove({ multi: true })

- 清空集合

  ~~~typescript
  // 清空 user 集合
  await db.collection('user').remove({ multi: true })
  ~~~

## 云函数

### 云函数用法

#### 参数

- 在 `main` 函数中，可以通过参数 `ctx` 来获取用户传递的请求信息

- `ctx` 具有下面的一些参数：

  | 属性            | 介绍                                                         |
  | :-------------- | :----------------------------------------------------------- |
  | `ctx.requestId` | 当前请求的唯一 ID                                            |
  | `ctx.method`    | 当前请求的方法，如`GET`、`POST`                              |
  | `ctx.headers`   | 所有请求的 headers                                           |
  | `ctx.user`      | 使用 Http Bearer Token 认证时，解析出的 token 值             |
  | `ctx.query`     | 当前请求的 query 参数                                        |
  | `ctx.body`      | 当前请求的 body 参数                                         |
  | `ctx.request`   | HTTP 响应，和`express`的`Request`实例保持一致                |
  | `ctx.response`  | HTTP 响应，和`express`的`Response`实例保持一致               |
  | `ctx.socket`    | [WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket) 实例，[Laf WebSocket 使用文档](https://doc.laf.run/guide/function/websocket.html) |
  | `ctx.files`     | 上传的文件 ([File](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 对象数组) |
  | `ctx.env`       | 本应用自定义的环境变量 ([env](https://doc.laf.run/guide/function/env.html)) |

### 云函数返回值

- 方式一：`return`

  - 支持的类型

    ~~~javascript
    return Buffer.from("whoop"); // Buffer
    return {
      some: "json";
    } // 对象，会被处理成 JSON
    return ("<p>some html</p>"); // HTML
    return ("Sorry, we cannot find that!"); // 字符串
    ~~~

- 方式二：`ctx.response` 设置响应头、状态码和响应体等信息

  - 常见的 `res` 对象方法

    ~~~javascript
    ctx.response.send(body) // 发送响应体，可以是一个字符串、一个 Buffer 对象、一个 JSON 对象、一个数组等
    ctx.response.json(body) // 发送一个 JSON 响应
    ctx.response.status(statusCode) // 设置 HTTP 响应的状态码
    ctx.response.setHeader(name, value) // 设置一个响应头
    ...
    ~~~

  - 如果需要分段发送数据，则需要使用 `ctx.response.write` 和 `ctx.response.end`

    ~~~javascript
    export function main (ctx: FunctionContext) {
      // 设置响应头
      ctx.response.type = 'text/html';
      ctx.response.status = 200;
      // 写入数据块
      ctx.response.write('<html><body>');
      ctx.response.write('<h1>Hello, world!</h1>');
      ctx.response.write('</body></html>');
      // 结束响应
      ctx.response.end();
    };
    ~~~

### 云函数引入

- 可以在云函数中引入另一个已发布的云函数

- 引入写法：

  ~~~javascript
  // funcName 为 default 函数
  import funcName from '@/funcName'
  // 引入名为 func 的函数
  import { func } from '@/funcName'
  ~~~

- 在`test`云函数中引入`util`云函数

  ~~~javascript
  // util 云函数
  export default async function main () {
    return "util 已引入"
  };
  
  export function add(a: number, b: number) {
    return a + b
  };
  ~~~

  ~~~javascript
  // test 云函数
  import util, { add } from '@/util'
  
  export async function main(ctx: FunctionContext) {
    // 由于 util 的 default 方法是 async 的，所以需要加 await
    console.log(await util())
    // 打印结果："util 已引入"
    console.log(add(1, 2))
    // 打印结果：3
  }
  ~~~

### 云函数SDK

- 发送网络请求

  - 使用 `cloud.fetch()` 可发起 HTTP 请求，调用三方接口，可完成如支付接口、短信验证码等等三方接口操作。

  - 该接口是对 `axios` 请求库的封装，其调用方法与 `axios` 完全一致

- 操作数据库

  - 通过`cloud.database()` 可以获取数据库对象，进而对数据库进行操作。