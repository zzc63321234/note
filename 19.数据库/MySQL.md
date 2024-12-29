## 数据库分类

- `SQL`
  - 结构化查询语言 `Structed Query Language`

- 通常将数据库划分成两类
  - 关系型数据库
    - 关系型数据库通常会创建很多个二位数据表
    - 数据表之间互相关联，形成一对一、一对多、多对多等关系
    - 可以使用 `SQL` 语句在多张表中查询所需的数据
  - 非关系型数据库
    - `Not only SQL`，简称为 `NoSQL`
    - 相对而言非关系型数据库比较简单一点，存储数据也会更加自由（甚至可以直接将复杂的 `JSON` 对象直接塞入到数据库中）
    - `NoSQL` 是基于 `Key-Value` 的对应关系，并且查询过程不需要经过 `SQL` 解析
    - 非关系型数据库在爬取大量的数据进行存储时比较常用
- 常见关系型数据库
  - `MySQL`
  - `Oracle`
  - `DB2`
  - `SQL Server`
  - `Postgre SQL`

- 常见非关系型数据库
  - `MongoDB`
  - `Redis`
  - `Memcached`
  - `HBse`

## `MySQL` 简介

- `MySQL` 原本是一个开源项目，原开发者为瑞典的 `MySQL AB` 公司
- `2008` 年被 `Sun` 公司收购
- `2009` 年 `Sun` 公司被 `Oracle` 收购
- `MySQL` 是一个关系型数据库程序
  - 这个程序中管理着多个数据库
  - 每个数据库中可以有多张表
  - 每个表中可以有多条数据

## `MySQL` 使用

### 开启服务

- 安装 `MySQL Community Server`
  - [MySQL :: Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/)

- 打开 `MySQL x.x Command Line Client`，输入安装时设置的密码，即可使用 `MySQL` 终端

- 配置环境变量后可直接使用 `cmd`、`bash`、`powershell` 等使用 `MySQL`

  - 进入 `MySQL` 终端

    ~~~bash
    mysql -u root -p
    ~~~

    - `-u root` 表示使用用户名 `root`
    - 回车后输入密码即可使用 `MySQL` 终端
    - 命令末尾可以追加一个 `schema` 名称以启动 `MySQL` 后直接选中该 `schema`

### 终端操作

- 查看数据库

  - ~~~sql
    show databases;
    ~~~

  - 初始有四个数据库

    - `information-schema`
      - 信息数据库，包括 `MySQL` 在维护的其它数据库、表、列、访问权限等信息
    - `mysql`
      - 用于存储数据库管理者的用户信息、权限信息以及一些日志信息等
    - `performance-schema`
      - 性能数据库，用于记录 `MySQL Server` 数据库引擎在运行过程中的一些资源消耗相关的信息
    - `sys`
      - 相当于是一个简易版的 `performance-schema`，将性能数据库中的数据汇总成更容易理解的形式

- 创建新的数据库（以下两条命令效果等同）

  ~~~sql
  create database foo_db;
  create schema foo_db;
  ~~~

- 查看所有数据库（以下两条命令效果等同）

  ~~~sql
  show databases；
  show schemas;

- 进入使用数据库

  ~~~sql
  use foo_db;
  ~~~

- 创建一张表

  ~~~sql
  create table t_users(
    name varchar(20)
    age int
  );
  ~~~

- 查看表

  ~~~sql
  show tables;
  ~~~

- 往表中插入数据

  ~~~sql
  insert into t_user (name, age) values ('zzc6332', 26);
  ~~~

- 查找表中的数据

  ~~~sql
  select * from t_user;
  ~~~

### `GUI` 工具

- 可使用图形化界面工具连接数据库进行操作
- 常见的 `GUI` 工具
  - `Navicat`
  - `SQLYog`
  - `TablePlus`
  - `MySQL Workbench`（官方）

## `MySQL` 数据库的组成

`MySQL` 中的数据库被称为 `Schema` 或 `Database`，它们是相同的概念。

每个 `Schema` 包含四个部分：`Tables`（表）、`Views`（视图）、`Stored Procedure`（存储过程）、`Functions`（函数）。它们是构成数据库结构和操作的重要元素，各自有不同的用途：

- `Tables`：

  `Tables` 是 `Schema` 中存储数据的基本结构，它由行（`Rows`）和列（`Columns`）组成，每一列代表一个字段（`Field`），每一行代表一个数据记录（`Record`）。

  `Tables` 中的数据是结构化的，每一列都有一个确定的数据类型，并可以通过主键（`Primary Key`）、外键（`Foreign Key`）等约束来维护数据的完整性和关系。

- `Views`：

  `Views` 是基于一个或多个 `Tables` 的可查询的虚拟表。它是一个预定义的 `SQL` 查询的结果集。它可以简化复杂的 `SQL` 操作，但它并不存储数据本身。`Views` 每次被访问时，都是动态生成的，它反应了 `Tables` 中数据的当前状态。

- `Stored Procedures`：

  `Stored Procedures` 是一组为了完成特定功能的 `SQL` 语句集。它被编译并存储在数据库中，用户可以通过指定 `Stored Procedures` 的名称来调用执行。它可以接收参数、执行复杂的逻辑、控制事务、更新数据或返回结果集。`Stored Procedures` 是预编译的，因此可以提高性能。

- `Functions`：

  `Functions` 是一种特殊类型的 `Stored Procedures`，主要用于计算和返回单个值。`Functions` 通常用于数据转换、计算和表达式求值等操作，其相较于 `Stored Procedures` 的优势是可以在 `SQL` 语句中直接使用，但是无法执行过于复杂的业务逻辑，比如事务控制。

## 数据类型

- `MySQL` 支持的数据类型有
  - 数字类型
  - 日期和时间类型
  - 字符串（字符和字节）类型
  - 空间类型
  - `JSON` 数据类型

### 数字类型

- 整数数字类型

  - `INT`/`INTEGER`
  - `TINYINT`
  - `SMALLINT`
  - `MEDIUMINT`
  - `BIGINT`

  |   Type    | Storage(Bytes) | Minimum Value Signed | Maximum Value Signed | Minimum Value Unsigned | Maximum Value Unsigned |
  | :-------: | :------------: | :------------------: | :------------------: | :--------------------: | :--------------------: |
  |  TINYINT  |       1        |         -128         |         127          |           0            |          255           |
  | SMALLINT  |       2        |        -32768        |        32767         |           0            |         65535          |
  | MEDIUMINT |       3        |       -8388608       |       8388607        |           0            |        16777215        |
  |    INT    |       4        |     -2147483648      |      2147483647      |           0            |       42949672r5       |
  |  BIGINT   |       8        |        -2^63         |        2^63-1        |           0            |         2^64-1         |

