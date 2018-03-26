# 模块
## 模块规范
现在常见的模块规范分为三类：AMD、CMD 和 CommonJS。这三个规范都是为 javascript 模块化加载而生的，都是在用到或者预计要用到某些模块时候加载该模块，使得大量的系统巨大的庞杂的代码得以很好的组织和管理。模块化使得我们在使用和管理代码的时候不那么混乱，而且也方便了多人的合作。

### CommonJS
Commonjs 是一个更偏向于服务器端的 javascript 规范。当然你也可以把它用在浏览器里边。Node.js 采用了这个规范。 根据 CommonJS 规范，一个单独的文件就是一个模块。加载模块使用 require 方法，该方法读 取一个文件并执行，最后返回文件内部的 exports 对象。

### AMD
Commonjs 解决了模块化的问题，并且可以用在浏览器中，但是 Commonjs 是同步加载模块，当要用到该模块了，现加载现用，这种同步机制到了浏览器里边就有问题了，浏览器同步加载模块会导致性能、可用性、调试和跨域访问等问题。

鉴于浏览器的特殊情况，又出现了一个规范，这个规范呢可以实现异步加载依赖模块，并且会提前加载那就是 AMD 规范。

AMD 基本都是提前说明依赖模块，然后预加载这些模块。RequireJS 是 AMD 的实现。

``` js
define(function (require, exports, module) {
  var a = require('a'),
    b = require('b');
 
  exports.action = function () {};
});
```

#### define
define 方法用于定义模块，RequireJS 要求每个模块放在一个单独的文件里。

``` js
define("alpha", ["require", "exports", "beta"], function (require, exports, beta) {
  exports.verb = function() {
    return beta.verb();
    
    //或者:
    //return require("beta").verb();
  }
});
```

这里定义了一个 alpha 模块，并且依赖于内置的 require，exports 模块，以及外部的 beta 模块。可以看到，第三个参数是回调函数，可以直接使用依赖的模块，他们按依赖声明顺序作为参数提供给回调函数。

### CMD
CMD 也是预加载依赖 js 跟 AMD 的规范在预加载这一点上是相同的，明显不同的地方是调用，和声明依赖的地方。AMD 和 CMD 都是用 difine 和 require ，但是 CMD 标准倾向于在使用过程中提出依赖，就是不管代码写到哪突然发现需要依赖另一个模块，那就在当前代码用 require 引入就可以了。

SeaJS 是 CMD 的实现。

## NodeJS 模块
Node.js 采用的是 CommonJS 规范，根据功能拆分封装代码到不同 js 文件，每一个文件就是一个模块。它们内部各自使用的变量名和函数名都互不冲突，例如，`hello.js` 和 `main.js` 都申明了全局变量 `var s = 'xxx'`，但互不影响。

### 主模块
通过命令行参数传递给NodeJS以启动程序的模块被称为主模块。主模块负责调度组成整个程序的其它模块完成工作。例如通过以下命令启动程序时，main.js就是主模块。

	$ node main.js

判断是否是程序的入口文件有两种方式:

``` js
require.main === module（推荐）
module.parent === null
```

### require 函数
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

### exports 对象
是当前模块的导出对象，用于导出模块公有方法和属性。别的模块通过 require 函数使用当前模块时得到的就是当前模块的 exports 对象。

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

1. module.exports 初始值为一个空对象 {}；

2. exports 是指向 module.exports 的引用；

3. require() 返回的是 module.exports 而不是 exports。

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

  经过上述步骤就可发布模块了。但这个时候也许会考虑到项目中部分目录和文件不应该被发布出去，有点类似 `.gitignore` 的文件配置来排除这些项。这里使用的就是 `.npmignore` 文件，比 `.gitignore` 做得更加灵活。

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
