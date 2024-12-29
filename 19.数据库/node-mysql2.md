## 简介

- 使用 `Node` 中的代码执行连接 `MySQL` 并执行 `SQL` 语句，可以借助一些库
  - `mysql`：最早的 `Node` 连接 `MySQL` 的数据库驱动
  - `mysql2`：在 `mysql` 的基础上，进行了很多优化改进
- `mysql2` 兼容 `mysql` 的 `API`，并提供了一些附加功能，如：
  - `Prepared Statement`（预编译语句）
  - `Promise` 支持

## 使用 mysql2

- 安装 `mysql2`

  ~~~bash
  npm i mysql2
  ~~~

- 基本使用

  ~~~javascript
  const mysql = require('mysql2')
  
  // 创建连接
  const connection = mysql.createConnection({
    host: 'localhost',
    port: 3306,
    database: 'foo_db',
    user: 'root',
    password: 'xxxxxx'
  })
  
  // 执行 SQL 语句
  const statement = 'SELECT * FROM t_products;'
  connection.query(statement, (err, values, fields) => {
    if (err) {
      console.log('查询失败：', err)
      return
    }
    
    // 查看结果
    console.log(values) // 将查询到的数据转换成了数组形式
    consolr.log(fields) // 查询到的数据的字段信息
  })
  
  // 销毁连接
  connection.destroy()
  ~~~

- `Prepared Statement`（预编译语句）

  - 作用

    - 提高性能：将创建的语句模板发送给 `MySQL`，然后 `MySQL` 编译语句模块，并且存储它，但是不执行，当提供实际参数时才会执行，不用每次执行都编译

    - 防止 `SQL` 注入

      - `SQL` 注入示例（仅大致示意）

        - 例如一个登录请求

          ~~~sql
          SELECT * from users
          WHERE name = admin AND `password` = `xxx`;
          ~~~

        - 如果入侵者在 `SQL` 语句注入其它内容变为如下

          ~~~sql
          SELECT * from users
          WHERE name = admin AND (`password` = `xxx` OR 1 = 1);
          ~~~

          - 则可以绕过密码验证获取 `admin` 权限

      - 如果使用预编译语句，`MySQL` 会将占位符中传入的参数视为条件，而不会作为 `SQL` 语法的关键字解析

  - 示例

    ~~~javascript
    // 定义预编译语句，以 ? 作为占位符 
    const statement = 'SELECT * FROM t_products WHERE price > ? and brand = ?;'
    // 执行预编译语句，并传入参数
    connection.execute(statement, [1000, '华为'], (err, values) => {
      console.log(values)
    })
    ~~~

- `Connection Pools`（连接池）

  - 创建了一个连接之后，如果有多个请求同时发生的话，该连接可能正在被占用

  - 如果每次请求都创建一个新连接，性能消耗会过大

  - 连接池可以在需要的时候自动创建连接，连接不自动销毁，不使用时会放入连接池中，后续可以继续使用

  - 可以在创建连接池的时候设置 `LIMIT`，也就是最大创建个数

  - 示例

    ~~~javascript
    const mysql = require('mysql2')
    
    // 创建连接持
    const pool = mysql.createPool({
      host: 'localhost',
      port: 3306,
      database: 'foo_db',
      user: 'root',
      password: 'xxxxxx',
      connectionLimit: 5, // 指定最多有多少个连接
      maxIdle: 5, // 指定最多有多少个空闲的连接，超过这个数量的空闲连接会自动被释放
      waitForConnections: true, // 如果没有可用连接了是否等待，如为 false 则会报错
      idleTimeout: 60000, // 空闲的连接多久会断开
      queueLimit: 0 // 可以排队的请求数量，超过这个数量会报错，如为 0 则表示没有上限
    })
    
    // 之后同 connection 一样使用 pool
    ~~~

- `Promise` 方式获取结果

  - 通过 `require('mysql2').createPool().promise()`
  
    ~~~javascript
    const mysql = require('mysql2')
    
    const pool = mysql.createPool({
      host: 'localhost',
      port: 3306,
      database: 'foo_db',
      user: 'root',
      password: 'xxxxxx',
      connectionLimit: 5
    })
    
    pool.getConnection((err, connection) => {
      if (err) {
        console.log(err)
        return
      }
      connection.connect(err => {
        if (err) {
          console.log(err)
          return
        }
        console.log('数据库连接成功')
      })
    })
    
    const connection = pool.promise()
    
    const statement = 'SELECT * FROM t_products WHERE price > ? and brand = ?;'
    connection.execute((statement, [1000, '华为']).then(([values, fields]) => {
      console.log(values)
    }).catch(err => {
        console.log(err)
    })
    // 返回的 Promise 在成功时返回的 result 是一个数组，包含 values 和 fileds
    ~~~
  
  - 也可以通过 `require('mysql2/promise').createPool()`
  
  