- 浮点数字类型

  - `FLOAT`
    - 单精度浮点数，使用 `32` 位，即 `4` 个字节存储
  - `DOUBLE`
    - 双精度浮点数，使用 `64` 位，即 `8` 个字节存储

- 精确数字类型

  - `DECIMAL`
    - 如 `DECIMAL(2,1)` 表示数字总共有 `2` 位十进制，其中小数部分占 `1` 位
  - `NUMERIC`
    - 从概念上来说，`NUMERIC` 是一个通用的类型，而 `DECIMAL` 则是实际用于表示这个类型的实现方式
    - 在大多数数据库系统中，这两个术语是可以互换使用的

### 日期类型

- `YEAR`
  - 以 `YYYY` 格式显示值
  - 范围 `1901` 到 `2155`，和 `0000`
  - 不常用
- `DATE`
  - 以 `YYYY-MM-DD` 格式显示值
  - 范围 `1000-01-01` 到 `9999-12-31`

- `DATETIME`
  - 以 `YYYY-MM-DD hh:mm:ss` 格式显示值
  - 范围 `1000-01-01 00:00:00` 到 `9999-12-31 23:59:59`
  - 精度为微秒

- `TIMESTAMP`
  - 以 `YYYY-MM-DD hh:mm:ss` 格式显示值
  - 范围同 `UTC` 的时间范围，即  `1970-01-01 00:00:00` 到 `2038-01-19 03:14:07`
  - 精度为微秒

### 字符串类型

- `CHAR`
  - 在创建表时设置固定长度的字符数，可以指定 `0-255` 之间的任何值
  - 被查询时，会删除后面的空格
- `VARCHAR`
  - 在创建表时设置的是可变长度的字符数，可以指定 `0-65535` 之间的值
  - 被查询时，不会删除后面的空格
  - 如 `VARCHAR(20)` 表示一个最长为 `20` 个字符的可变长字符串
- `TEXT`
  - 用于存储大的字符串类型
- `BINARY`/`VARBINARY`
  - 用于存储二进制字符串，会将字符串转化为字节

- `BLOB`
  - 用于存储大的二进制类型

## `SQL` 语句类型

- 什么是 `SQL` 语句
  - `Structed Query Language`，结构化语言，简称 `SQL`，是用于和数据库沟通的语言
  - 使用 `SQL` 编写出来的语句，称之为 `SQL` 语句
- 常见的关系型数据库的 `SQL` 语句都是比较相似的
- `SQL` 语句的常用规范
  - 通常关键字使用大写，如 `CREATE`、`TABLE`、`SHOW` 等
  - 一条语句结束后，需要以 `;` 结尾
  - 如果遇到关键字作为表名或字段名称，可以使用 ` `` ` 包裹

- `SQL` 语句的分类
  - `DDL`
    - `Data Definition Language`，数据定义语言
    - 可以通过 `DDL` 语句对数据库或表结构进行创建、删除、修改等操作
  - `DML`
    - `Data Manipulation Language`，数据操作语言
    - 可以通过 `DML` 语句对表数据进行创建、删除、修改等操作
  - `DQL`
    - `Data Query Language`，数据查询语言
    - 可以通过 `DQL` 从数据库中查询记录
  - `DCL`
    - `Data Control Language`，数据控制语言
    - 对数据库、表格的权限进行相关访问控制操作

### `DDL` 语句

- `Data Definition Language`，对数据库和表结构进行的操作

#### 数据库操作

- 查看当前数据库

  ~~~sql
  # 查看所有的数据
  SHOW DATABASES;
  
  # 使用某一个数据库
  USE foo_db;
  
  # 查看当前正在使用的数据库
  SELECT DATABASE();
  ~~~

- 创建新的数据库

  ~~~sql
  # 创建数据库
  CREATE DATABASE foo_db;
  
  # 如果不存在同名数据库，则创建它
  CREATE DATABASE IF NOT EXISTS foo_db;
  ~~~

- 删除数据库

  ~~~sql
  # 删除数据库
  DROP DATABASE foo_db;
  
  # 如果存在该数据库，则删除它
  DROP DATABASE IF EXIT foo_db;
  ~~~

- 修改数据库

  ~~~sql
  ALTER DATABASE foo_db CHARACTER SET = utf8 COLLATE = utf8_unicode_ci;
  ~~~

#### 数据表操作

##### 表约束

- `PRIMARY KEY` 主键
  - 一张表中，为了区分每一条记录的唯一性，必须有一个字段是永远不会重复，并且不会为空的，这个字段通常会被设置为主键
  - 主键是表中唯一的索引
  - 主键必须是 `NOT NULL` 的，如果没有设置，则会被隐式的设置为 `NOT NULL`
  - 主键可以是多列索引
    - `PRIMARY KEY(key_part, ...)`
    - 一般称之为联合主键
  - 开发中，主键字段应是和业务无关的，不要使用业务字段作为主键

- `UNIQUE` 唯一
  - 一张表中，某些字段希望值是不重复的，则可以使用 `UNIQUE` 来约束这个字段
  - 允许多个 `NULL` 值存在，不会被视为是重复的

- `NOT NULL` 不能为空
  - 该字段必须插入值
- `DEFAULT` 默认值
  - 该字段不设置值时给予一个默认值
- `AUTO_INCREMENT` 自动递增
  - 该字段不设置值时可以根据之前的字段进行递增

##### 表操作

- 查看数据表

  ~~~sql
  # 查看所有的数据表
  SHOW TABLES;
  
  # 查看某一表的表结构
  DESC t_users; # DESC 关键字是 describe 的缩写
  ~~~

- 创建数据表

  ~~~sql
  CREATE TABLE IF NOT EXISTS t_users(
    id INT PRIMARY KEY AUTO_INCREMENT
    name VARCHAR(20) NOT NULL,
    `level` INT DEFAULT 0,
    telPhone VARCHAR(20) DEFAULT '' UNIQUE　NOT NULL
  );
  ~~~

- 删除数据表

  ~~~sql
  # 删除数据表
  DROP TABLE t_users;
  
  # 如果存在该数据表，则删除它
  DROP TABLE IF EXISTS t_users;
  ~~~

- 修改数据表

  ~~~sql
  # 修改表名
  ALTER TABLE `users` RENAME TO `t_users`;
  
  # 添加一个新的字段（filed）
  ALTER TABLE `t_users` ADD `createTime` DATETIME;
  
  # 修改一个字段
  ALTER TABLE `t_users` CHANGE `createTime` `createAt` DATETIME;
  
  # 删除一个字段
  ALTER TABLE `t_users` DROP `createAt`;
  
  # 修改某个字段的类型
  ALTER TABLE `t_users` MODIFY `id` BIGINT;
  ~~~

### `DML` 语句

- `Data Manipulation Language`，对表数据进行的操作

- 插入数据

  ~~~sql
  INSERT INTO `t_users` (`name`, `level`, `telPhone`, `creatAt`) VALUES ('zzc6332', 10, '99999', '2023-08-19 22:15:35');

