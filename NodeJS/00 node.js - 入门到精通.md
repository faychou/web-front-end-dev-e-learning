# node.js入门到精通
JS是脚本语言，脚本语言都需要一个解析器才能运行。对于写在HTML页面里的JS，浏览器充当了解析器的角色。而对于需要独立运行的JS，NodeJS就是一个解析器。

所以说 Node.js 是服务端 JavaScript 运行环境。简而言之 node.js 是运行在服务端上的 JavaScript。

优点：

* 事件机制，异步，无阻塞的，轻量高效，单进程线程，占用服务器资源少，高并发支持好。
* 适用于消息实时推送，聊天系统，社交平台等高并发，I/O密集型。

缺点：

* 异步编程的缺点往往就是到处callback，会让人不知所措；
* 不适合做企业级应用开发，特别是复杂业务逻辑的，代码不好维护，事务支持不是很好。

## 一、环境安装

## 二、npm

## 三、命令交互模式
打开终端，键入 node 进入命令交互模式。

```
$ node
> console.log('Hello World!');
Hello World!
```

如果要运行一大段代码的话，可以先写一个 JS 文件再运行。

``` js
// hello.js
function hello() {
    console.log('Hello World!');
}
hello();
```

```
$ node hello.js
Hello World!
```

## 四、模块
根据功能拆分封装代码到不同js文件，每一个文件就是一个模块。它们内部各自使用的变量名和函数名都互不冲突，例如，`hello.js` 和 `main.js` 都申明了全局变量 `var s = 'xxx'`，但互不影响。

### 主模块
通过命令行参数传递给NodeJS以启动程序的模块被称为主模块。主模块负责调度组成整个程序的其它模块完成工作。例如通过以下命令启动程序时，main.js就是主模块。

	$ node main.js

判断是否是程序的入口文件有两种方式:

``` js
require.main === module（推荐）
module.parent === null
```

### require函数
用于在当前模块中加载和使用别的模块。require 过的文件会加载到缓存，所以多次 require 同一个文件（模块）不会重复加载

``` js
var foo1 = require('./foo');
var foo2 = require('./foo.js');
var foo3 = require('/home/user/foo');
var foo4 = require('/home/user/foo.js');

// 如果是NodeJS内置模块名称，不做路径解析
require('fs');


// 'D:\\study\\a.js' 查看(不加载)某个模块文件的带完整绝对路径的文件名
require.resolve('./a.js') 

//该对象表示缓存了所有已经被加载的模块的缓存区
require.cache对象 
```

### exports对象
是当前模块的导出对象，用于导出模块公有方法和属性。别的模块通过require函数使用当前模块时得到的就是当前模块的exports对象。

``` js
exports.hello = function () {
  console.log('Hello World!');
};

module.exports = function () {
  console.log('Hello World!');
};
```

``` js
var i = 0;

function count() {
  return ++i;
}

exports.count = count;
```

exports 和 module.exports 的区别了：

1. module.exports 初始值为一个空对象 {}

2. exports 是指向的 module.exports 的引用

3. require() 返回的是 module.exports 而不是 exports

``` js
	exports = module.exports = {...}
```

## 包
nodejs 以包的形式组织程序模块，而包的定义却十分简单——包含文件内容符合规范package.json 文件的目录或归档文件。并通过 `<package-name>@<version>` 来唯一标识每个包。

