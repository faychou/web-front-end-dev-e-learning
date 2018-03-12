# path
### path.normalize(url)
将传入的路径转换为标准路径。除了解析路径中的.与..外，还能去掉多余的斜杠。如果有程序需要使用路径作为某些数据的索引，但又允许用户随意输入路径时，就需要使用该方法保证路径的唯一性。

``` js
path.normalize('/foo/bar//baz/asdf/quux/..')
// '/foo/bar/baz/asdf'
```

> 标准化之后的路径里的斜杠在Windows系统下是\，而在Linux系统下是/。如果想保证任何系统下都使用/作为路径分隔符的话，需要用.replace(/\\/g, '/')再替换一下标准路径。

### path.join(path1, path2,...)
将传入的多个路径拼接为标准路径。

``` js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..')
//  '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar')
//  TypeError: Path must be a string.
```

### path.resolve(path1, path2..)
获取绝对路径。

``` js
path.resolve('/foo/bar', './baz')
// '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/')
// '/tmp/file'

path.resolve('www', 'files/png/', '../gif/image.gif')
//'/home/user/node/www/files/gif/image.gif'
```

### path.relative(from, to)
返回某个路径下相对于另一个路径的相对位置串。

``` js
path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb')
// '../../impl/bbb'
```

#### path.dirname(url)
返回路径的所在的文件夹名称。

``` js
path.dirname('/foo/bar/baz/asdf/quux')
// '/foo/bar/baz/asdf'
```

### path.basename(url, [ext])
返回路径中的最后一部分，以”/“分割。返回结果可排除[ext]后缀字符串。

``` js
path.basename('/foo/bar/baz/asdf/quux.html')
// 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html')
```

### path.extname(url)
返回路径 url 的扩展名。

``` js
path.extname('index.html')   // '.html'

path.extname('index.')  // '.'

path.extname('index')  // ''
```