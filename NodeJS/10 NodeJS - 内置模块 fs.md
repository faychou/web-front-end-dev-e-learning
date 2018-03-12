# fs
文件系统模块，负责读写文件。

### fs.open(path, flags, mode, callback(err,fd))
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

### fs.readFile(pathname, callback (err, data))
异步模式读取文件。

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
同步模式读取文件。

``` js
var fs = require('fs');
var data = fs.readFileSync('sample.txt', 'utf-8');
console.log(data);
```

如果同步读取文件发生错误，则需要用try...catch捕获该错误：

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

### fs.read()

### fs.close()

### fs.ftruncate()

### fs.unlink()

### fs.mkdir()

### fs.readdir()

### fs.rmdir()
