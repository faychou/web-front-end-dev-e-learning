# 使用browserify管理react
    Browserify 可以让你使用类似于 node 的 require() 的方式来组织浏览器端的 Javascript 代码，通过预编译让前端 Javascript 可以直接使用 Node NPM 安装的一些库

## 一、browserify的安装
    npm install -g browserify

## 二、创建项目
1、创建一个react-demo的文件夹

2、cd到react-demo中执行npm init

3、安装react react-dom bable等

    npm install --save react react-dom babel-preset-react babel-loader babel-core

4、在react-demo目录中新建.babelrc文件并输入：

    { "presets": ["react"] }

5、新建一个index.html
``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
  </head>
  <body>
    <div id="example"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

6、新建一个index.js
``` Javascript
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```

7、在根目录下运行以下命令将index.js转化成bundle.js：

    browserify -t [ babelify ] index.js -o bundle.js

8、如果执行成功，会在react-demo目录中生成bundle.js文件

## 三、自动监听文件变化
完成上述步骤后，我们会发现一个问题，就是每次修改index.js文件后都要执行转化成bundle.js命令。所以必须要寻找一个方法来监听index.js文件，当他发生变化后会自动转化成bundle.js。这里就需要用到一个第三方的库watchify。

1、安装：

    npm install -g watchify

2、安装reactify

    npm install --save reactify

3、在package.json中加上

    "browserify" : {
        "transform": [
            ["reactify"]
        ]
    }

4、如果使用的是ES6的语法，则还需要稍作修改：

    "browserify" : {
        "transform": [
            ["reactify", {"es6": true}]
        ]
    }


5、完成以上步骤以后，在命令行输入

    watchify index.js -o bundle.js