- 删除数据

  ~~~sql
  # 删除表中所有数据
  DELETE FROM `t_users`;
  
  # 删除表中符合条件的数据
  DELETE FROM `t_users` WHERE `id` = 10;
  ~~~

- 更新数据

  ~~~sql
  # 修改表中所有数据
  UPDATE `t_users` SET `level` = 0, `telPhone` = '';
  
  # 修改表中符合条件的数据
  UPDATE `t_users` SET `level` = 0, `telPhone` = '' WHERE id = 10;
  ~~~

- 拓展：如果希望修改一条数据时，记录下当时的时间可以结合 `DDL` 操作实现

  - 追加更新时间戳

    ~~~sql
    ALTER TABLE `t_users` ADD `updateTime` TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;
    ~~~

  - 或在定义表时设置 `updateTime` 字段

    ~~~sql
    CREATE TABLE `t_users` (
      `id` INT PRIMARY KEY AUTO_INCREMENT
      `name` VARCHAR(20) NOT NULL,
      `level` INT DEFAULT 0,
      `telPhone` VARCHAR(20) DEFAULT '' UNIQUE　NOT NULL
      `updateTime` TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
    );
    ~~~

### `DQL` 语句

- `Data Query Language`，用于查询数据

- `SELECT`

  - 用于从一个或多个表中检索选中的行（`Record`）

  - 查询的格式如下

    ~~~
    SELECT select_expr [, select expr]...
        [FROM table_references]
        [WHERE where_condition]
        [ORDER BY expr [ASC | DESC]]
        [LIMIT {[offset,] row_count | row_count OFFSET offset}]
        [GROUP BY expr]
        [HAVING where_condition]
    ~~~

- 基本查询

  ~~~sql
  # 查询所有的数据并且显示所有的字段
  SELECT * FROM `t_users`;
  
  # 查询 name、level、telphone
  SELECT `name`, `level`, `telphone` FROM `t_users`;
  
  # 给字段起别名
  ## 别名一般在多张表或给客户端返回对应的 key 时会使用到
  ## AS 关键字可以省略
  SELECT `name` AS `userName`, `level` AS `userLevel`, `telphone` AS `userTelphone` FROM `t_users`;
  ~~~

- `where` 条件查询

  - `WHERE` 的比较运算符

    ~~~sql
    # 查询 level 小于 10 的用户
    SELECT * FROM `t_users` WHERE `level` < 10;
    
    # 查询 level 大于等于 10 的用户
    SELECT * FROM `t_users` WHERE `level` >= 10;
    
    # 查询 level 等于 10 的用户
    SELECT * FROM `t_users` WHERE `level` = 10;
    
    # 查询 level 不等于 10 的用户
    SELECT * FROM `t_users` WHERE `level` != 10;
    
    # 查询 level 是 NULL 的用户
    # IS NULL 专门用于检查 NULL 值
    SELECT * FROM `t_users` WHERE `level` IS NULL;
    # 查询 level 不是 NULL 的用户
    SELECT * FROM `t_users` WHERE `level` IS NOT NULL;
    ~~~

  - `WHERE` 的逻辑运算符

    ~~~sql
    # 查询 level 小于 10，且 telphone 是 '' 的用户
    SELECT * FROM `t_users` WHERE `level` < 10 AND `telphone` = '';
    SELECT * FROM `t_users` WHERE `level` < 10 && `telphone` = '';
    
    # 查询 level 小于 10，或 telphone 是 '' 的用户
    SELECT * FROM `t_users` WHERE `level` < 10 OR `telphone` = '';
    SELECT * FROM `t_users` WHERE `level` < 10 || `telphone` = '';
    
    # 查询 level 在 10-20 的用户，包含 10 和 20
    SELECT * FROM `t_users` WHERE `level` >= 10 AND `level` <= 20;
    SELECT * FROM `t_users` WHERE `level` BETWEEN 10 AND 20;
    
    # 查询 name 是 'abc' 或 'cba' 或 'bac' 的用户
    SELECT * FROM `t_users` WHERE `name` = 'abc' OR `level` = 'cba' OR `level` = 'bac';
    SELECT * FROM `t_users` WHERE `name` IN ('abc', 'cba', 'bac');
    ~~~

  - `WHERE` 的模糊查询

    - 使用 `LIKE` 关键字，结合两个特殊的符号

      - `%` 表示匹配任意个的任意字符
      - `_` 表示匹配一个的任意字符

    - 示例

      ~~~sql
      # 查询所有以 z 开头的 name
      SELECT * FROM `t_users` WHERE `name` LIKE 'z%';
      
      # 查询所有带 z 的 name
      SELECT * FROM `t_users` WHERE `name` LIKE '%z%';
      
      # 查询所有第三个字符是 z 的 name
      SELECT * FROM `t_users` WHERE `name` LIKE '__z%';
      ~~~

- 查询结果排序

  - `ORDER BY` 关键字可将查询结果按照某种方式进行排序，有两个常用的值

    - `ASC` 升序排列
    - `DESC` 降序排列

  - 示例

    ~~~sql
    # 查询所有结果，并按 level 升序排列
    SELECT * FROM `t_users` ORDER BY `level` ASC;
    ~~~

- 分页查询

  - 格式

    ~~~
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    ~~~

  - 示例

    ~~~sql
    # 以 30 条数据为一页，分别查询第 1，2，3 页
    SELECT * FROM `t_users` LIMIT 30 OFFSET 0;
    SELECT * FROM `t_users` LIMIT 30 OFFSET 30;
    SELECT * FROM `t_users` LIMIT 30 OFFSET 60;
    # 以 30 条数据为一页，分别查询第 3 页，另一种写法
    SELECT * FROM `t_users` LIMIT 60, 30；
    ~~~

## 高级查询

### 聚合函数

- 聚合函数表示对**值的集合**进行操作的**组（集合）函数**
- 聚合函数是 `DQL` 语句的内容

- 示例

  ~~~sql
  # 华为手机价格的平均值
  SELECT AVG(price) FROM t_products WHERE brand = '华为';
  
  # 所有手机中最高价格和最低价格
  SELECT MAX(price) FROM t_products;
  SELECT MIN(price) FROM t_products;
  
  # 计算总投票人数
  SELECT SUM(voteCnt) FROM t_products;
  
  # 计算所有条目的数量
  SELECT COUNT(*) FROM t_products;
  # 计算华为手机条目的数量
  SELECT COUNT(*) FROM t_products WHERE brand = '华为';
  # 计算所有条目中，voteCnt 字段值不是 NULL 值的数量
  SELECT COUNT(voteCnt) FROM t_products;
  # 计算品牌的数量，如果 brand 字段为 NULL 值则不计入，DISTINCT 会将相同值的字段记为 1 次
  SELECT COUNT(DISTINCT brand) FROM t_products;
  ~~~
  
  - 注意不要直接 `SELECT` 聚合函数和表中的字段，如需查询表中对应的字段信息，需配合 `Group By`

