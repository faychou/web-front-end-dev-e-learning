# 前端模块化
## 模块化规范
### AMD 浏览器端
异步模块定义，浏览器端模块开发的规范，代表：requirejs。

特点：模块被异步加载，模块加载不影响后面语句的运行。

### CommonJS 
代表：nodejs

加载模块：require()
导出模块：module.exports = {} / exports = {}

### ES6 中的 import / export

### CMD 浏览器端
代表：seajs（阿里前端大神玉伯开发）

## AMD
### 1、定义模块
``` js
// 语法:define(name, dependencies?, factory);
// name表示：当前模块的名称，是一个字符串 可有可无
// dependencies表示：当前模块的依赖项，是一个数组无论依赖一项还是多项 无则不写
// factory表示：当前模块要完成的一些功能，是一个函数
    
// 定义对象模块
define({})
// 定义方法模块
define(function() {
  return {}
})
// 定义带有依赖项的模块
define(['js/a'], function() {})
```

### 2、加载模块
``` js
// - 注意：require的第一个参数必须是数组

// 参数必须是数组 表示模块路径 以当前文件为基准,通过回调函数中的参数获取加载模块中的变量 参数与模块按照顺序一一对应
require(['a', 'js/b'], function(a, b) {
  // 使用模块a 和 模块b 中的代码
})
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