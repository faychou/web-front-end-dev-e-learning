# 内置对象-`querystring`
用于处理 URL 查询字符串。查询字符串指：URL 字符串指从问号 `?` 开始到锚点 `#` 结束的字符串（存在 `#`，则到`＃`结束，不存在则到 URL 字符串结束）部分。`querystring` 模块可将 URL 查询字符串解析为对象，或将对象序列化为查询字符串。



### `querystring.parse`
``` js
querystring.parse('foo=bar&baz=qux&baz=quux&corge');
 
 // => { foo: 'bar', baz: ['qux', 'quux'], corge: '' }

```

### `querystring.stringify(obj[, sep][, eq][, options])`
``` js
querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' });
 
// => 'foo=bar&baz=qux&baz=quux&corge='
```

案例：

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

#### `querystring.escape`

对查询字符串进行编码。

``` js
const str = querystring.escape(`url=github.com%2Fwebfansplz&name=null`)

console.log(str) // url%3Dgithub.com%252Fwebfansplz%26name%3Dnull
```

#### `querystring.unescape`

解码查询字符串中的参数。

``` js
const str = querystring.escape(`url=github.com%2Fwebfansplz&name=null`)

console.log(querystring.parse(querystring.unescape(str))) // { url: 'github.com/webfansplz', name: 'null' }
```

