# Stream（数据流）
当内存中无法一次装下需要处理的数据时，或者一边读取一边处理更加高效时，我们就需要用到数据流。NodeJS中通过各种Stream来提供对数据流的操作。

#### createReadStream(src)
``` js
var fs = require('fs');

// 打开一个流:
var rs = fs.createReadStream('sample.txt', 'utf-8');

// data事件表示流的数据已经可以读取了
rs.on('data',function (chunk) {
  console.log('DATA:')
  console.log(chunk);
});

// end事件表示这个流已经到末尾了，没有数据可以读取了
rs.on('end',function () {
  console.log('END');
});

rs.on('error',function (err) {
  console.log('ERROR: ' + err);
});
```

#### createWriteStream(dst)
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

如果写入速度跟不上读取速度的话，只写数据流内部的缓存会爆仓。我们可以根据.write方法的返回值来判断传入的数据是写入目标了，还是临时放在了缓存了，并根据drain事件来判断什么时候只写数据流已经将缓存中的数据写入目标，可以传入下一个待写数据了。

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