# webpack

## 开始
``` js
module.exports = {
    entry: './src/index.js' // 这里是项目入口文件地址
    ouput: {
        path: __dirname + "/dist", // 这里是项目输出的路径,__dirname表示当前文件的位置
        filename: "js/"+"[name].js" // 这里是生成文件的名称，可起你想要的名字
    }
}
```

NODE_ENV=production 就是将运行环境设置成生产环境

webpack.config.js 在开发环境下运行 webpack 的配置文件

webpack.production.config.js 在生产环境下运行 webpack 的配置文件

--progress 是编译过程显示进程百分比的

### 浏览器缓存资源
给输出文件加上[hash]来添加 hash 值，这样就可以做到用户加载 html 里会去加载对应hash值得打包文件。

``` js
output: {
  path: __dirname + "/dist",
  filename: "[name][hash].js"
}
```

## loader
``` js
module.exports = {
    entry: './src/index.js' // 这里是项目入口文件地址
    ouput: {
        path: __dirname + "/dist", // 这里是项目输出的路径,__dirname表示当前文件的位置
        filename: "js/"+"[name].js" // 这里是生成文件的名称，可起你想要的名字
    }，
    module: {
        rules: [
            { test: /\.tsx?$/, loader: "awesome-typescript-loader" },
            { enforce: "pre", test: /\.js$/, loader: "source-map-loader" }
        ]
    },
}
```

### css/css预处理语言(less、sass、stylus)
``` js
{
  test: /\.(scss|css)?$/, 
  loader: ["style-loader", "css-loader", "sass-loader"]
}
```

webpack 会按照从右到左的顺序执行 loader，先解析 sass，之后进行 css 的打包编译。

* style-loader： 将 css 插入到页面的 style 标签;
* css-loader： 是处理 css 文件中的 url() 等;
* sass-loader： 是将 sass 文件编译成 css。

### postcss
postcss 是目前 css 兼容性的解决方案，会自动加入前缀，使 css 样式在不同的浏览器能兼容。

第一步、下载：

``` bash
npm i postcss-loader
```

第二步、修改 webpack 配置文件：

``` js
{ 
  test: /\.(sass|css)?$/, 
  loader: ["style-loader", "css-loader", "sass-loader", "postcss-loader"]
}
```
只需要保证 postcss-loader 放在 css-loader 之后就可以。

第三步、在根目录写一个 postcss.config.js 的配置文件：

``` js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
};
```

postcss 解决兼容性问题主要依靠的是它的插件 autoprefixer 。

### 图片处理
对图片进行 base64 转码以减小资源的体积。

第一步、安装：

``` bash
npm install url-loader file-loader --save-dev
```

第二步、在 modules 的 rules 里面添加：

``` js
{
  test: /\.(png|jpg)$/,
  loader: 'url?limit=8192&name=images/[hash:8].[name].[ext]'
}
```

limit 设置一个阈值，小于这个值得图片就会自动启用 base64 编码的图片，大于这个值的图片会打包到 name 这参数对应的路径，图片名称就会包括8位 md5 编码 name 对应文件本来名称，ext 对应扩展名。

在网速不好的时候先于内容加载和减少http的请求次数来减少网站服务器的负担。

### svg
很多时候我们会引入 svg 图片来减小体积或者使用字体图标，在下载的素材文件里，会有一些 `.woff、.svg、.eot` 的文件，则必须对这些文件进行处理：

``` js
{ 
  test: /\.(woff|svg|eot|ttf)?$/, 
  loader: "url-loader" 
}
```

### webpack-dev-server
服务器。

第一步、安装：

``` bash
npm i webpack-dev-server --save-dev
```

> 注意：webpack-dev-server 打包的文件是存在内存中的，所以只用来在开发环境使用。

## 插件
### HtmlWebpackPlugin
这个插件能在构建过程中自动在你的 HTML 文件里插入对构建文件的引用。

第一步、下载：

``` bash
npm i html-webpack-plugin
```

第二步、使用：

``` js
//引入插件
var HtmlWebpackPlugin = require('html-webpack-plugin');

//...

//使用插件
new HtmlWebpackPlugin({
  filename: 'index.html'  //最终生成的文件名
  template: 'path.join(__dirname, 'src/index.html')',  //html 文件模版，可选
  inject: true,
  chunksSortMode: 'dependency'
})
```

> 注意：去掉 index 文件里 js 的引用，带有 hash 值的构建文件将会自动增加到 index 文件。

### 压缩 js 代码
webpack 自带了 UglifyJsPlugin 插件来压缩 js 代码。

``` js
var webpack = require('webpack');

//...

plugins: [
  new webpack.optimize.UglifyJsPlugin({
    compress: {
      warnings: false
    }
  })
]
```

> 注意：在配置文件头部引入 webpack 。

### 拆分文件
我们在使用的js库如vue或者react等的时候，webpack会将它们一起打包，react和react-dom文件就好几百k，全部打包成一个文件，可想而知，这个文件会很大，用户在首次打开时就往往会出现白屏等待时间过长的问题，这时，我们就需要将这类文件抽离出来。

``` js
externals: {
  "react": "React",
  "react-dom": "ReactDOM"
},
```

externals 和 plugins 是平级。左侧 key 表示依赖，右侧 value 表示文件中使用的对象。比如在 react 开发中，我们常常会这样在文件头部写 import React from 'react'，这里大家可以和上面对号入座下。

然后对这个文件进行单独的引入使用了，在 index.html 中添加如下代码：

``` html
<script src="./node_modules/react/umd/react.xxx.js"></script>
<script src="./node_modules/react-dom/umd/react-dom.xxx.js"></script>
```

写到这，我们就已经将文件拆分了。
不过，我们在项目上线的时候不可能会带有node_modules,所以我们就需要使用一个copy插件将react和react-dom文件复制出来

``` js
new CopyWebpackPlugin([ // from是要copy的文件，to是生成出来的文件
  { 
    from: "node_modules/react/umd/react.xxx.js", to: "js/react.min.js" 
  },{ 
    from: "node_modules/react-dom/umd/react-dom.xxx.js", to: "js/react-dom.min.js" 
  },{ 
    from: "public/favicon.ico", to: "favicon.ico" 
  }
])
```

这样我们的index.html文件中就要写成下面这种形式:

``` html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <link rel="icon" href="favicon.ico">
  <title>Projection-Web</title>   
</head>
<body>
  <div id="root"></div>
  
  <script src="js/react.min.js"></script>
  <script src="js/react-dom.min.js"></script>
</body>
</html>
```

### 拆分css
我们也可以将css文件单独拆分出来，这样的好处就是打包的css文件我们可以放到cdn上，然后缓存到浏览器客户端中。这样就尽可能的减小文件的体积，以及不必要的资源重新加载，浪费带宽。

第一步、安装插件：

``` bash
npm install extract-text-webpack-plugin --save-dev
```

第二步、修改 webpack 配置文件：

``` js
var ExtractTextPlugin = require("extract-text-webpack-plugin");

module.exports = {
  //...
  
  module: {
    loaders: [{
      test: /\.css$/,
      loader: ExtractTextPlugin.extract('style-loader',
      'css-loader!postcss-loader')
    }]
  },
  postcss: function() {
    return [autoprefixer, cssnext, precss, cssnano]
  },
  plugins: [
    new ExtractTextPlugin('./css/[name].min.css') // 生成到css文件夹下
  ]
}
```

webpack会将所有引用到的css文件打包，最终生成./css/[name].min.css文件。
