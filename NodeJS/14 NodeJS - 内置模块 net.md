# 内置模块 net
在 NodeJS 中用内置的 net 模块可以实现 TCP 连接。

``` js
let net = require('net');
let server = net.createServer(connection => {
	// ...
}).lieten(8080);
```

需要注意的是 TCP 是长连接，意味着它会一直保持连接直到我们手动去关闭客户端或则服务端表示要关闭连接。所以即使你每隔一段时间通过 TCP 连接向服务端发送信息，  createServer 里注册的回调函数也只会执行一次。

### 关闭连接
* 客户端手动关闭；
* `connection.end()` 通过套接字的关闭让连接中断；
* `server.close()` 让服务器关闭，自然连接也会关闭；
* `server.unref()` 当服务器所有连接都关闭后，能让服务器自主关闭。

还可以监听服务器的关闭：

``` js
server.on('close',fuction(){})
```

### 读取
通过监听 on('data') 事件来读取客户端的输入：

``` js
connection.on('data',function(){});
```

### 端口被占用解决方案
``` js
let port = 8080;
server.listen(port,'localhost',function() {
  console.log(`server is running at ${port}`);
});

server.on('error',function(err) {
  if(err.code === 'EADDRINUSE') {
    server.listen(++port);
  }
});
```

### 案例
在 TCP 中必须有两个终端，一个终端与指定端口绑定，而另一个则需要访问这个指定端口。

1、创建一个文件夹，命名为 node-network；

2、创建一个 server：

``` js
//filewatcher.js

const net = require('net'),
  fs = require('fs'),
  filename = process.argv[2];
      
server = net.createServer((connection)=> {
  console.log('Subscriber connected');
  connection.write(`watching ${filename} for changes`);
  
  let watcher = fs.watch(filename,(err,data)=> {
    connection.write(`${filename} has changed`);
  });
  
  connection.on('close',()=> {
    console.log('Subscriber disconnected');
    watcher.close();
  });
});

server.on('connection',() => {
  console.log('客户端链接');
})

server.listen(3000,()=> console.log('listening for subscribers'));
```

3、创建一个被监听的文件 subject.txt：

``` txt
Hello world, I'm gonna change !
```

4、新建一个客户端文件 client.js：

``` js
const net = require('net');
let client = net.connect({port:3000});

client.on('data',(data)=> {
  console.log(data.toString());
});
```

5、打开一个终端，运行 filename.js，后面跟着我们要监听的文件名：

``` bash
node filewatcher.js subject.txt
```

这里注意：在 filewatcher.js 文件中，有一行代码 process.argv[2]，我们知道 process 是一个全局变量，提供 NodeJS 代码运行的重要信息。argv[] 是一个参数数组，当我们获取 argv[2] 时，希望得到运行代码的第三个参数，也就是 subject.txt 。

6、再打开一个终端，运行 client.js，也就是客户端：

``` bash
node client.js
```

7、最后，修改 subject.txt 文件，然后看看客户端的命令行会不会有输出。

网络的一个主要的特征就是许多客户端都可以同时接入这个网络。打开另一个命令行窗口，输入 node client.js 来启动另一个客户端，然后再修改 subject.txt 文件。看看输出了什么？


