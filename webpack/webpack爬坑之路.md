# webpack

## 开始
``` js
module.exports = {
  entry: './src/index.js' // 项目入口文件地址
  ouput: {
    path: __dirname + "/dist", // 项目输出路径,__dirname表示当前文件位置
    //上面 path 也可以写成:path.join(__dirname, 'dist') 
    publicPath:publicPath,   // 静态资源相对路径
    filename: "js/"+"[name].js" // 打包后的文件名，[name]是指对应的入口文件名字，也可取任意名字
    //filename: '[name].bundle.[hash].js'
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
### enfore
``` js
module.exports = {
  entry: './src/index.js' // 这里是项目入口文件地址
  ouput: {
    path: __dirname + "/dist", // 这里是项目输出的路径,__dirname表示当前文件的位置
    filename: "js/"+"[name].js" // 这里是生成文件的名称，可起你想要的名字
  }，
  module: {
    rules: [
      { 
        test: /\.js$/,
        enforce: "pre",  
        loader: "source-map-loader" 
      }
    ]
  },
}
```

``` js
//语法检查
rules: [
  {
    test: /\.js$/,
    enforce: "pre",
    loader: "eslint-loader"
  }
]
```

> 注意：之前 webpack1 中需要用到 preLoader 的地方可以改到 rules 的 enfore 中进行配置。

### css/css预处理语言(less、sass、stylus)
``` js
module: {
  rules: [
    {
      test: /\.(scss|css)$/,
      include: [
        path.join(__dirname, 'src')
      ], 
      use: ["style-loader", "css-loader", "sass-loader"]
    }
  ]
}
```

webpack 会按照从右到左的顺序执行 loader，先解析 sass，之后进行 css 的打包编译。

* style-loader： 用来将 css 文件嵌入到 js 文件里;
* css-loader： 用来解析 css 文件;
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

postcss 解决兼容性问题主要依靠的是它的插件 autoprefixer ，这里可能要去下载 `autoprefixer` 包。

### babel
``` js
module:{
  rules:[
    {
      test: /\.(js|jsx)$/,
      exclude: /node_modules/, //排除
      loader: 'babel-loader',  //注意 webpack2.x 是不支持 loader 简写
    }
  ]
},
```

### 图片处理
对图片进行 base64 转码以减小资源的体积。

第一步、安装：

``` bash
npm install url-loader file-loader --save-dev
```

第二步、在 modules 的 rules 里面添加：

``` js
{
  test: /\.(png|jpg|gif)$/,
  loader: 'url-loader?limit=8192&name=images/[hash:8].[name].[ext]'
}
```

limit 设置一个阈值，小于这个值得图片就会自动启用 base64 编码的图片，大于这个值的图片会打包到 name 这参数对应的路径，图片名称就会包括8位 md5 编码 name 对应文件本来名称，ext 对应扩展名。

在网速不好的时候先于内容加载和减少http的请求次数来减少网站服务器的负担。

### svg
很多时候我们会引入 svg 图片来减小体积或者使用字体图标，在下载的素材文件里，会有一些 `.woff、.svg、.eot` 的文件，则必须对这些文件进行处理：

``` js
{ 
  test: /\.(woff|svg|eot|ttf)?$/, 
  loader: "file-loader",
  options: {
    name: 'source/[name].[ext]?[hash]' //该参数是打包后的文件名
  }
}
```

### webpack-dev-server
服务器。

第一步、安装：

``` bash
npm i webpack-dev-server --save-dev
```

第二步、配置：


``` js
devServer: {
  contentBase: "./dist",//本地服务器所加载的页面所在的目录
  //historyApiFallback: true, //非hash模式路由不刷新（适用于单页面开发调试）
  noInfo:true,
  host:'192.168.102.103',
  port:'4001'
},
```

> 注意：webpack-dev-server 打包的文件是存在内存中的，所以只用来在开发环境使用。

## resolve

``` js
resolve: {
    extensions: [".js", ".vue", ".json"], // 可以导入的时候忽略的拓展名范围
    alias: { //也就是路径别名
      vue$: "vue/dist/vue.esm.js",  
      "@": resolve("src"),  // 这里就是别名了,比如@就代表直接从/src 下开始找起!!!
      "~": resolve("src/components")
    }
  },
