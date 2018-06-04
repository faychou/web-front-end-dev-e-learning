# Socket.IO
Socket.IO 实现了基于事件的实时双向通信。

## 安装
``` bash
npm install --save socket.io
```

## 引入
因为 Socket.IO 由两部分组成：

* 一个服务端用于挂载到 Node.JS HTTP 服务器： socket.io；
* 一个加载到浏览器中的客户端： socket.io-client。

所以在引入 Socket.IO 的时候要分为两步来引入。

### 服务器端
``` js
// index.js
const express = require('express');

const app = express();
const server = require('http').Server(app);

// 传入 http （HTTP 服务器） 对象初始化一个 socket.io 实例
const io = require('socket.io')(server);

app.get('/',(req,res) => {
  // res.send('<h1>Hello world</h1>');
  res.sendFile(__dirname + '/index.html');
});

// 监听 connection 事件来接收 sockets
io.on('connection',(socket) => {
  console.log('socket 连接成功');
});

server.listen(3333,() => {
  console.log('listening on http://127.0.0.1:3333');
});
```

### 客户端
与 `index.js` 同级目录下新建一个 `client.html` ：

``` html
<!doctype html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Socket.IO</title>
  </head>
  <body>
    
    <!-- 开发环境下， socket.io 会自动提供客户端 -->
    <script src="/socket.io/socket.io.js"></script>
    <script>
      var socket = io();
    </script>
  </body>
</html>
```

> 注意：在调用 `io()` 时没有指定任何 URL，因为它默认将尝试连接到提供当前页面的主机。

## API
### 触发事件
``` js
// 客户端：client.html
const socket = io();

// 发送消息
socket.emit('message-name','message-data');
```

``` js
// 服务端：index.js
io.on('connection',(socket) => {
  //接受来自客户端消息
  socket.on('message-name', (msg) => {
    console.log('message: ' + msg);
  });
});
```

### 广播
将事件发送给每个用户，在服务器端设置：

``` js
// index.js
io.emit('some event', { for: 'everyone' });
```

要将消息发给除特定 socket 外的其他用户，可以用 broadcast 标志：

``` js
// index.js
io.on('connection',(socket) => {
  socket.broadcast.emit('hi');
});
```

