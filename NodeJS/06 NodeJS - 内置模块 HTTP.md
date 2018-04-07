# 内置模块 HTTP
Node.js 标准库提供了http模块，其中封装了一个高效的 HTTP 服务器和一个简易的 HTTP 客户端。

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

### response.writeHead(statusCode[, headers])
向请求的客户端发送响应头。statusCode 是 HTTP 状态码，如 200（请求成功）、404（未找到）等。headers 是一个类似关联数组的对象，表示响应头的每个属性，如 Content-Type。

常见的 Content-Type 类型有：

* 'text/html'
* 'text/plain'

``` js
var http = require('http');
var port = process.env.PORT || 1337;

http.createServer(function(req, res) {
  // 获得 HTTP 请求的 method 和 url:
  console.log(req.method + ': ' + req.url);
  
  // 将 HTTP 响应 200 写入 response, 同时设置 Content-Type: text/html:
  res.writeHead(200, { 'Content-Type': 'text/html' });
  res.end(new Date() + '\n');
}).listen(port);

console.log('Server running on port %s', port);
```

### response.write(data[, encoding])
向请求的客户端发送响应内容。 data 是一个  Buffer 或字符串，表示要发送的内容。如果  data 是字符串，那么需要指定
encoding 来说明它的编码方式，默认是 utf-8 。在 response.end 调用之前，response.write  可以被多次调用。

### response.end([data], [encoding])
结束响应，告知客户端所有发送已经完成。当所有要返回的内容发送完毕的时候，该函数 必须 被调用一次。它接受两个可选参数，意义和 response.write 相同。如果不调用该函数，客户端将永远处于等待状态。

### listen()
告知服务器去监听的端口号。

* 参数一：监听端口号；
* 参数二：可选，一个回调函数，服务器一启动，这个回调函数就会被执行。

> 注意：服务器启动后，如果修改了文件，服务器并不会自动重启，需要我们手动重新运行服务器，如果要实现自动重启可以使用第三方模块 supervisor。

### 获取 GET 请求
``` js
var http = require("http");
var url = require("url");
var server = http.createServer();

server.on("request", function (req, res) {
  if (req.url == "/favicon.ico") {
    return;
  }

  var m = url.parse(req.url, true);
  console.log(m)
  res.writeHead(200, {'Content-type': 'text/html;charset = utf8'});
  res.end();
})
server.listen(3000);
console.log("The server begin");
```

### 获取 POST 请求
POST 请求的内容全部都在请求体中，原因是等待请求体传输可能是一件耗时的工作，譬如上传文件。而很多时候我们可能并不需要理会请求体的内容，且恶意的 POST
请求会大大消耗服务器的资源。所以 Node.js 默认是不会解析请求体的，因此我们需要手写一个，具体实现方法如下：

``` js
var http = require('http');
var querystring = require('querystring');
var util = require('util');

http.createServer(function(req, res) {
  var post = '';
  req.on('data', function(chunk) {
    post += chunk;
  });

  req.on('end', function() {
    post = querystring.parse(post);
    res.end(util.inspect(post));
  });
}).listen(3000);
```

### supervisor
第三方模块，用来监听文件变化，自动重起服务器。

```
# 安装
npm install -g supervisor

# 使用 supervisor 命令启动 app.js 
supervisor app.js
```