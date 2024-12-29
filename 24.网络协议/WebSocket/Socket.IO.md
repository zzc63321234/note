## 简介

`Socket.IO` 可以在客户端和服务器之间实现**低延迟**、**双向**和**基于事件**的通信。

`Socket.IO` 连接可以通过不同的底层传输建立：

- `HTTP` 长轮询
- `WebSocket`
- `WebTransport`

`socket.io` 的 `npm` 包中包含了 `dist`（服务端）和 `client-dist`（客户端），另外也可以单独安装 `socket.io-client` 包。

## 基本使用

### 连接

服务端以 `express` 为例：

~~~javascript
const express = require('express')
const { createServer } = require('node:http')
const { join } = require('node:path')
// 引入 Socket.IO 的 Server
const { Server } = require('socket.io')

const app = express()
const server = createServer(app)
// 开启 Socket.IO 服务
const io = new Server(server)

app.get('/', (req, res) => {
    res.sendFile(join(__dirname, 'index.html'))
})

// 监听 Socket.IO 服务
io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('disconnect', () => {
        console.log('user disconnected');
    });
});

server.listen(3000, () => {
    console.log('server running at http://localhost:3000');
})
~~~

客户端：

~~~html
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>Socket.IO chat</title>
    <style>
        body {
            margin: 0;
            padding-bottom: 3rem;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }

        #form {
            background: rgba(0, 0, 0, 0.15);
            padding: 0.25rem;
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            display: flex;
            height: 3rem;
            box-sizing: border-box;
            backdrop-filter: blur(10px);
        }

        #input {
            border: none;
            padding: 0 1rem;
            flex-grow: 1;
            border-radius: 2rem;
            margin: 0.25rem;
        }

        #input:focus {
            outline: none;
        }

        #form>button {
            background: #333;
            border: none;
            padding: 0 1rem;
            margin: 0.25rem;
            border-radius: 3px;
            outline: none;
            color: #fff;
        }

        #messages {
            list-style-type: none;
            margin: 0;
            padding: 0;
        }

        #messages>li {
            padding: 0.5rem 1rem;
        }

        #messages>li:nth-child(odd) {
            background: #efefef;
        }
    </style>
</head>

<body>
</body>

<script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
<script>
    const socket = io();
</script>

</html>
~~~

开启服务后，浏览器访问 `localhost:3000`，控制台将打印 `'a user connected'`，如果刷新页面，将依次打印 `'user disconnected'` 和 `'a user connected'`。

### 触发事件

通过 `socket.emit()` 触发事件，通过 `socket.on()` 处理事件。

客户端示例：

~~~html
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>Socket.IO chat</title>
    <style>
        body {
            margin: 0;
            padding-bottom: 3rem;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }

        #form {
            background: rgba(0, 0, 0, 0.15);
            padding: 0.25rem;
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            display: flex;
            height: 3rem;
            box-sizing: border-box;
            backdrop-filter: blur(10px);
        }

        #input {
            border: none;
            padding: 0 1rem;
            flex-grow: 1;
            border-radius: 2rem;
            margin: 0.25rem;
        }

        #input:focus {
            outline: none;
        }

        #form>button {
            background: #333;
            border: none;
            padding: 0 1rem;
            margin: 0.25rem;
            border-radius: 3px;
            outline: none;
            color: #fff;
        }
    </style>
</head>

<body>
    <form id="form" action="">
        <input id="input" autocomplete="off" /><button>Send</button>
    </form>
</body>

<script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
<script>
    const socket = io();

    const form = document.getElementById('form');
    const input = document.getElementById('input');

    form.addEventListener('submit', (e) => {
        e.preventDefault();
        if (input.value) {
            socket.emit('chat message', input.value);
            input.value = '';
        }
    });
</script>

</html>
~~~

服务端示例：

~~~javascript
const express = require('express')
const { createServer } = require('node:http')
const { join } = require('node:path')
// 引入 Socket.IO 的 Server
const { Server } = require('socket.io')

