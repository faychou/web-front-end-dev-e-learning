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

### 监听事件：on
语法：

``` js
socket.on(event,(data,fn) => { // data：事件中携带的数据
  //...
});
```

Socket.IO 通过 `on` 方法来监听事件，主要的内置事件分为：

服务器端事件：

* connection 连接成功之后触发，用于初始化；
* disconnect 断开连接（包括关闭浏览器，主动断开，掉线等任何断开连接的情况）；
* message 客户端通过 socket.send 来传送消息时触发此事件；
* anything 收到任何事件时触发；

相关代码如下：

``` js
// server.js
io.on('connection',(socket) => {
  socket.on('message',(message,callback) => {});

  socket.on('anything',(data) => {});

  socket.on('disconnect',() => {});
});
```

客户端事件：

* connecting 正在连接；
* connect 连接成功；
* message 同服务器端 message 事件；
* anything 同服务器端 anything 事件；
* disconnect 断开连接；
* connect_failed 连接失败；
* reconnecting 正在重连；
* reconnect 重新连接成功；
* reconnect_failed 重连失败；
* error 错误发生，并且无法被其他事件类型所处理。

> 注意：客户端 socket 发起连接时的顺序：第一次连接时，事件触发顺序为：connecting -> connect；当失去连接时，事件触发顺序为：disconnect -> reconnecting（可能进行多次）-> connecting -> reconnect -> connect 。

当然，除了内置事件外，还可以设置监听自定义事件：

``` js
// 客户端：client.js
const socket = io();

// 广播自定义事件
socket.emit('message-name','message-data');
```

``` js
// 服务端：server.js
io.on('connection',(socket) => {
  //接受来自客户端的自定义事件
  socket.on('message-name', (msg) => {
    console.log('message: ' + msg);
  });
});
```

### 监听事件：once
语法：

``` js
socket.once(event,(data,fn) => {
  //...
});
```

### 广播：emit
语法：

``` js
socket.emit(event,data,(data1,data2,...) => {
  //...
});
```

将事件发送给每个用户，在服务器端设置：

``` js
// server.js
io.emit('some event', { for: 'everyone' });
```

给指定的客户端发送消息：

``` js
io.sockets.socket(socketid).emit(‘String’, data);
```

要将消息发给除发送者之外的其他用户，可以用 broadcast 标志：

``` js
// server.js
io.on('connection',(socket) => {
  socket.broadcast.emit('hi');
});
```

## 命名空间
Socket.IO 允许为 socket 添加命名空间，也就是分配不同的 路径。这可以最小化资源数量 （TCP 连接），并为应用提供频道划分功能。

### 默认命名空间
默认的命名空间是 `/` ，Socket.IO 客户端默认连接到这个命名空间，服务端默认监听的也是这个命名空间。这个命名空间使用 `io.sockets` 或 `io` 来标识：

``` js
// 下面两行代码都将发送消息给所有连接到 `/` 命名空间的客户端
io.sockets.emit('hi', 'everyone');
io.emit('hi', 'everyone'); // 简写形式，与上一行等效
```

每个命名空间都会触发一个 connection 事件。该事件接收一个 Socket 实例作为参数。

``` js
io.on('connection', (socket) => {
  socket.on('disconnect', function(){ });
});
```

### 自定义命名空间
使用自定义命名空间，可以在服务端调用 of 函数：

``` js
// server.js
var nsp = io.of('/my-namespace');
nsp.on('connection', (socket) => {
  console.log('someone connected');
});
nsp.emit('hi', 'everyone!');
```

客户端使用如下方法连接到该命名空间：

``` js
// client.js
const socket = io('/my-namespace');
```

## 房间
对于每个命名空间，你还可以定义任意频道。

### 加入和离开房间
调用 join 可以订阅特定房间：

``` js
io.on('connection',(socket) => {
  socket.join('some room');
});
```

通过 to 或者 in （它们是等效的） 可以在频道内广播或发送消息：

``` js
io.to('some room').emit('some event');
```

调用 leave 可以离开房间，和 join 方法一样。

``` js
socket.leave('some room');
```

### 默认房间
Socket.IO 中的每个 Socket 都使用一个随机、不可预测、唯一的 Socket#id 标识符来标识。为了简便，每个 socket 自动进入一个使用自身 id 标识的房间。

``` js
io.on('connection',(socket) => {
  socket.on('say to someone',(id, msg) => {
    socket.broadcast.to(id).emit('my message', msg);
  });
});
```

### 断开连接
断开连接时， sockets 会自动 leave 所有频道，不需要你做任何处理。

## 使用外部消息源
对于一些场景，你可能需要从 Socket.IO 进程之外的的环境触发事件，以传递消息给 Socket.IO 命名空间 / 房间内的 sockets。

通过实现 Redis Adapter：

``` js
const io = require('socket.io')(3000);
const redis = require('socket.io-redis');
io.adapter(redis({ host: 'localhost', port: 6379 }));
```

你可以从任意进程 emit 消息给任意房间：

``` js
const io = require('socket.io-emitter')({ host: '127.0.0.1', port: 6379 });
setInterval(() => {
  io.emit('time', new Date());
}, 5000);
```