### `Group By`

- `Group By` 通常和聚合函数一起使用，表示先对数据进行分组，再对一组数据进行聚合函数计算

- 上面聚合函数中的示例相当于将所有的数据分成了一组

- 示例

  ~~~sql
  # 根据品牌进行分组，计算出各个品牌中的最高价格和最低价格
  SELECT brand, MAX(price), MIN(price) FROM t_products GROUP BY brand;
  
  # 根据品牌分类，计算出各个品牌的商品数量、平均价格（精确到小数点后2位）、平均评分，并为它们起别名
  SELECT brand,
    COUNT(*) `count`,
    ROUND(AVG(price), 2) avgPrice,
    AVG(score) avgScore
  FROM t_products
  GROUP BY brand;

- `Group By` 约束条件

  - 通过使用 `HAVING` 关键字对 `Group By` 查询到的结果添加一些约束条件

  - 用法同 `WHERE`

    - `WHERE` 作用于原始数据集
    - `HAVING` 作用于聚合后的分组

  - 示例

    ~~~sql
    # 从查询到的结果中筛选出平均价格在 4000 以下，平均分大于等于 7 的品牌
    SELECT brand,
      COUNT(*) `count`,
      ROUND(AVG(price), 2) avgPrice,
      AVG(score) avgScore
    FROM t_products
    GROUP BY brand
    HAVING avgPrice < 4000 && avgScore >= 7;
    ~~~

### 多表关联

#### 外键

- 外键约束的主要意义是对两个表之间关联的字段进行约束。它可以确保从表中的记录在主表中有一个对应的记录，防止在子表中插入无效的引用数据，以确保这种引用关系是有效的且一致的。

  外键记录与多表查询操作是独立的，即使两个表之间没有外键关系，也可以通过共同的字段或逻辑连接来执行多表查询。

  由于多表关联中，一般是从属方（从表）对主控方（主表）有依赖，因此大多数情况下，外键设置在从表的一方。

- 场景

  - 例如在 `t_products` 表中，对应的品牌还包括其它信息，如品牌的官网、品牌的世界排名等，如果表示在 `t_products` 表中则相同品牌的条目会造成大量冗余数据，因此可以将这些品牌相关的数据放到另一张表中

    ~~~sql
    CREATE TABLE IF NOT EXISTS t_brands(
      id INT PRIMARY KEY AUTO_INCREMENT,
      name VARCHAR(20) NOT NULL,
      website VARCHAR(20),
      worldRank INT
      # ,brand_id INT,
      # FOREIGN KEY (brand_id) REFERENCES t_brands(id) # 创建外键
    );
    ~~~
  
  - 插入模拟数据
  
    ~~~sql
    INSERT INTO t_brands (name, website, worldRank) VALUES ('华为', 'www.huawei.com', 2);
    INSERT INTO t_brands (name, website, worldRank) VALUES ('小米', 'www.mi.com', 10);
    INSERT INTO t_brands (name, website, worldRank) VALUES ('苹果', 'www.apple.com', 5);
    INSERT INTO t_brands (name, website, worldRank) VALUES ('oppo', 'www.oppo.com', 15);
    ~~~
  
  - 创建外键
  
    - 将两张表联系起来，可以将 `t_products` 中的 `brand_id` 字段关联到 `t_brands` 中的 `id` 字段
  
      - 如果要在创建表时添加外键约束，可以在创建表的括号最后添加如下语句
  
        ~~~sql
        # 添加 brand_id 字段
        brand_id INT,
        # 为 brand_id 字段设置外键约束
        FOREIGN KEY (brand_id) REFERENCES t_brand(id)
        ~~~
  
      - 如果表已创建好，额外添加外键
  
        ~~~sql
        # 添加 brand_id 字段
        ALTER TABLE t_products ADD brand_id INT;
        # 为 brand_id 字段设置外键约束
        ALTER TABLE t_products ADD FOREIGN KEY (brand_id) REFERENCES t_brands(id);
        ~~~
  
    - 现在可以将 `t_products` 中的 `brand_id` 关联到 `t_brands` 中的 `id` 的值
  
      ~~~sql
      UPDATE t_products SET brand_id = 1 WHERE brand = '华为';
      UPDATE t_products SET brand_id = 2 WHERE brand = '小米';
      UPDATE t_products SET brand_id = 3 WHERE brand = '苹果';
      UPDATE t_products SET brand_id = 4 WHERE brand = 'OPPO';
      ~~~

  - 更新和删除外键

    - 设置了外键约束后，被关联的外键默认情况下不能更新或删除

    - 如果需要更新和删除，需要修改 `ON DELETE` 和 `ON UPDATE` 的值

    - 可用值
  
      - `RESTRICT`
        - 默认值
        - 当更新或删除某个记录时，会检查该记录是否有关联的外键记录，如果有则会报错，不允许更新或删除
      - `NO ACTION`
        - 在 `SQL` 中定义的标准
    
        - 在 `MySQL` 中使用上与 `RESTRICT` 一致
    
      - `CASCADE`
        - 当更新或删除某个记录时，会检查该记录是否有关联的外键记录，如果有则会更新或删除关联的记录
      - `SET NULL`
        - 当更新或删除某个记录时，会检查该记录是否有关联的外键记录，如果有则会将关联的记录设置为 `NULL`
    

#### 多表查询

- 直接查询的方式

  - 直接从两张表中查询数据

    ~~~sql
    SELECT * FROM t_products, t_brands;
    ~~~

    - 得到的数据是 `t_products` 中的每一个条目，和 `t_brands` 中的每一个条目结合一次组成的条目，数量是两张表条目数的乘积
    - 这个结果称之为**笛卡尔乘积**（Cartesian product），又称**直积**
    - 对于不对应的条目组成的数据是无意义的

  - 直接从两张表中查询有意义数据

    ~~~sql
    SELECT * FROM t_products, t_brands WHERE t_products.brand_id = t_brans.id;
    ~~~

    - 表示将查询到笛卡尔乘积后的结果中，将符合 `t_products.brand_id = t_brans.id` 条件的数据过滤出来

