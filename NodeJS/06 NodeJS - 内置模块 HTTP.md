# 内置模块 HTTP
## 搭建 web 服务器：
1、新建一个文件夹，并在终端里进入该文件；

2、执行 npm init 命令，创建 package.json 文件；

3、创建一个名为 app.js 的文件，代码为：

``` js
var http = require('http'); 
http.createServer(function(request, response) {
  response.writeHead(200, {'Content-Type': 'text/html'});
  response.write('<h1>Node.js</h1>');
  response.end('<p>Hello World</p>'); 
}).listen(3000);

console.log("HTTP server is listening at port 3000.");

//也可以这样写
function onRequest (request, response) {
  response.writeHead(200, {'Content-Type': 'text/html'});
  response.write('<h1>Node.js</h1>');
  response.end('<p>Hello World</p>'); 
}

http.createServer(onRequest).listen(3000);

//还可以这样写
var http = require('http'),
  server = http.createServer();

server.on('request',(request,response)=>{
  response.writeHead(200,{'Content-Type':'text/plain'});
  response.write('Hello world');
  response.end();
});

server.listen(3000,()=>{
  console.log('Node server created at port 3000');
});
```

request 代表接收到的请求，response 代表响应的数据。

4、终端运行： 

	node app.js

5、打开浏览器访问 http://127.0.0.1:3000 

## http
该模块提供了处理 htpp 操作的接口，调用 createServer() 方法来创建一个服务器，该方法需要一个回调函数作为参数，这个回调函数有2个参数对象，request 代表接收到的请求，response 代表响应的数据。

### response.writeHead()
设置返回报文头部字段，比如状态码和内容类型。

常见的 Content-Type 类型有：

* 'text/html'
* 'text/plain'

``` js
var http = require('http');
var port = process.env.PORT || 1337;

http.createServer(function(req, res) {
  // 获得HTTP请求的method和url:
  console.log(req.method + ': ' + req.url);
  
  // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
  res.writeHead(200, { 'Content-Type': 'text/html' });
  res.end(new Date() + '\n');
}).listen(port);

console.log('Server running on port %s', port);
```

### response.write()
是对 web 页面进行写入操作。

### response.end()
最后使用该方法来结束这个响应。

### listen()
告知服务器去监听的端口号。

* 参数一：监听端口号；
* 参数二：可选，一个回调函数，服务器一启动，这个回调函数就会被执行。

> 注意：服务器启动后，如果修改了文件，服务器并不会自动重启，需要我们手动重新运行服务器，如果要实现自动重启可以使用第三方模块 supervisor。

### supervisor
第三方模块，用来监听文件变化，自动重起服务器。

```
# 安装
npm install -g supervisor

# 使用 supervisor 命令启动 app.js 
supervisor app.js
```