### 发布包
站在使用者的角度，掌握上述章节的内容已经足够了，但当我们开发出好玩的项目而且又想和大家分享时，那么下面的内容就不得不了解了！

  1. 注册一个 registry 帐号， `npm adduser` ，然后根据引导输入帐号、密码和邮箱地址。

  2. 登录 registry 帐号， `npm login` ，登录信息会保存在客户端。

  3. 发布项目， `npm publish` 。（建议发布版本从1.0.0开始）

  经过上述步骤我们就可发布模块了。但这个时候你也许会考虑到项目中部分目录和文件不应该被发布出去，应该有一个像 `.gitignore` 的文件来配置这些排除项。看来你猜对了，那就是 `.npmignore` 文件，不过 npm 做得更灵活一些！

  1. 默认不带 `.npmignore` 文件，若项目中带 `.gitignore` 则使用 `.gitignore` 文件内容的配置项；

  2. 若带 `.npmignore` 文件则不使用 `.gitignore` 文件中的配置项；

  3. 即使配置 `.npmignore` 文件，也无法排除 package.json 和 README.* 文件；

  4. NPM 内置设定以下文件必须被排除:

``` 
.*.swp
.*.swp
._*
.DS_Store
.git
.hg
.lock-wscript
.svn
.wafpickle-*
CVS
npm-debug.log
```

## 五、 基本模块
### global
在Node.js环境中，也有唯一的全局对象，但不叫window，而叫global。

``` js
判断JavaScript执行环境:
if (typeof(window) === 'undefined') {
    console.log('node.js');
} else {
    console.log('browser');
}
```

### process
代表当前Node.js进程。通过process对象可以拿到许多有用信息：

``` js
process === global.process; // true

process.env; // 返回一个对象，包含了当前Shell的所有环境变量

process.version; // 版本号'v7.10.0'

process.platform; // 当前系统平台 'darwin'

process.arch; // 'x64'

process.argv; // 返回当前进程的命令行参数数组 '/usr/local/bin/node'

// process对象提供以下方法
process.cwd(); // 返回当前工作目录('/Users/faychou/node-demo')

process.chdir('/private/tmp'); // 切换当前工作目录

process.cwd(); // 返回运行当前脚本的工作目录的路径(绝对路径）

process.on(); //监听事件

process.exit(); //退出当前进程
```

> 在 Mac 和 Linux 的终端直接输入 env，会列出当前的环境变量

#### process.nextTick()
process.nextTick()的函数不是立刻执行，而是要等到下一次事件循环。

``` js
// 将在下一轮事件循环中调用:
process.nextTick(function () {
    console.log('nextTick callback!');
});
console.log('nextTick was set!');

// nextTick was set!
// nextTick callback!
```

如果我们响应exit事件，就可以在程序即将退出时执行某个回调函数：

``` js
// 程序即将退出时的回调函数:
process.on('exit', function (code) {
    console.log('about to exit with code: ' + code);
});
```

### __filename 与 __dirname
``` js
console.log(__filename); // /Users/faychou/demo/b.js 当前文件的完整绝对路径的文件名
console.log(__dirname); // /Users/faychou/demo 当前文件所在目录的完整绝对路径
```

## 六、 内置模块
### HTTP
搭建服务器：

第一步：建立一个名为 app.js 的文件，代码内容为：

``` js
var http = require('http'); 
http.createServer(function(request, response) {
  response.writeHead(200, {'Content-Type': 'text/html'});
  response.write('<h1>Node.js</h1>');
  response.end('<p>Hello World</p>'); 
}).listen(3000);

console.log("HTTP server is listening at port 3000."); 
```

第二步：终端运行： 

	node app.js

第三步：打开浏览器访问 http://127.0.0.1:3000 

``` js
// 上面的app.js也可以这样写
function onRequest (req, res) {
  res.writeHead(200, {'Content-Type': 'text/html'});
  res.write('<h1>Node.js</h1>');
  res.end('<p>Hello World</p>'); 
}

http.createServer(onRequest) .listen(3000);
```

* {'Content-Type': 'text/html'}
* {'Content-Type': 'text/plain'}

``` js
var http = require('http');
var port = process.env.PORT || 1337;

http.createServer(function(req, res) {
  // 获得HTTP请求的method和url:
  console.log(req.method + ': ' + req.url);
  
  // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
  res.writeHead(200, { 'Content-Type': 'text/html' });
  res.end(new Date() + '\n');
}).listen(port);

console.log('Server running on port %s', port);
```

