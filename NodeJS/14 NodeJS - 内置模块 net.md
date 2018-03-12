# 内置模块 net
要在 NodeJS 中进行连网操作，我们需要引入 net 模块。

在 TCP 中必须有两个终端，一个终端与指定端口绑定，而另一个则需要访问这个指定端口。

### 案例
1、创建一个文件夹，命名为 node-network；

2、创建 filewatcher.js：

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