- 多表之间的连接

  - 如果需要从多张表中获取某些特定的数据，可以使用 `SQL JOIN` 操作

  - 连接方式分为`左连接`、`右连接`、`内连接`、`全连接`

    - 左连接

      - 获取到的数据以左表为主

      - 左表中的条目无论是否与右表有对应的数据关联，都会被查询出来

        - 没有右表对应数据关联的条目，对应的右表字段是 `NULL`

      - 写法是 `LEFT [OUTER] JOIN 表名 ON 连接条件`，其中 `OUTER` 可以省略

      - 左连接在开发中最常用

      - 示例

        ~~~sql
        # 呈现左表所有数据，并且将右表中有关联的数据合并呈现
        SELECT * FROM t_products LEFT JOIN t_brands ON t_products.brand_id = t_brand.id;
        
        # 在上面的查询基础上，只显示与右表有关联的数据
        SELECT * FROM t_products LEFT JOIN t_brands ON t_products.brand_id = t_brand.id WHERE t_brand.id IS NOT NULL;
        ~~~

    - 右连接

      - 获取到的数据以右表为主
      - 右表中的条目无论是否与左表有对应的数据关联，都会被查询出来
        - 没有左表对应数据关联的条目，对应的左表字段是 `NULL`
      - 写法是 `RIGHT [OUTER] JOIN 表名 ON 连接条件`，其中 `OUTER` 可以省略

    - 内连接

      - 获取到的数据是左表和右表都有对应的数据关联

      - 写法 `CROSS JOIN` 或 `INNER JOIN` 或 `JOIN`

      - 示例

        ~~~sql
        SELECT * FROM t_products JOIN t_brands ON t_products.brand_id = t_brand.id;
        ~~~

        - 这种写法与上方的`直接从两张表中查询有意义数据`中的写法实现的效果相同，但代表的含义不同
          - 内连接写法代表的是两张表连接时就会约束数据之间的关系，来决定之后查询的结果
          - 直接查询的写法代表的是先计算出笛卡尔乘积，在此基础上进行条件筛选

    - 全连接

      - 全连接会将左右表中所有的条目都查询出来，有数据关联的条目会被合并，没有数据关联的条目，多余字段是 `NULL`

      - `SQL` 规范中全部连接使用的是 `FULL JOIN`，但 `MySQL` 并没有对它的支持，需要使用 `UNION` 来实现

      - 使用 `UNION` 将左连接的查询结果与右连接的查询结果联合在一起，来实现全连接的效果

      - 示例

        ~~~sql
        (SELECT * FROM t_products LEFT JOIN t_brands ON t_products.brand_id = t_brand.id)
        UNION
        (SELECT * FROM t_products RIGHT JOIN t_brands ON t_products.brand_id = t_brand.id);
        ~~~

  - 场景：将查询到的品牌信息放到一个单独的对象中
  
    ~~~sql
    SELECT
      pdt.id id, pdt.title title, pdt.price price,
      JSON_OBJECT('id', bd.id, 'name', bd.name, 'website', bd.website) AS brand
    FROM t_products AS pdt LEFT JOIN t_brands AS bd ON pdt.brand_id = bd.id
    ~~~
  
    - 使用 `JSON_OBJECT()` 聚合函数将 `t_brands` 表中的数据转换成 `JSON` 对象

#### 多对多关系

- 场景

  - 例如学生选课，一个学生可以选择多门课程，一个课程可以被多个学生选择

  - 建立学生表和课程目录表

    ~~~sql
    # 创建学生表
    CREATE TABLE IF NOT EXISTS `students`(
      id INT PRIMARY KEY AUTO_INCREMENT,
      name VARCHAR(20) NOT NULL,
      age INT
    );
    
    # 创建课程目录表
    CREATE TABLE IF NOT EXISTS `courses`(
      id INT PRIMARY KEY AUTO_INCREMENT,
      name VARCHAR(20) NOT NULL,
      price DOUBLE NOT NULL
    );
    
    # 插入学生数据
    INSERT INTO `students` (name, age) VALUES(`LiLei`, 18);
    INSERT INTO `students` (name, age) VALUES(`HanMeiMei`, 19);
    INSERT INTO `students` (name, age) VALUES(`Tom`, 21);
    INSERT INTO `students` (name, age) VALUES(`Rick`, 23);
    INSERT INTO `students` (name, age) VALUES(`Morty`, 15);
    
    # 插入课程数据
    INSERT INTO `courses` (name, price) VALUES(`英语`, 100);
    INSERT INTO `courses` (name, price) VALUES(`数学`, 998);
    INSERT INTO `courses` (name, price) VALUES(`物理`, 666);
    INSERT INTO `courses` (name, price) VALUES(`历史`, 888);
    ~~~

  - 建立一个关系表来记录两张表中的数据关系

    ~~~sql
    # 创建关系表
    CREATE TABLE IF NOT EXISTS `students_reference_courses`(
      id INT PRIMARY KEY AUTO_INCREMENT,
      student_id INT NOT NULL,
      course_id INT NOT NULL,
      FOREIGN KEY (student_id) REFERENCES students(id) ON UPDATE CASCADE ON DELETE CASCADE,
      FOREIGN KEY (course_id) REFERENCES course(id) ON UPDATE CASCADE ON DELETE CASCADE
    )
    
    # 插入关系数据
    INSERT INTO `students_reference_courses` (student_id, course_id) VALUES(1, 2);
    INSERT INTO `students_reference_courses` (student_id, course_id) VALUES(1, 3);
    INSERT INTO `students_reference_courses` (student_id, course_id) VALUES(2, 1);
    INSERT INTO `students_reference_courses` (student_id, course_id) VALUES(2, 3);
    INSERT INTO `students_reference_courses` (student_id, course_id) VALUES(2, 4);
    ~~~

  - 查询多对多数据

    ~~~sql
    # 查询有选课的学生的选课情况
    SELECT
      stu.name studentName, cs.name courseName, cs.price coursePrice
    FROM `students` stu # 为表起别名
    JOIN `students_reference_courses` r
      ON stu.id = r.student_id
    JOIN `courses` cs
      ON r.course_id = cs.id
    
    # 查询所有学生的选课情况
    SELECT
      stu.name studentName, cs.name courseName, cs.price coursePrice
    FROM `students` stu
    LEFT JOIN `students_reference_courses` r
      ON stu.id = r.student_id
    LEFT JOIN `courses` cs
      ON r.course_id = cs.id
    
    # 查询单个学生的选课情况
    SELECT
      stu.name studentName, cs.name courseName, cs.price coursePrice
    FROM `students` stu
    LEFT JOIN `students_reference_courses` r
      ON stu.id = r.student_id
    LEFT JOIN `courses` cs
      ON r.course_id = cs.id
      WHERE stu.id = 1
    
    # 查询没有选课的学生
    SELECT
      stu.name studentName, cs.name courseName, cs.price coursePrice
    FROM `students` stu
    LEFT JOIN `students_reference_courses` r
      ON stu.id = r.student_id
    LEFT JOIN `courses` cs
      ON r.course_id = cs.id
      WHERE cs.id IS NULL
    
    # 查询没有被学生选的课程
    SELECT
      stu.name studentName, cs.name courseName, cs.price coursePrice
    FROM `students` stu
    RIGHT JOIN `students_reference_courses` r
      ON stu.id = r.student_id
    RIGHT JOIN `courses` cs
      ON r.course_id = cs.id
      WHERE stu.id IS NULL
    ~~~

  - 将查询到学生选的多门课放到数组中

    ~~~sql
    SELECT
      stu.id id, stu.name name, stu.age age,
      JSON_ARRAYAGG(JSON_OBJECT('id', cs.id, 'name', cs.name, 'price', cs.price)) courses
    FROM `students` stu
    LEFT JOIN `students_reference_courses` r
      ON stu.id = r.student_id
    LEFT JOIN `courses` cs
      ON r.course_id = cs.id
    GROUP BY stu.id;
    ~~~

