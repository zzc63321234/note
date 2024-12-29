## 简介

`Redis` 遵守 `BSD` 开源协议，使用 `C` 语言开发。

`Redis` 全称 `Remote Dictionary Server`，通常被称为数据结构服务器。`Redis` 中的数据是以 `key-value` d的形式存储的。`Redis` 中键（`Key`）是字符串（`String`）类型，值（`Value`）可以是多种结构，如字符串（`String`）、哈希（`Hash`）、列表（`List`）、集合（`Set`）、有序集合（`Sorted Set`）等。

## 使用场景

`Redis` 最广泛的应用场景是 `Cache`，可用于：

- **排行榜**，如果使用传统的关系型数据库来做，非常麻烦，而利用 `Redis` 的 `SortSet` 数据结构能够非常方便搞定；
- **计算器/限速器**，利用 `Redis` 中原子性的自增操作，我们可以统计类似用户点赞数、用户访问数等，这类操作如果用 `MySQL`，频繁的读写会带来相当大的压力；限速器比较典型的使用场景是限制某个用户访问某个 `API` 的频率，常用的有抢购时，防止用户疯狂点击带来不必要的压力；
- **好友关系**，利用集合的一些命令，比如求交集、并集、差集等，可以方便搞定一些共同好友、共同爱好之类的功能；
- **简单消息队列**，除了 `Redis` 自身的发布/订阅模式，我们也可以利用 `List` 来实现一个队列机制，比如到货通知、邮件发送之类的需求，不需要高可靠，但是会带来非常大的 `DB` 压力，完全可以用 `List` 来完成异步解耦；
- **`Session` 共享**，以 `PHP` 为例，默认 `Session` 是保存在服务器的文件中，如果是集群服务，同一个用户过来可能落在不同机器上，这就会导致用户频繁登陆；采用 `Redis` 保存 `Session` 后，无论用户落在那台机器上都能够获取到对应的 `Session` 信息。

## `Redis` 命令

在安装了 `Redis`  服务的系统中，可以在终端中输入以下命令开启一个 `Redis` 客户端以连接到本地的 `Redis` 服务：

~~~bash
$ redis-cli
~~~

也可以连接远程的 `Redis` 服务：

~~~bash
$ redis-cli -h host -p port -a password
~~~

开启 `redis-cli` 后，可以执行 `PING` 命令检测 `Redis` 服务是否启动。

以下实例演示了如何连接到主机为 `127.0.0.1`，端口为 `6379` ，密码为 `mypass` 的 `redis` 服务上。

使用 `SELECT` 命令指定索引可以切换为指定的数据库，默认是 `0`：

~~~redis
> SELECT 1
"OK"
~~~

以 `COMMAND KEY_NAME` 形式的 `Redis` 命令操作数据。

[Commands | Redis](https://redis.io/commands/)

[redis 命令手册](https://redis.com.cn/commands.html)

### `Key` 相关命令

当存储了多个 `key` 后，可以用 `KEYS` 命令来查询有哪些 `key`：

> 例：
>
> ~~~redis
> > SET mykey1-1 1-1
> "OK"
> 127.0.0.1:6379> SET mykey1-2 1-2
> "OK"
> > SET mykey2-1 2-1
> "OK"
> > SET mykey2-2 2-2
> "OK"
> > keys 'mykey1*'
> 1) "mykey1-1"
> 2) "mykey1-2"
> > keys 'mykey2*'
> 1) "mykey2-1"
> 2) "mykey2-2"
> > keys '*'
> 1) "mykey2-1"
> 2) "mykey1-1"
> 3) "mykey2-2"
> 4) "mykey1-2"
> ~~~

设置过期时间

> 使用 `EXPIRE` 命令可以为指定的 `key` 设置过期时间，单位为秒，到期后会自动删除：
>
> ~~~redis
> > EXPIRE mykey1 30
> (integer) 1
> ~~~
>
> 使用 `TTL` 命令（意为 `Time To Live`）可以以秒为单位返回给定 `key` 的剩余生存时间：
>
> ~~~redis
> > TTL mykey1
> (integer) 15
> ~~~
>
> - `TTL` 命令的返回值有以下几种情况：
>   - 当 `key` 不存在时，返回 `-2`。
>   - 当 `key` 存在但没有设置剩余生存时间时，返回 `-1`。
>   - 否则，以秒为单位，返回 `key` 的剩余生存时间。

### `String` 类型命令

`String` 是 `Redis` 中最基础的数据类型。

`SET`、`GET` 命令用于设置、获取 `kay` 的 `value`。默认的 `Value` 是 `String` 类型：

> 例：
>
> ~~~redis
> 127.0.0.1:6379> SET mykey redis.com.cn  
> OK   
> 127.0.0.1:6379> GET mykey   
> "redis.com.cn"  
> ~~~

`INCR`、`INCRBY`、`INCRBYFLOAT` 、`DECR`、`DECRBY` 用于改变 `key` 中存储的数字：

