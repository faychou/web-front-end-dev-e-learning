# 内置模块-`stream`
数据流（`stream`）是 Node 中处理流式数据的抽象接口。 `stream` 模块用于构建实现了流接口的对象。

当内存中无法一次装下需要处理的数据时，或者一边读取一边处理更加高效时，我们就需要用到数据流。Node 中通过各种 `stream` 来提供对数据流的操作。

流可以是可读的、可写的、或者可读可写的。 所有的流都是 `EventEmitter` 的实例。

Node 中有四种基本的流类型:

- Writable - 可写入数据的流（例如 `fs.createWriteStream()`）。
- Readable - 可读取数据的流（例如 `fs.createReadStream()`）。
- Duplex - 可读又可写的流（例如 `net.Socket`）。
- Transform - 在读写过程中可以修改或转换数据的 Duplex 流（例如 `zlib.createDeflate()`）。

#### `http` 中的数据流

``` js
const http = require('http')

const server = http.createServer((req, res) => {
  let body = ''
  // 如果没有设置字符编码，则会接收到 Buffer 对象。
  req.setEncoding('utf8')

  // 可读流，触发 'data' 事件
  req.on('data', chunk => {
    body += chunk
  })

  // 'end' 事件表明整个请求体已被接收
  req.on('end', () => {
    try {
      const data = JSON.parse(body)
      res.write(data)
      res.end()
    } catch (er) {
      res.statusCode = 400
      res.end(`错误: ${er.message}`)
    }
  })
})

server.listen(1337)
```

#### `createReadStream(src)`
``` js
var fs = require('fs');
var result;

// 打开一个流:
var rs = fs.createReadStream('sample.txt', 'utf-8');

// data事件表示流的数据已经可以读取了
rs.on('data',function (chunk) {
  console.log('DATA:')
  console.log(chunk);
  result += chunk;
});

// end 事件表示这个流已经到末尾了，没有数据可以读取了
rs.on('end',function () {
  console.log('END');
  console.log('result结果：', result);
});

rs.on('error',function (err) {
  console.log('ERROR: ' + err);
});
```

#### `createWriteStream(dst)`
``` js
var fs = require('fs');

var ws1 = fs.createWriteStream('output1.txt', 'utf-8');

// 要以流的形式写入文件，只需要不断调用write()方法，最后以end()结束
ws1.write('使用Stream写入文本数据...\n');
ws1.write('END.');
ws1.end();

var ws2 = fs.createWriteStream('output2.txt');
ws2.write(new Buffer('使用Stream写入二进制数据...\n', 'utf-8'));
ws2.write(new Buffer('END.', 'utf-8'));
ws2.end();
```

如果写入速度跟不上读取速度的话，只写数据流内部的缓存会爆仓。我们可以根据 `.write` 方法的返回值来判断传入的数据是写入目标了，还是临时放在了缓存了，并根据 `drain` 事件来判断什么时候只写数据流已经将缓存中的数据写入目标，可以传入下一个待写数据了。

``` js
var rs = fs.createReadStream(src);
var ws = fs.createWriteStream(dst);

rs.on('data', function (chunk) {
    if (ws.write(chunk) === false) {
        rs.pause();
    }
});

rs.on('end', function () {
    ws.end();
});

ws.on('drain', function () {
    rs.resume();
});
```