### 子查询

- 可以将一条 `SQL` 查询语句的查询结果作为另一个 `SQL` 查询语句的返回字段

- 示例

  ~~~mysql
  SELECT
    m.id id, m.content content,
    (SELECT COUNT(*) FROM comments c WHERE c.moment_id = m.id) comment_count
  FROM moment m
  ~~~

## 事务（`Transaction`）

事务（`Transaction`）是由单独单元的一个或多个 `SQL` 语句组成，在这个单元中，每个 `SQL` 语句都是互相依赖的。整个单独单元是一个不可分割的整体存在，类似于物理当中的原子（不可分割的最小单位）。

通俗来说，事务是一个整体，里面的内容要么都执行成功，要么都不成功，不存在部分执行成功而部分执行不成功的情况。如果单元中的某条 `SQL` 语句执行失败或产生错误，那么整个单元都会回滚，所有受到影响的数据将返回到事务开始之前的状态。如果单元中的所有 `SQL` 语句都执行成功的话，那么该事务也就被顺利执行。

在 `MySQL` 中，可以使用 `show engines;` 语句来查看支持的存储引擎，以及存储引擎对事务的支持情况。

### 事务的作用

事务管理对于企业级应用而言至关重要，它保证了用户的每一次操作都是可靠的，即使出现了异常的访问情况，也不至于破坏后台数据的完整性。

例如银行客户使用 `ATM` 机时的操作看作一个事务，如果操作中 `ATM` 出现了故障，那么必须保证该次故障发生时的整个操作都不会生效，以保证客户和银行的利益都不受损失。

### 事务的四个特性（`ACID`）

- 原子性（`Atomicity`）：

  事务是一个不可分割的最小工作单位，事务中的所有操作要么都执行成功，要么都不成功。

- 一致性（`Consistency`）：

  事务执行的结果必须使数据库从一个正确的状态转换到另一个正确的状态。

  可以理解为，事务按照预期生效，事务执行前后数据的状态是符合预期的。

  例如在银行转账的场景中，如果从 `A`  账户向 `B` 账户转账 `100` 元，转帐前和转账后两个账户的数据都是正确的状态，就是数据的一致性。如果事务执行完成后， `A` 账户减少了 `100` 元，而 `B` 账户没有增加 `100`，那么就没有达到一致性。

- 隔离性（`Isolation`）：

  一个事务的执行不能被其它事务干扰。

  例如多个用户并发访问数据库时，数据库为每一个用户开启的事务相互独立，不能被其它用户的事务所影响。

- 持久性（`Durability`）：

  一个事务一旦提交成功，它对数据库中数据的改变不能再被回滚。

### 并发事务可能引发的问题

在多个事务处理同一个数据时，如果没有采取有效的隔离机制，那么在并发处理数据时会带来一些问题：

- 脏读：

  在一个事务处理过程中读取了另一个未提交的事务中的数据。例：

  > 假设有一条数据是 NULL 值。事务 A 更新了该数据的值为 A 值，此时（事务 A 还没有完成）事务 B 去查询了这条数据的值，读取到的是 A 值。这种情况就是脏读。如果之后事务 A 并没有提交，而是进行了回滚，那么之前事务 B 读取到的值就是一个临时的数据 A 值。而预期读取的应该是 NULL 值，因为事务 A 并没有被提交。

- 丢失更新（脏写）：

  撤销一个事务时，把其它事务已提交成功的更新数据覆盖了。例：
  
  >假设有一条数据是 NULL 值。事务 A 更新了该数据的值为 A 值，此时（事务 A 还没有完成）事务 B 也更新了这条数据的值为 B 值并且提交了。这种情况就是脏写。如果之后事物 A 并没有提交，而是进行了回滚，那么该条数据的值会被回滚为 NULL 值。而该条数据预期的应该是 B 事物对其更新的 B 值，而它被 A 事物的回滚覆盖了。
  
  情景示例：
  
  > 小明去银行柜台存钱，他的账户中原来的余额为 100 元，现在打算存入 100 元。在他存钱的过程中，账户刚好自动扣除了 5 元的银行卡年费，余额变成了 95 元。这时，小明突然又想用这 100 元请女朋友看电影吃饭，不打算存了。在他撤回存钱操作后，账户余额恢复成了在存款操作之前的 100 元。那么，小明账户中本该扣除的 5 元却没有被扣除。
  
- 丢失更新（第二类）

  如果多个事务对同一条数据进行读取，并先后对该条数据进行”对该条数据修改后提交“的操作，那么先提交的事务所作的更新会丢失。情景示例：
  
  > 小明和女朋友去逛街，女朋友看中了一支口红，遂让女朋友用自己的银行卡去买，自己坐在商场座椅上刷手机等待。此时小明在手机上下单了附近奶茶店的奶茶。他们的付款渠道用的是同一张银行卡，当他们都付款成功后，却发现银行只扣了奶茶钱，却没有扣除买口红的钱。
  
- 不可重复读：

  事务在一个数据里读取一个数据多次，期间由于其它事务对该数据的修改，导致每次读出来的数据不一致。例：

  > 假设有一条数据是 `NULL` 值。开启事务 `A` 读取这个数据，读到的是 `NULL` 值。此时（事务 `A` 还没有完成）事务 `B` 更新了这个数据的值为 `B` 值并提交。之后事务 `A` 再次读取这个数据，读到的是 `B` 值。在同一个事务 `A` 中两次对同一条数据读取到的值不一致，这就是不可重复读。不过不可重复读并不一定是一个问题，这取决于数据库的预期表现是否可重复读。

- 幻读/虚读：

  事务在多次查询一系列数据行的期间，由于其它事务对表进行了插入或删除操作，导致多次查询到的数据集合中的数据行不同。幻读类似于不可重复读，不可重复读关注的是单行数据的变化，幻读关注的是查询到的数据集合的变化。

### 事务隔离级别

事务隔离级别是为了解决并发事务引发的问题而诞生的。事务隔离级别越高，在并发下产生的问题越少，同时付出的性能消耗也越大。因此很多时候必须在并发性和性能之间做权衡，根据项目情况选择合适的事务隔离级别。其中避免脏写是所有隔离级别共同遵守的规则，因为脏写的存在一定会破坏数据的完整性和一致性。