#### supervisor
监听文件变化，自动重起服务器。

```
# 安装
npm install -g supervisor

# 使用 supervisor 命令启动 app.js 
supervisor app.js
```

### URL
#### url.parse(urlStr, QueryString, protocol)
将一个URL字符串转换为URL对象。

* urlStr：url字符串；
* QueryString：布尔值；
* protocol：布尔值

``` js
url.parse('http://www.faychou.com:8080/p/a/t/h?query=string#hash');

// 输出结果如下：
{ 
  protocol: 'http:',
  port: '8080',
  hostname: 'faychou.com',
  hash: '#hash',
  search: '?query=string',
  pathname: '/p/a/t/h',
}
```

#### url.format
允许将一个URL对象转换为URL字符串。

``` js
url.format({
  protocol: 'http:',
  host: 'www.faychou.com',
  pathname: '/p/a/t/h',
  search: '?query=string'
});

// 输出结果如下：
'http://www.faychou.com/p/a/t/h?query=string'
```

#### url.resolve
用于拼接URL。

``` js
url.resolve('/one/two/three', 'four')  // '/one/two/four'
	
url.resolve('http://example.com/', '/one')  // 'http://example.com/one'
	
url.resolve('http://example.com/one', '/two')  // 'http://example.com/two'
```

### querystring
用于实现URL参数字符串与参数对象的互相转换。

#### querystring.parse
``` js
querystring.parse('foo=bar&baz=qux&baz=quux&corge');
 
 // =>
{ foo: 'bar', baz: ['qux', 'quux'], corge: '' }

```

#### querystring.stringify
``` js
querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' });
 
// =>
'foo=bar&baz=qux&baz=quux&corge='
```

### path
#### path.normalize(url)
将传入的路径转换为标准路径。除了解析路径中的.与..外，还能去掉多余的斜杠。如果有程序需要使用路径作为某些数据的索引，但又允许用户随意输入路径时，就需要使用该方法保证路径的唯一性。

``` js
path.normalize('/foo/bar//baz/asdf/quux/..')
// '/foo/bar/baz/asdf'
```

> 标准化之后的路径里的斜杠在Windows系统下是\，而在Linux系统下是/。如果想保证任何系统下都使用/作为路径分隔符的话，需要用.replace(/\\/g, '/')再替换一下标准路径。

#### path.join(path1, path2,...)
将传入的多个路径拼接为标准路径。

``` js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..')
//  '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar')
//  TypeError: Path must be a string.
```

#### path.resolve(path1, path2..)
获取绝对路径。

``` js
path.resolve('/foo/bar', './baz')
// '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/')
// '/tmp/file'

path.resolve('www', 'files/png/', '../gif/image.gif')
//'/home/user/node/www/files/gif/image.gif'
```

#### path.relative(from, to)
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

#### path.basename(url, [ext])
返回路径中的最后一部分，以”/“分割。返回结果可排除[ext]后缀字符串。

``` js
path.basename('/foo/bar/baz/asdf/quux.html')
// 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html')
```

#### path.extname(url)
返回路径 url 的扩展名。

``` js
path.extname('index.html')   // '.html'

path.extname('index.')  // '.'

path.extname('index')  // ''
```

### fs
文件系统模块，负责读写文件。

#### fs.open(path, flags, mode, callback(err,fd))
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

#### fs.stat(path, callback(err, stats));
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

#### fs.readFile(pathname, callback (err, data))
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

#### fs.readFileSync
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

#### fs.writeFile
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

#### fs.writeFileSync
将数据同步写入文件

``` js
var fs = require('fs');
var data = 'Hello, Node.js';
fs.writeFileSync('output.txt',data);
```

#### fs.read();
#### fs.close();
#### fs.ftruncate();
#### fs.unlink();
#### fs.mkdir();
#### fs.readdir();
#### fs.rmdir();

