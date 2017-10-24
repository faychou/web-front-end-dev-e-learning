# 理解 AMD、CMD、CommonJS 规范
这三个规范都是为javascript模块化加载而生的，都是在用到或者预计要用到某些模块时候加载该模块，使得大量的系统巨大的庞杂的代码得以很好的组织和管理。模块化使得我们在使用和管理代码的时候不那么混乱，而且也方便了多人的合作。

## AMD
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

### define
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

## CMD
CMD 也是预加载依赖 js 跟 AMD 的规范在预加载这一点上是相同的，明显不同的地方是调用，和声明依赖的地方。AMD 和 CMD 都是用 difine 和 require ，但是 CMD 标准倾向于在使用过程中提出依赖，就是不管代码写到哪突然发现需要依赖另一个模块，那就在当前代码用 require 引入就可以了。

SeaJS 是 CMD 的实现。

## CommonJS
Commonjs 是一个更偏向于服务器端的 javascript 规范。当然你也可以把它用在浏览器里边。Node.js 采用了这个规范。 根据 CommonJS 规范，一个单独的文件就是一个模块。加载模块使用 require 方法，该方法读 取一个文件并执行，最后返回文件内部的 exports 对象。