# querystring
用于实现URL参数字符串与参数对象的互相转换。

### querystring.parse
``` js
querystring.parse('foo=bar&baz=qux&baz=quux&corge');
 
 // =>
{ foo: 'bar', baz: ['qux', 'quux'], corge: '' }

```

### querystring.stringify
``` js
querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' });
 
// =>
'foo=bar&baz=qux&baz=quux&corge='
```

``` js
const http = require('http');
const url = require('url');
const qs = require('querystring');

// 生成一段返回值
const genResponse = userId => ({
  success: true,
  data: {
    userId,
    name: 'Nodejs 入门示例',
    description: '这是返回信息的描述内容',
    date: new Date()
  }
});

// http服务
http.createServer((req, res) => {
  res.setHeader('Content-Type', 'application/json;charset=utf-8');
  const reqUrl = url.parse(req.url);
  // 判断接口路径是否是约定好的
  if (reqUrl.pathname === '/api/search/data') {
    // 获取链接上传来的userId参数
    const uid = qs.parse(reqUrl.query).userId;
    // 生成返回值
    const result = JSON.stringify(genResponse(uid));
    res.end(result);
  } else {
    res.writeHead(404);
    res.end('NotFund');
  }
}).listen(8000, ()=> {
  console.log('listen on 8000!');
})
```