#### `Default`

默认隔离级别。每种数据库支持的事务隔离级别不一样。在 `MySQL` 中，可以使用 `SELECT @@transaction_isolation;` 命令来查看默认的事务隔离级别。

#### `Read uncommitted`

能够读取到未被提交的数据。这种隔离级别是最低级别，无法解决脏读、不可重复读、幻读中的任何一种。

#### `Read comitted`

只能读取到已被提交的数据。这种隔离级别可以防止脏读，但是无法限制不可重复读和幻读。

#### `Reapeatable read`

可重复读取，即在数据被读取后加锁，防止其它事务更新它，直到该事务被提交。可以防止脏读，不可重复读，无法限制幻读。

#### `Serializable`

串行化。这是最高的事务隔离级别，任何事务必须要在其它所有事务的子事务都执行完成后才能执行。这种事务隔离级别效率低下，性能消耗极大。

#### 总结

| 事务隔离级别       | 脏写 | 脏读 | 不可重复读 | 幻读 |
| ------------------ | :--: | :--: | :--------: | :--: |
| `Read uncommitted` |  ×   |  √   |     √      |  √   |
| `Read comitted`    |  ×   |  ×   |     √      |  √   |
| `Reapeatable read` |  ×   |  ×   |     ×      |  √   |
| `Serializable`     |  ×   |  ×   |     ×      |  ×   |

从上往下，级别越高，并发性能越差，安全性越高。

### `MySQL` 中使用事务

事务分为隐式事务和显式事务。在直接使用 `DML` 语句（`INSERT`、`UPDATE`、`DELETE`）时，就会自动开启事务。隐式事务没有明显的开始和结束标记。在 `MySQL` 中隐式事务默认会自动提交。可以通过 `autocommit` 环境变量设置是否自动提交隐式事务：

> 使用以下命令查看当前是否开启了 `autocommit` 功能：
>
> ~~~sql
> SELECT @@autocommit;
> ~~~
>
> 如果数值为 `0` 则表示未开启，如果为 `1` 则表示已开启。
>
> 可以使用以下命令修改 `autocommit`：
>
> ~~~sql
> SET autocommit = 0;
> ~~~
>
> 如果没有开启 `autocommit`，则隐式事务不会自动提交，需要手动使用 `COMMIT;` 命令提交。

显式事务需要显式声明事务开启，即使开启了 `autocommit`，显式事务也不会被自动提交：

> 以下命令表示开启事务：
>
> ~~~sql
> start transaction;
> ~~~
>
> 开启事务后，使用的 `SQL` 语句都是在该事务中进行。
>
> 如果要结束当前事务，可以使用 `COMMIT;` 命令提交当前事务中的所有更改，或使用 `ROLLBACK;` 命令撤销当前事务中的所有更改。
>
> 事务中可以设置保存点（回滚点），并可以在事务中使用 `ROLLKBACK TO [保存点];` 语法命令将保存点之后的所有修改都撤销，且当前事务继续。例：
>
> ~~~sql
> -- 开启事务
> start transaction;
> -- 删除id为2的记录
> delete from t_foo where id = 1;
> -- 设置保存点名为A
> savepoint A;
> -- 删除id为3的记录
> delete from t_foo where id = 2;
> -- 回滚到AA保存点处
> rollback to A;
> ~~~

## 高级特性

### `View`

可以为 `SQL` 语句建立一个 `View`：

~~~sql
CREATE VIEW customer_orders AS 
    SELECT 
        c.name AS customer_name, 
        o.id AS order_id, 
        o.order_date, 
        o.total_amount
    FROM customers c
    JOIN orders o ON c.id = o.customer_id;
~~~

这里的语法是 `CREATE VIEW [表名] AS [DQL语句]` 。`View` 中并不存储数据本身，其中的内容是动态生成的。

使用以下命令可查询上面创建的 `View`：

~~~sql
SELECT * FROM customer_orders;
~~~

`View` 一般只用来查询，如果用它进行增删改会有很多限制。

### `Stored Procedure`

`Stored Procedure` 允许将一组为了完成特定任务的 SQL 语句集合存储在数据库中。这些 `SQL` 语句可以包含复杂的逻辑，可以操作多个表，实现复杂的数据处理功能。`Stored Procedure` 也支持接收参数在 `SQL` 语句中使用。

创建 `Stored Procedure` 的基本语法：

~~~sql
CREATE PROCEDURE [存储过程名] ([参数列表]) AS -- AS 可省略
BEGIN
    -- SQL 语句
END;
~~~

- 其中参数列表中的参数可以是输入参数（`IN`），输出参数（`OUT`），或者两者兼有（`INOUT`）。

  - `IN` 参数是一种程序传递到 `Stored Procedure` 中的输入参数。`Stored Procedure` 中可以使用该参数进行计算和比较等操作，但不能修改此参数。
  - `OUT` 参数是一种从 `Stored Procedure` 中传出的输出参数。该参数的值在 `Stored Procedure` 中无法被读取，但可以被赋值修改。当 `Stored Procedure` 执行结束后，该参数的值将返回给程序或调用者。

  - `INOUT` 参数既可以作为输入参数，也可以作为输出参数。

- 参数列表中的参数需要指定数据类型。

`IN` 参数 和 `OUT` 参数使用示例：

>定义一个 `Stored Procedure`：
>
>~~~sql
>-- 将语句分隔符临时设置为 $$，以便如果要存储多条 SQL 语句时，原本的语句分隔符 ; 不会被解析为结束定义
>DELIMITER $$ 
>
>-- 接收两个输入参数 a 和 b，两个输出参数 sum 和 avg
>CREATE PROCEDURE `sum_and_avg`(IN a INT, IN b INT, OUT sum INT, OUT avg DOUBLE)
>BEGIN
>    -- 可以读取 IN 参数 a 和 b，但无法修改
>    -- 无法直接读取 OUT 参数 sum 和 avg，但可以修改，且可以读取修改后的值
>    SET sum = a + b;
>    SET avg = sum / 2;
>END $$
>
>-- 将语句分隔符恢复为默认的 ；符号
>DELIMITER ;
>~~~
>
>调用该 `Stored Procedure`：
>
>~~~sql
>-- 定义变量 @sum 和 @avg
>SET @sum = 0;
>SET @avg = 0;
>-- 调用 sum_and_avg，传入输入参数 1、2，将 @sum、@avg 变量作为输出参数传入
>CALL sum_and_avg(1, 2, @sum, @avg);
>-- 输出参数在 sum_and_avg 中被修改后的值会返回给程序中的变量，使用 SELECT 语法查看被修改后的变量
>SELECT @sum, @avg;
>~~~

`INOUT` 参数使用示例：

