# fs
是文件操作的封装，它提供了文件的读取、写入、更名、删除、遍历目录、链接等 POSIX 文件系统操作，且所有的方法都有异步和同步的形式。

### fs.open(path, flags[,mode], callback(err,fd))
异步模式下打开文件。

说明：

* path - 文件的路径。
* flags - 文件打开的行为。
* mode - 设置文件权限，默认权限为 0666(可读，可写)。
* callback - 回调函数。

``` js
var fs = require("fs");

// 异步打开文件
console.log("准备打开文件！");

fs.open('input.txt', 'r+', function(err,fd) {
  if (err) {
    return console.error(err);
  }
  console.log("文件打开成功！");     
});
```

flags 可以是以下值：

```
'r' - 以读取模式打开文件。如果文件不存在则发生异常。

'r+' - 以读写模式打开文件。如果文件不存在则发生异常。

'rs+' - 以同步读写模式打开文件。命令操作系统绕过本地文件系统缓存。

（这对 NFS 挂载模式下打开文件很有用，因为它可以让你跳过潜在的旧本地缓存。 它对 I/O 的性能有明显的影响，所以除非需要，否则不要使用此标志。

注意，这不会使 fs.open() 进入同步阻塞调用。 如果那是你想要的，则应该使用 fs.openSync()。）

'w' - 以写入模式打开文件。文件会被创建（如果文件不存在）或截断（如果文件存在）。

'wx' - 类似 'w'，但如果 path 存在，则失败。

'w+' - 以读写模式打开文件。文件会被创建（如果文件不存在）或截断（如果文件存在）。

'wx+' - 类似 'w+'，但如果 path 存在，则失败。

'a' - 以追加模式打开文件。如果文件不存在，则会被创建。

'ax' - 类似于 'a'，但如果 path 存在，则失败。

'a+' - 以读取和追加模式打开文件。如果文件不存在，则会被创建。

'ax+' - 类似于 'a+'，但如果 path 存在，则失败。
```

### fs.stat(path, callback(err, stats));
属于异步模式，返回一个Stat对象，包含文件或目录的详细信息。

说明：

* path - 文件路径
* callback - 回调函数

``` js
var fs = require('fs');

fs.stat('ceshi.txt', function (err,stats) {
  if (err) {
    console.log(err);
  } else {
    // 是否是文件:
    console.log('isFile: ' + stat.isFile());
    
    // 是否是目录:
    console.log('isDirectory: ' + stat.isDirectory());
        
    if (stat.isFile()) {
      // 文件大小:
      console.log('size: ' + stat.size);
      
      // 创建时间, Date对象:
      console.log('birth time: ' + stat.birthtime);
      
      // 修改时间, Date对象:
      console.log('modified time: ' + stat.mtime);
    }
  } 
});
```

### fs.readFile(pathname,[options], callback (err, data))
异步模式读取文件。第一个必选参数 pathname，表示要读取的文件名。第二个参数 options 是可选的，表示文件的字符编码。callback 是回调函数，用于接收文件的内容。如果不指定 options，则 callback 就是第二个参数。如果指定了 options， data 是一个解析后的字符串，否则 data 将会是以 Buffer 形式表示的二进制数据。

``` js
var fs = require('fs');

fs.readFile('sample.txt', 'utf-8', function (err, data) {
  if (err) {
    console.log(err);
  } else {
    console.log(data);
  }
});
```

``` js
// 读取一个图片文件:
var fs = require('fs');

fs.readFile('sample.png', function (err,data) {
  if (err) {
    console.log(err);
  } else {
    console.log(data);
    onsole.log(data.length + ' bytes');
  }
});
```

### fs.readFileSync
同步模式读取文件，读取到的文件内容会以函数返回值的形式返回。

``` js
var fs = require('fs');
var data = fs.readFileSync('sample.txt', 'utf-8');
console.log(data);
```

如果同步读取文件发生错误，则需要用 try catch 捕获该错误并处理异常：

``` js
try {
    var data = fs.readFileSync('sample.txt', 'utf-8');
    console.log(data);
} catch (err) {
    // 出错了
}
```

### fs.writeFile
将数据异步写入文件。

``` js
var fs = require('fs');
var data = 'Hello, Node.js';

fs.writeFile('output.txt',data,function (err) {
  if (err) {
    console.log(err);
  } else {
    console.log('ok.');
  }
});
```

### fs.writeFileSync
将数据同步写入文件

``` js
var fs = require('fs');
var data = 'Hello, Node.js';
fs.writeFileSync('output.txt',data);
```

### fs.read(fd, buffer, offset, length, position, callback)
相比  fs.readFile 提供了更底层的接口。从 fd 指定的文件中读取数据。buffer 是数据将被写入到的 buffer。offset 是 buffer 中开始写入的偏移量。length 是一个整数，指定要读取的字节数。position 指定从文件中开始读取的位置。如果 position 为 null，则数据从当前文件读取位置开始读取，且文件读取位置会被更新。如果 position 为一个整数，则文件读取位置保持不变。回调有三个参数 (err, bytesRead, buffer)。

``` js
var fs = require('fs');
fs.open('content.txt', 'r', function(err, fd) {
if (err) {
console.error(err);
return;
}
var buf = new Buffer(8);
fs.read(fd, buf, 0, 8, null, function(err, bytesRead, buffer) {
if (err) {
console.error(err);
return;
}
console.log('bytesRead: ' + bytesRead);
console.log(buffer);
})
});

// bytesRead: 8
// <Buffer 54 65 78 74 20 e6 96 87>
```

### fs.close()

### fs.ftruncate()

### fs.unlink()

### fs.mkdir()

### fs.readdir()

### fs.rmdir()