```

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
  favicon: './src/favicon.ico', //favicon路径，会同时可以生成hash值,可选
  filename: 'index.html'  //最终生成的文件名
  template: 'path.join(__dirname, 'src/index.html')',  //html 文件模版，可选
  inject: true, //js插入的位置，true/'head'/'body'/false
  chunksSortMode: 'dependency',
  hash: true, //为静态资源生成hash值
  chunks: [item[0].slice(0,-5),'common'],//需要引入的chunk，不配置就会引入所有页面的资源
  minify: { //压缩HTML文件
    removeComments: true, //移除HTML中的注释
    collapseWhitespace: false, //删除空白符与换行符
    ignoreCustomFragments:[
      // regexp  //不处理 正则匹配到的 内容
    ]
  },
  minify: false //不压缩
})
```

> 注意：去掉 index 文件里 js 的引用，带有 hash 值的构建文件将会自动增加到 index 文件。

### 压缩 js 代码
webpack 自带了 UglifyJsPlugin 插件，来压缩 js 代码。

``` js
var webpack = require('webpack');

//...

plugins: [
  new UglifyJsPlugin({
    compress: {
      warnings: false //警告配置默认为 false，如果编译后想查看警告信息可设置为 true
    }
  })
]
```

UglifyJsPlugin 中的 sourceMap 默认项为 false。需要手动开启UglifyJsPlugin 的配置项：sourceMap: true 。

``` js
devtool: "source-map",
plugins: [
  new UglifyJsPlugin({
    sourceMap: true
  })
]
```

### BannerPlugin
``` js
plugins: [
  new webpack.BannerPlugin({
    banner: 'Banner', 
    raw: true, 
    entryOnly: true
  });
]
```

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
  
  module:{
    rules: [{
      test: /\.css$/,
      use: ExtractTextPlugin.extract({
        fallback: "style-loader",
        use: "css-loader!postcss-loader!sass-loader"
      })
    }]
  },
  postcss: function() {
    return [autoprefixer, cssnext, precss, cssnano]
  },
  plugins: [
    new ExtractTextPlugin('./css/[name].min.css') // 生成到css文件夹下
  ],
  new ExtractTextPlugin("[name].css?[hash]") //基础配置只需要传入打包名称就行了
}
```

webpack会将所有引用到的css文件打包，最终生成./css/[name].min.css文件。

### CommonsChunkPlugin
抽取公共代码，CommonsChunkPlugin 是在有多个 entry 时使用的，即在有多个入口文件时，这些入口文件可能会有一些共同的代码，我们便可以将这些共同的代码抽取出来成独立的文件。

如果在多个 entry 中 require 了相同的 css 文件，我们便可以使用 CommonsChunkPlugin 来将这些共同的样式文件抽取出来为独立的样式文件。

``` js
module.exports = {
  entry: {
    "A": "./src/entry.js",
    "B": "./src/entry2.js"
  },
  //...
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
		name: "commons", filename: "commons.js"
	}),
    //...
  ]
}
```

CommonsChunkPlugin 好像只会将所有 chunk 中都共有的模块抽取出来，如果存在如下的依赖:

``` js
// entry1.js
var style1 = require('./style/myStyle.css')
var style2 = require('./style/style.css')

// entry2.js
require("./style/myStyle.css")
require("./style/myStyle2.css")

// entry3.js
require("./style/myStyle2.css")
```

使用插件后会发现，根本没有生成 commons.css 文件。如果我们只需要取前两个 chunk 的共同代码，我们可以这么做:

``` js
module.exports = {
  entry: {
    "A": "./src/entry.js",
    "B": "./src/entry2.js",
    "C": "./src/entry3.js"
  },
  //...
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
		name: "commons", filename: "commons.js", chunks: ['A', 'B']
	}),
    //...
  ]
}
```

## 项目发布
在开发的阶段，我们往往不需要让文件打包到最优状态，因为需要保证打包速度，但是在发布的时候需要打包到最优状态，这就需要我们对开发和生产两种模式做不同的处理，我是采用  cross-env 这个包获取 NODE_ENV 的值来判断当前是什么环境：

``` js
if (process.env.NODE_ENV === 'production') {
  //生产模式下进行打包优化
}
```

如何来改变 NODE_ENV 的值呢？ cross-env 可以帮助我们通过命令来修改, 执行以下命令，就能将 process.env.NODE_ENV 的值变为 'development'

``` bash
cross-env NODE_ENV=development
```
