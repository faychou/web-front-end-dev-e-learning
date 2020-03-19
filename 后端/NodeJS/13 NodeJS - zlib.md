# 内置模块-`zlib`
在流传输过程中，为减少传输数据加快传输速度，往往会对流进行压缩。

HTTP 流就是如此，为提高网站响应速度，会在服务端进行压缩，客户端收到数据后再进行相应的解压。

Node 中的 `zlib` 模块提供了流压缩与解压缩功能，`Zlib` 模块提供了对 `Gzip/Gunzip`、`Deflate/Inflate`、`DeflateRaw/InflateRaw` 类的绑定，这些类可以实现对可读流/可写流的压缩与解压。

#### 文件压缩

``` js
const zlib = require('zlib')
const fs = require('fs')
const gzip = zlib.createGzip()
const inp = fs.createReadStream('zlib.txt')
const out = fs.createWriteStream('zlib.txt.gz')
inp.pipe(gzip).pipe(out)
```

#### 文件解压

``` js
const zlib = require('zlib')
const fs = require('fs')
const gunzip = zlib.createGunzip()
const inp = fs.createReadStream('./un-zlib.txt.gz')
const out = fs.createWriteStream('un-zlib.txt')
inp.pipe(gunzip).pipe(out)
```

#### 服务端 `gzip` 压缩

``` js
const fs = require('fs')
const http = require('http')
const zlib = require('zlib')
const filepath = './index.html'

const server = http.createServer((req, res) => {
  const acceptEncoding = req.headers['accept-encoding'] // 请求中声明浏览器支持的解压类型
  if (acceptEncoding.includes('gzip')) {
    const gzip = zlib.createGzip()
    res.writeHead(200, {
      'Content-Encoding': 'gzip' // 服务器告诉浏览器使用了哪种压缩类型
    })
    fs.createReadStream(filepath)
      .pipe(gzip)
      .pipe(res)
  } else {
    fs.createReadStream(filepath).pipe(res)
  }
})

server.listen(4396)
```