> 定义一个 `Stored Procedure`：
>
> ~~~sql
> -- 将语句分隔符临时设置为 $$，以便如果要存储多条 SQL 语句时，原本的语句分隔符 ; 不会被解析为结束定义
> DELIMITER $$ 
> 
> -- 接收两个 INOUT 参数 a 和 b
> CREATE PROCEDURE `swap`(INOUT a INT, INOUT b INT)
> BEGIN
>     -- INOUT 参数 a 和 b 可以直接读取，也可以修改
>     DECLARE temp INT;
>     SET temp = a;
>     SET a = b;
>     SET b = temp;
> END $$
> 
> -- 将语句分隔符恢复为默认的 ；符号
> DELIMITER ;
> ~~~
>
> 调用该 `Stored Procedure`：
>
> ~~~sql
> -- 定义变量 @a 和 @b
> SET @a = 1;
> SET @b = 2;
> -- 调用 swap，将变量 @a 和 @b 作为 INOUT 参数传入
> CALL swap(@a, @b);
> -- INOUT 参数可以作为输出参数，因此变量 @a 和 @b 被 swap 修改了
> SELECT @a, @b;
> ~~~

结合查询数据表的示例：

>~~~sql
>-- 将语句分隔符临时设置为 $$，以便如果要存储多条 SQL 语句时，原本的语句分隔符 ; 不会被解析为结束定义
>DELIMITER $$ 
>
>-- 定义一个名为 get_customer_orders 的 Stored Procedure，它接收一个名为 customer_id 的整数类型的输入参数
>CREATE PROCEDURE get_customer_orders(IN customer_id INT)
>
>-- 开始定义 Stored Procedure 中的 SQL 语句
>BEGIN
>        -- 执行一个 SELECT 查询，返回 ID 与传入的 customer_id 参数相同的客户的订单信息
>        SELECT o.id AS order_id, o.order_date, o.total_amount
>        FROM orders o
>		WHERE o.customer_id = customer_id;
>-- 结束定义
>END $$
>
>-- 将语句分隔符恢复为默认的 ；符号
>DELIMITER ;
>~~~
>
>可以用以下命令传入参数以调用上面定义的 `Stored Procedure`：
>
>~~~sql
>call practice.get_customer_orders(1); -- 查询 ID 为 1 的客户的订单信息
>~~~

### `Function`

`MySQL` 中的聚合函数就是一种特殊的 `Function`。 

`MySQL` 默认不允许用户创建 `Function`，需要设置以下变量以解锁：

~~~sql
SET GLOBAL log_bin_trust_function_creators = 1;
~~~

`MySQL` 中只支持标量值函数，也称为单行函数，是对于每个输入行返回一个值的函数。通常用于进行数据转换、计算和格式化等操作。

标量值 `Function` 定义语法如下：

~~~sql
CREATE
    FUNCTION [函数名] ([参数列表])
    RETURNS [返回值数据类型]
AS
BEGIN
-- SQL 语句（必须返回变量或值）
END;
~~~

示例：

> 创建函数：
>
> ~~~sql
> DELIMITER $$
> 
> CREATE
>     FUNCTION square(x INT)
>     RETURNS INT
> BEGIN
>     DECLARE result INT;
>     SET result = x * x;
>     RETURN result;
> END $$
> 
> DELIMITER ;
> ~~~
>
> 自定义的函数可以在 `SELECT` 语句中要选中的内容中使用：
>
> ~~~sql
> SELECT square(column_foo) FROM table_foo
> ~~~
>
> 也可以在查询条件中使用：
>
> ~~~sql
> SELECT name FROM table_students WHERE SCORE = square(10)
> ~~~

## 变量

`MySQL` 中可以使用 `SET` 和 `DECLARE` 来定义变量。它们的使用场景和特性有所不同：

- `SET`：
  - `SET` 语句定义的变量是用户定义的会话变量，在整个会话中都有效。
  - `SET` 语句定义的变量必须以 `@` 开头。
  - `SET` 语句可以在定义变量的同时进行赋值。
  - `SET` 语句定义的变量无需指定数据类型。
  - `SET` 语句可以定义变量，也可以修改变量。
- `DECLARE`：
  - `DECLARE` 语句定义的变量是局部变量，在 `Stored Procedure` 或 `Function` 内部的 `BEGIN...END` 语句块之内有效。
  - `DECLARE` 语句定义的变量不必以 `@` 开头。
  - `DECLARE` 只能用于定义变量，且不能在声明的同时进行赋值
  - `DECLARE` 必须指定数据类型。

`MySQL` 中可以使用 `SELECT...INTO...` 语法从查询中提取值并将它们赋值给预先定义的变量：

>~~~sql
>SELECT c1, c2, c3 INTO @v1, @v2, @v3 FROM table_name WHERE condition;
>~~~
>
>这里，`c1`, `c2`, `c3` 是表中的列或者表达式，而 `@v1`, `@v2`, `@v3` 是变量，用于存储相应的列或表达式结果。

## 补充

- 如何重置表的 `AUTO_INCREMENT` 计数值

  在MySQL中，如果需要重置一个 `AUTO_INCREMENT`字段，可以根据具体需求使用以下方法：

  1. **更改表的 `AUTO_INCREMENT` 计数值**

     使用 `ALTER TABLE`语句可以设置 `AUTO_INCREMENT` 计数器的值。以下示例将表 `my_table` 的 `AUTO_INCREMENT` 值重置为 `1`：

     ~~~sql
     ALTER TABLE my_table AUTO_INCREMENT = 1;
     ~~~

     注意，这种方法只会影响新插入的数据。如果表中存在一些较大的 `AUTO_INCREMENT` 值，则大于此设置值的现有 `AUTO_INCREMENT` 值将不会受到影响，并且新数据的 `AUTO_INCREMENT` 值可能仍然增长。

  2. **清空表并重置所有 `AUTO_INCREMENT` 计数器**

     如果你希望表中的所有现有数据都被删除，并将 `AUTO_INCREMENT` 计数器彻底重置，可以使用 `TRUNCATE TABLE` 语句：

     ~~~sql
     TRUNCATE TABLE my_table;
     ~~~

     这将删除表中的所有数据，并将 `AUTO_INCREMENT` 计数器重置为 `1`（或其它定义值，如果在创建表时设置了不同的初始值）。

     请注意，`TRUNCATE TABLE` 是一个具有破坏性的操作，因为它会删除表中的所有数据。在执行此操作之前，请确保正确备份数据并确保这符合你的需求。

     需要注意的是，在对 `AUTO_INCREMENT` 字段进行操作时，要确保数据库中不存在引用这些 `AUTO_INCREMENT` 值的外键约束。在进行上述操作之前，你可能需要调整或删除与这些外键约束相关的记录。

- 将 `timestamp` 类型的字段返回原始格式

  ~~~sql
  SELECT UNIX_TIMESTAMP(created_at) AS created_at FROM orders
  ~~~

  

  