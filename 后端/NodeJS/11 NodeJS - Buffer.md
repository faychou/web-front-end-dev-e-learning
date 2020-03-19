# 内置模块-`Buffer`
用于读取或操作二进制数据流。

创建 Buffer：

``` js
const buffer = Buffer.from('Hello world')
// <Buffer 48 65 6c 6c 6f 20 77 6f 72 6c 64>
```

Buffer 与 字符串 之间的转换：

``` js
// 换为 UTF-8 格式的字符串
const buffer = Buffer.from('Hello world')
console.log(buffer.toString()) // Hello world

// 换为base64格式字符串
console.log(buffer.toString('base64')) // SGVsbG8gd29ybGQ=

// 将base64编码的字符串，转换为UTF-8编码

const buffer = Buffer.from('Hello world')
const base64Str = buffer.toString('base64')
const buf = Buffer.from(base64Str, 'base64')
console.log(buf.toString('utf8')) // Hello world
```

