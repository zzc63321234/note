## `redis`

`Redis` 官方提供了 `node-redis` 包，用于在 `Node.js` 中操作 `Redis`。

安装：

~~~bash
$ npm i redis
~~~

使用示例：

~~~javascript
import { createClient } from 'redis';

// 创建一个 Redis 客户端
const client = createClient({
    socket: {
        host: 'localhost',
        port: 6379
    }
});

// 监听 Redis 客户端的错误事件
client.on('error', err => console.log('Redis Client Error', err));

// 将 Redis 客户端连接到 Redis 服务器
await client.connect();

// 执行 KEYS 命令
const value = await client.keys('*');

console.log(value);

await client.disconnect();
~~~

## `ioredis`

安装：

~~~bash
$ npm i ioredis
~~~

使用示例：

~~~javascript
import Redis from "ioredis";

const redis = new Redis({
    host: 'localhost',
    port: 6379
});

const res = await redis.keys('*');

console.log(res);
~~~

