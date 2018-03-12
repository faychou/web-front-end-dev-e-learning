# Buffer
创建 Buffer：

``` js
var a = new Buffer(10);
a.length  //10

var b = new Buffer([ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]);
b[0];  // 0x68;
```

Buffer 与 字符串 之间的转换：

``` js
var bin = new Buffer('hello', 'utf-8');  // <Buffer 68 65 6c 6c 6f>
var str = bin.toString('utf-8');  //  "hello"
```

### Buffer.concat(list, totalLength)
Buffer合并

### buf.copy(target, tStart, sStart, sEnd)
Buffer拷贝

### buf.slice(start, end)
Buffer裁剪

### Buffer.isEncoding(encoding)
测试给定的编码字符串

### Buffer.isBuffer(obj)
检测是否Buffer类型