const app = express()
const server = createServer(app)
// 开启 Socket.IO 服务
const io = new Server(server)

app.get('/', (req, res) => {
    res.sendFile(join(__dirname, 'index.html'))
})

// 监听 Socket.IO 服务
io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('disconnect', () => {
        console.log('user disconnected');
    });
    // 监听 socket 发出的事件
    socket.on('chat message', (msg) => {
        console.log('message: ' + msg);
    });
});

server.listen(3000, () => {
    console.log('server running at http://localhost:3000');
})
~~~

### 广播

客户端示例：

~~~html
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>Socket.IO chat</title>
    <style>
        body {
            margin: 0;
            padding-bottom: 3rem;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }

        #form {
            background: rgba(0, 0, 0, 0.15);
            padding: 0.25rem;
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            display: flex;
            height: 3rem;
            box-sizing: border-box;
            backdrop-filter: blur(10px);
        }

        #input {
            border: none;
            padding: 0 1rem;
            flex-grow: 1;
            border-radius: 2rem;
            margin: 0.25rem;
        }

        #input:focus {
            outline: none;
        }

        #form>button {
            background: #333;
            border: none;
            padding: 0 1rem;
            margin: 0.25rem;
            border-radius: 3px;
            outline: none;
            color: #fff;
        }

        #messages {
            list-style-type: none;
            margin: 0;
            padding: 0;
        }

        #messages>li {
            padding: 0.5rem 1rem;
        }

        #messages>li:nth-child(odd) {
            background: #efefef;
        }
    </style>
</head>

<body>
    <ul id="messages"></ul>
    <form id="form" action="">
        <input id="input" autocomplete="off" /><button>Send</button>
    </form>
</body>

<script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
<script>
    const socket = io();

    const form = document.getElementById('form');
    const input = document.getElementById('input');
    const messages = document.getElementById('messages');

    form.addEventListener('submit', (e) => {
        e.preventDefault();
        if (input.value) {
            socket.emit('chat message', input.value);
            input.value = '';
        }
    });

    // 接收广播消息并展示
    socket.on('chat message', (msg) => {
        const item = document.createElement('li');
        item.textContent = msg;
        messages.appendChild(item);
        window.scrollTo(0, document.body.scrollHeight);
    })
</script>

</html>
~~~

服务端示例：

~~~javascript
const express = require('express');
const { createServer } = require('node:http');
const { join } = require('node:path');
const { Server } = require('socket.io');

const app = express();
const server = createServer(app);
const io = new Server(server);

app.get('/', (req, res) => {
    res.sendFile(join(__dirname, 'index.html'));
});

io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('disconnect', () => {
        console.log('user disconnected');
    });
    // 将 socket 发来的消息广播给所有客户端（包括发送者）
    socket.on('chat message', (msg) => {
        // 如果这里使用 socket.broadcast.emit('chat message', msg); 那么将不会广播给发送者本身
        io.emit('chat message', msg);
    });
});

server.listen(3000, () => {
    console.log('server running at http://localhost:3000');
});
~~~

### 房间

连接到 `io` 中的客户端 `socket` 可以加入或离开任意 `room`，可以通过 `room` 向加入它的客户端 `socket` 广播事件，例：

~~~javascript
io.on('connection', (socket) => {
  // join the room named 'some room'
  socket.join('some room');
  
  // broadcast to all connected clients in the room
  io.to('some room').emit('hello', 'world');

  // broadcast to all connected clients except those in the room
  io.except('some room').emit('hello', 'world');

  // leave the room
  socket.leave('some room');
});
~~~

## API

[教程 - API 概述 | Socket.IO 中文网 (nodejs.cn)](https://socket.nodejs.cn/docs/v4/tutorial/api-overview)

[服务器 API | Socket.IO 中文网 (nodejs.cn)](https://socket.nodejs.cn/docs/v4/server-api/)

[客户端 API | Socket.IO 中文网 (nodejs.cn)](https://socket.nodejs.cn/docs/v4/client-api/)
