# 前端模块化
ES6 之前，Javascript 并没有模块体系。但是实际开发中，为了更高效的开发，我们需要模块化编程开发，也就产生了几类模块化标准。

## 模块化规范
### AMD
异步加载模块，模块的加载不影响其后面语句的运行。所有依赖这个模块的语句都会添加进一个回调函数中，等到模块加载完成，回调函数就会执行。

代表：requirejs。

适用平台：浏览器端。

特点：模块被异步加载，模块加载不影响后面语句的运行。

### CMD
CMD 和 AMD 解决的问题一样，不过是模块定义方式、模块加载的时机不同。

代表：seajs（阿里前端大神玉伯开发）

适用平台：浏览器端。

CMD & AMD 间的区别：

* AMD 依赖前置，在定义模块时就声明其所要依赖的模块；
* CMD 是按需加载依赖，在用到那个模块再去 require；
* AMD 在使用前就准备好，CMD 是用到了再去准备模块。

### CommonJS
一个文件就是一个模块，每个模块都是单独的作用域，除非定义为 global 对象的属性，属于同步加载方案。

代表：nodejs。

适用平台：服务端。

### ES6 模块化
ES6 静态加载的设计思想，使得在编译时就可以确定模块的依赖关系，以及输入、输出的变量。ES6 则在语言层面上实现了模块化，取代 CommonJS、AMD、CMD 成为服务端和浏览器端通用的模块解决方案。

### 使用 async、defer 属性
使用 async 和 defer 标签就可以异步加载。区别在于：

* defer 等到页面渲染完成才会执行
* async 只要脚本加载完成，立即执行

``` html
<script type="text/javascript" src="./a.js" defer></script>

<script type="text/javascript" src="./b.js" async></script>
```

### 浏览器中使用模块
在浏览器端使用脚本默认开启 defer 属性，也就是按照引入的顺序一个一个加载，这也符合静态化的思想。

``` html
<script  type='module' src='a.js'></script>
<script  type='module'>
  import utils from './utils.js'
  // 其他代码
</script>
```

## requirejs
### 1、定义模块
``` js
// 语法:define(id?, dependencies?, factory);
// id：当前模块的名称，可有可无；
// dependencies：当前模块的依赖项，是一个数组，无则不写
// factory：当前模块要完成的一些功能，一般直接返回一个对象
    
// 定义对象模块
define({});

// 定义方法模块
define(function() {
  return {};
});

// 定义带有依赖项的模块
define(['js/a'], function() {});

//完整
define('math',['jquery'], function ($) {//引入jQuery模块
  return {
    add: function(x,y) {
      return x + y;
    }
  };
});
```

### 2、加载模块
``` js
// - 注意：require 的第一个参数必须是数组

// 参数必须是数组 表示模块路径 以当前文件为基准,通过回调函数中的参数获取加载模块中的变量 参数与模块按照顺序一一对应
require(['a', 'js/b'], function(a, b) {
  // 使用模块a 和 模块b 中的代码
})
```

`require.config` 配置别名：

``` js
//别名配置
requirejs.config({
  paths: {
    jquery: 'jquery.min' //可以省略.js
  }
});
//引入模块，用变量$表示jquery模块
requirejs(['jquery'], function ($) {
  $('body').css('background-color','red');
});
```

### 3、路径查找配置
requirejs 默认使用 baseUrl+paths 的路径解析方式。

可以使用以下方式避开此设置：

* 以.js结尾
* 以 / 开始
* 包含协议：https:// 或 http://

``` js
// 配置示例
// 注意配置应当在使用之前
require.config({
  baseUrl: './js' // 配置基础路径为：当前目录下的js目录
})
require(['a'])    // 查找 基础路径下的 ./js/a.js

// 简化加载模块路径
require.config({
  baseUrl: './js',
  // 配置一次即可，直接通过路径名称（template || jquery）加载模块
  paths: {
    template: 'assets/artTemplate/template-native',
    jquery: 'assets/jquery/jquery.min'
  }
})
// 加载jquery template模块
require(['jquery', 'template'])
```

### 4、非模块化和依赖项支持
* 添加模块的依赖模块，保证加载顺序（deps）
* 将非模块化模块，转化为模块化（exports）

``` js
// 示例
    require.config({
      baseUrl: './js',
      paths: {
        // 配置路径
        noModule: 'assets/demo/noModule'
      },
      // 配置不符合规范的模块项
      shim: {
        // 模块名称
        noModule: {
          deps: [],         // 依赖项
          exports: 'sayHi'  // 导出模块中存在的函数或变量
        }
      }
    });

// 注意点  如果定义模块的时候，指定了模块名称，需要使用该名称来引用模块
    // 定义 这个模块名称与paths中的名称相同
    define('moduleA', function() {})
    // 导入
    require.config({
      paths: {
        // 此处的模块名：moduleA
        moduleA: 'assets/demo/moduleA'
      }
    })
```

### 5、路径加载规则
路径配置的优先级：

* 通过 config 配置规则查找
* 通过 data-main 指定的路径查找
* 以引入 requirejs 的页面所在路径为准查找

``` html
<!-- 
  设置data-main属性
  1 data-main属性指定的文件也会同时被加载
  2 用于指定查找其他模块的基础路径
-->
<script src="js/require.js" data-main="js/main"></script>
```

## seajs
``` js
// 定义模块  myModule.js
define(function(require, exports, module) {
  var $ = require('jquery.js')
  $('div').addClass('active');
  exports.data = 1;
});

// 加载模块
seajs.use(['myModule.js'], function(my) {
  var star= my.data;
  console.log(star);  //1
});
```
