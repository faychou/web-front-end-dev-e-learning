# 内置模块-path
用于处理文件路径和目录路径。

#### `path.normalize(url)`

将传入的路径转换为标准路径。除了解析路径中的 `.` 与 `..` 外，还能去掉多余的 `/`。如果有程序需要使用路径作为某些数据的索引，但又允许用户随意输入路径时，就需要使用该方法保证路径的唯一性。

``` js
const path = require('path')
path.normalize('/foo/bar//baz/asdf/quux/..')
// '/foo/bar/baz/asdf'
```

> 标准化之后的路径里的斜杠在Windows系统下是\，而在Linux系统下是/。如果想保证任何系统下都使用/作为路径分隔符的话，需要用.replace(/\\/g, '/')再替换一下标准路径。

#### `path.join(path1, path2,...)`

将传入的多个路径拼接为标准路径，路径最后不会带目录分隔符。

``` js
path.join('/src', 'modal', './index.js') // /src/modal/index.js

path.join('/foo', 'bar', 'baz/asdf', 'quux', '..') //  '/foo/bar/baz/asdf'
```

#### `path.resolve(path1, path2..)`

将传入的路径解析为绝对路径。

``` js
// 1、给定的路径的序列是 "从右往左" 被处理的，后面每个 path 被依次解析，直到构造完成一个绝对路径。
path.resolve('/foo/bar', './baz')
// '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/')
// '/tmp/file'

path.resolve('foo', '/baz', 'bar');
// '/home/user/baz/bar'

// 2、如果处理完全部给定的 path 片段后还未生成一个绝对路径，则当前工作目录（绝对路径）会被用上。
path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif')
// 则返回 '/home/user/wwwroot/static_files/gif/image.gif'

// 3、生成的路径是规范化后的，且末尾的斜杠会被删除，除非路径被解析为根目录。
path.resolve('/foo', 'bar/', 'baz/');
// '/foo/bar/baz' 
```

#### `path.relative(from, to)`

返回某个路径下相对于另一个路径的相对位置串。

``` js
path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb')
// '../../impl/bbb'
```

#### `path.parse(url)`

解析路径。

``` js
path.parse('/src/modal/index.js')

/*
 { root: '/',
  dir: '/src/modal',
  base: 'index.js',
  ext: '.js',
  name: 'index' }
*/
```

#### `path.format(object)`

序列化路径。

``` js
path.format({
  root: '/',
  dir: '/src/modal',
  base: 'index.js',
  ext: '.js',
  name: 'index'
}) // /src/modal/index.js
```

#### `path.dirname(url)`

返回路径所在的文件夹名称。

``` js
path.dirname('/foo/bar/baz/asdf/quux')
// '/foo/bar/baz/asdf'
```

#### `path.basename(url, [ext])`

返回路径中的最后一部分，以 `/` 分割。返回结果可排除 `[ext]` 后缀字符串。

``` js
path.basename('/foo/bar/baz/asdf/quux.html')
// 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html')
```

#### `path.extname(url)`

返回路径 `url` 的扩展名。

``` js
path.extname('index.html')   // '.html'

path.extname('index.')  // '.'

path.extname('index')  // ''
```

#### `path.isAbsolute(url)`

检查是否是绝对路径。

``` js
path.isAbsolute('/src/index.js') // true
path.isAbsolute('.') // false

```