### Buffer
创建Buffer：

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

#### Buffer.concat(list, totalLength)
Buffer合并

#### buf.copy(target, tStart, sStart, sEnd)
Buffer拷贝

#### buf.slice(start, end)
Buffer裁剪

#### Buffer.isEncoding(encoding)
测试给定的编码字符串

#### Buffer.isBuffer(obj)
检测是否Buffer类型

### Stream（数据流）
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

### pipe
把一个文件流和另一个文件流串起来，这样源文件的所有数据就自动写入到目标文件里了，所以，这实际上是一个复制文件的程序。

``` js
var fs = require('fs');

var rs = fs.createReadStream('sample.txt');
var ws = fs.createWriteStream('copied.txt');

rs.pipe(ws);
```

## 七、第三方模块
### 设置 Node 路径
当你将应用划分为多个模块时，使用相对路径来引入另一个模块将会非常繁琐：

``` js
const app = require('../../app');
```

而更加简便的方法就是：把你的应用文件放在名为 source 或者 src 的目录中，然后通过 `cross-env` 来设置 `NODE_PATH` 环境变量，使他们可以跨平台使用（可以在 Windows 下读取并运行应用）。

``` bash
npm install --save cross-env
```

之后，在 `package.json` 脚本中设置环境变量：

``` json
"scripts": {
  "start": "cross-env NODE_PATH=source node source/server.js",
  "debug": "cross-env NODE_PATH=source node --debug-brk --inspect source/server.js",
  "test": "cross-env NODE_PATH=source node source/test/index.js"
}
```

然后引入模块：

``` js
const app = require('app');
```

### 进程管理( PM2)
``` bash
# 安装
npm install -g pm2

# 运行
pm2 start source/app.js
```

你可以用 `pm2 list` 管理运行的应用实例，也可以使用 pm2 stop 来终止实例。

### gzip压缩
服务端开启 gzip 压缩一般的 css、js 文件能压缩 60、70% 。如果前端部署用node、express作服务器的话，使用中间件 compression 即可开启 gzip 压缩。

``` js
// server.js
var express = require('express');
var compression = require('compression');
var app = express();
app.use(compression());
```

## 八、事件处理
``` js
addListener(event,listener) // 对指定的事件绑定事件处理函数

on(event,listener) // 对指定的事件绑定事件处理函数(addListener的别名),可以对同一事件绑定不同事件处理函数

once(event,listener) // 对指定事件只执行一次绑定的事件处理函数

removeListener(event,listener) // 对指定事件解除事件处理函数

removeAllListener([event]) // 对指定事件解除所有事件处理函数

listeners(event) // 获取指定事件的所有事件处理函数

emit(event,[args],[..]) // 手工触发指定事件

setMaxListeners(n) // 指定事件处理函数的最大数量,默认对同一事件最大绑定10个事件处理函数.

EventEmitter.listenCount(emitter,event) // 获取某个对象的指定事件的事件处理函数数量
```

## 九、Node的调试器
``` js
在命令行输入 node debug <被执行的脚本文件名> // 启动调试器

启动后,输入 cont/c  // 继续执行余下的代码

输入 next/n // 执行下一句代码

输入 step/s // 进入函数内部

输入 out/o //立即执行完函数内部剩余代码

setBreakpoint/sb([filename默认为当前运行脚步] , line) //设置断点

clearBreakpoint/cb([filename],line) // 取消断点

在调试过程输入repl  // 进入REPL环境

restart // 重新开始脚步调试

kill  // 终止脚本调试

list(n) // 查看当前所执行代码之前/之后的n行代码

backtrace/bt  // 查看函数/外层各函数的返回位置,包括返回代码的行号,起始字符的所在位置

run // 重新开始脚步调试

scripts // 查看当前运行的文件,及所有被加载的模块文件名. 

version // 显示V8引擎版本号
```