> | 命令          | 描述                                                    |
> | ------------- | ------------------------------------------------------- |
> | `INCR`        | 将 `key` 中储存的数字值增一                             |
> | `INCRBY`      | 将 `key` 所储存的值加上给定的增量值 ( `increment` )     |
> | `INCRBYFLOAT` | 将 `key` 所储存的值加上给定的浮点增量值 ( `increment` ) |
> | `DECR`        | 将 `key` 中储存的数字值减一                             |
> | `DECRBY`      | 将 `key` 所储存的值减去给定的减量值 ( `decrement` )     |
>
> 例：
>
> ~~~redis
> > SET mynum 666
> OK
> > INCR mynum
> (integer) 667
> > GET mynum
> "667"
> ~~~

### `List` 类型命令

`List` 类型是一个字符串列表，其中的元素都是字符串，其中的元素按顺序排序，

`LPUSH`、`RPUSH` 命令分别用于将元素添加到列表的左边、右边：

> 例：
>
> ~~~redis
> > LPUSH list1 1
> (integer) 1
> 
> > LPUSH list1 2 
> (integer) 2
> 
> > RPUSH list1 3
> (integer) 3

`LPOP`、`RPOP` 分别用于从列表的左边、右边移除并获取一个元素：

> 例：
>
> ~~~redis
> > LPOP list1
> "2"
> 
> > RPOP list1
> "3"

`LRANGE` 命令用于从列表中获取指定范围的数据：

> 例：
>
> ~~~redis
> > LPUSH list1 1
> (integer) 1
> 
> > LPUSH list1 2
> (integer) 2
> 
> > RPUSH list1 3
> (integer) 3
> 
> > LRANGE list1 0 -1
> 1) "2"
> 2) "1"
> 3) "3"
> ~~~
>
> 其中结尾下标为 `-1` 则表示截取到最后一个元素。`LRANGE list1 0 -1` 表示查询 `list1` 中的全部元素。

### `SET` 类型命令

`Set` 是无序且不重复的字符串集合。

`SADD` 命令用于向 `Set` 添加成员，`SMEMBERS` 命令用于获取 `Set` 中的所有成员：

> 例：
>
> ~~~redis
> > SADD set1 1
> (integer) 1
> 
> > SADD set1 1
> (integer) 0
> 
> > SADD set1 2
> (integer) 1
> 
> > SMEMBERS set1
> 1) "1"
> 2) "2"

`SISMEMBER` 命令用于判断一个字符串是否存在于 `Set` 中：

> 例：
>
> ~~~redis
> > SISMEMBER set1 1
> (integer) 1
> 
> > SISMEMBER set1 3
> (integer) 0
> ~~~

### `Zset` 类型命令

`Zset` 即 `Sorted Set`，代表有序集合。常用于排行榜等场景。

`ZADD` 命令用于向 `Zset` 添加或更新成员，需要为成员指定一个 `score`：

> 例：
>
> ~~~redis
> > ZADD zset1 5 a
> (integer) 1
> 
> > ZADD zset1 3 b
> (integer) 1
> 
> > ZADD zset1 5 c
> (integer) 1
> 
> > ZADD zset1 6 d
> (integer) 1
> 
> > ZADD zset1 4 e
> (integer) 1
> ~~~
>
> `Zset` 中会通过成员的 `score` 对成员进行排序并安排索引。

`ZRANGE` 命令可以获取 `Zset` 中指定索引区间内的成员：

> 例：
>
> ~~~redis
> > ZRANGE zset1 0 2
> 1) "b"
> 2) "e"
> 3) "a"
> 
> > ZRANGE zset1 -3 -1
> 1) "a"
> 2) "c"
> 3) "d"
> ~~~

### `Hash` 类型命令

`Hash` 是键值对的集合，表示字符串字段和字符串值之间的映射，适合用于表示对象。

`HSET` 命令用于设置 `Hash` 中的字段和值，`HGET` 命令用于获取 `Hash` 中指定字段的值：

> 例：
>
> ~~~redis
> > HSET hash1 key1 1
> (integer) 1
> 
> > HSET hash1 key2 2
> (integer) 1
> 
> > HGET hash1 key1
> "1"
> 
> > HGET hash1 key2
> "2"
> ~~~

### `Geo` 类型命令

`Geo` 中存储的是地理位置的经纬度信息。

`Geo` 实际上是基于 `Zset` 存储的， 它会将经度和维度信息转换成一个 `Geohash` 值，将其作为 `Zset` 的 `score` 存储。

`GEOADD` 命令用于将指定的地理空间信息（维度、经度、名称）添加到指定的 `key` 中：

> 例：
>
> ~~~redis
> > GEOADD loc 12.423454 31.556322 a
> (integer) 1
> 
> > GEOADD loc 21.234522 32.523562 b
> (integer) 1
> ~~~

`GEOPOS` 命令用于从指定 `key` 中返回给定元素的经纬度：

> 例：
>
> ~~~redis
> > GEOPOS loc a b
> 1) 1) "12.42345303297042847"
>    2) "31.55632157653229086"
> 2) 1) "21.23452037572860718"
>    2) "32.52356103205139704"
> ~~~

`GEODIST` 命令用于返回指定 `key` 中两个给定位置之间的距离：

> 例：
>
> ~~~redis
> > GEODIST loc a b
> "837430.8499"
> ~~~

`GEORADIUS` 命令用于以给定的经纬度为中心，找出某一半径内的元素：

> 例：
>
> ~~~redis
> > GEORADIUS loc 12 30 200 KM 
> 1) "a"
> ~~~

