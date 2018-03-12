# 内置模块 url
## url API
### url.parse(urlStr, QueryString, protocol)
将一个URL字符串转换为URL对象。

* urlStr：url字符串；
* QueryString：布尔值；
* protocol：布尔值

``` js
url.parse('http://www.faychou.com:8080/p/a/t/h?query=string#hash');

// 输出结果如下：
{ 
  protocol: 'http:',
  port: '8080',
  hostname: 'faychou.com',
  hash: '#hash',
  search: '?query=string',
  pathname: '/p/a/t/h',
}
```

### url.format
允许将一个URL对象转换为URL字符串。

``` js
url.format({
  protocol: 'http:',
  host: 'www.faychou.com',
  pathname: '/p/a/t/h',
  search: '?query=string'
});

// 输出结果如下：
'http://www.faychou.com/p/a/t/h?query=string'
```

### url.resolve
用于拼接URL。

``` js
url.resolve('/one/two/three', 'four')  // '/one/two/four'
	
url.resolve('http://example.com/', '/one')  // 'http://example.com/one'
	
url.resolve('http://example.com/one', '/two')  // 'http://example.com/two'
```

## NodeJS 路由实现
一个服务器会存储大量的文件。当浏览器发送请求时，会告知服务器他们需要的文件，而服务器会将相应的文件返回给客户端。这就叫做路由。

在 NodeJS 中，我们需要手动定义自己的路由。

``` js
//app.js
const http = require('http'),
  url = require('url');
 
makeServer = function (request,response) {
  let path = url.parse(request.url).pathname;
  console.log(path);
  
  if(path === '/'){
    response.writeHead(200,{'Content-Type':'text/plain'});
    response.write('Hello world');
  } else if(path === '/about') {
    response.writeHead(200,{'Content-Type':'text/plain'});
    response.write('About page');
  } else if(path === '/blog') {
    response.writeHead(200,{'Content-Type':'text/plain'});
    response.write('Blog page');
  } else {
    response.writeHead(404,{'Content-Type':'text/plain'});
    response.write('Error page');
  }
  response.end();
};

server = http.createServer(makeServer);
server.listen(3000,()=> {
  console.log('Node server created at port 3000');
});
```
