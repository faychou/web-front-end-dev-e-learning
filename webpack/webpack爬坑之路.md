# webpack

## 基本配置
``` js
module.exports = {
  entry: './src/index.js' // 项目入口文件地址
  ouput: {
    path: __dirname + "/dist", // 项目输出路径,__dirname表示当前文件位置
    //上面 path 也可以写成:path.join(__dirname, 'dist') 
    publicPath:publicPath,   // 静态资源相对路径
    filename: "js/"+"[name].js" // 打包后的文件名，[name]是指对应的入口文件名字，也可取任意名字
    //filename: '[name].[hash].js'
  }
}
```

## entry
entry 是指项目入口文件地址，它的值可以是字符串、数组或对象。

### 单一的入口
enter 项的值可以使用任意类型，最终输出的结果都是一样的。

``` js
entry: './src/index.js'
//or
entry: ['./src/index.js']
//or
entry: {
  index:'./src/index.js'
}
```

### enter：数组类型
添加多个彼此不互相依赖的文件，可以使用数组格式的值。

``` js
entry: ['./src/index.js','./src/add.js']
```

### enter：混合类型
也可以在 enter 对象里使用数组类型，例如下面的配置将会生成2个文件：vender.js（包含三个文件），index.js

``` js
entry: {
  vendor:['jquery','fullpage.js','z-tree.js'],
  index:'./src/index.js'
}
```

## ouput
ouput 是指项目输出配置和输出路径。

### output
是指项目输出路径。

``` js
path:__dirname + "/dist" //__dirname 表示当前文件位置
//上面 path 也可以写成:
path:path.join(__dirname, 'dist') 
```
### publicPath
是指静态资源相对路径。path 仅仅告诉 webpack 打包后输出到哪里，然而 publicPath 项则被许多 webpack 的插件用于在生产模式下更新内嵌到 css、html 文件里的 url 值。

``` js
publicPath:"https:www.faychou.cn"
```

例如，在开发模式下的 css 文件中边你可能用 "./app.css" 这样的 url 来加载资源，但是在生产模式下 "app.css" 文件可能会定位到 CDN 上并且你的 Node.js  服务器可能是运行在另一个服务器上。这就意味着在生产环境你必须手动更新所有文件里的 url 为 CDN 的路径。

### filename
设置打包后的文件名。

``` js
filename: "js/"+"[name].js" // [name]是指对应的入口文件名字，也可取任意名字
//filename: '[name].[hash].js'
//filename: "[chunkhash].js"  //入口(entry chunk)」文件命名模版
```

## 浏览器缓存资源
目前 webpack 有两种设置资源缓存的方式：[hash] 和 [chunkhash]。

### hash
hash 代表每次 webpack 在编译的过程中会生成唯一的 hash 值，在项目中任何一个文件改动后就会被重新创建，然后 webpack 计算新的 hash 值。给输出文件加上 [hash] ，这样就可以做到用户加载 html 里会去加载对应 hash 值打包的文件。

``` js
module.exports = {
  entry: __dirname + '/src/index.js',
  output: {
    path: __dirname + "/dist",
    filename: "[name].[hash].js"
  }
}
```

### chunkhash
chunkhash 是根据模块计算出来的 hash 值，所以某个文件的改动只会影响它本身的 hash 值，不会影响其他文件。

> 备注：如果只是单纯地将所有内容打包成一个文件，那么使用 hash 即可，而如果项目涉及到拆包，分模块进行加载等，需要用 chunkhash，来保证每次更新之后只有相关的文件 hash 值发生改变。

``` js
module.exports = {
  entry: __dirname + '/src/index.js',
  output: {
    path: __dirname + '/dist',
    filename: '[name].[chunkhash:8].js',
  }
}
```

## loader
由于 webpack 只能处理 javascript，所以我们需要对一些非 js 文件处理成 webpack 能够处理的模块。多个 loader 可以用在同一个文件上并且被链式调用。链式调用时从右到左执行且 loader 之间用 "!" 来分割。

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

### css/css 预处理语言(less、sass、stylus)
``` js
module: {
  rules: [
    {
      test: /\.(scss|css)$/,
      include: [ // 必须匹配选项
        path.join(__dirname, 'src')
      ], 
      use: ["style-loader", "css-loader", "sass-loader"]
      //也可以写成 loader："style-loader!css-loader!sass-loader"
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
npm install postcss-loader autoprefixer --save-dev
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

//或者
//建立.postcssrc.js文件
module.exports = {
  "plugins": {
    // to edit target browsers: use "browserslist" field in package.json
    "autoprefixer": {
      "browsers": [
        "ie >= 9",
        "ff >= 30",
        "chrome >= 34",
        "safari >= 7",
        "opera >= 23"
      ]
    }
  }
}
```

postcss 解决兼容性问题主要依靠的是它的插件 autoprefixer ，这里要去下载 `autoprefixer` 包。

### babel
``` js
module:{
  rules:[
    {
      test: /\.(js|jsx)$/,
      exclude: /node_modules/, //排除 node_modules 文件夹
      loader: 'babel-loader',  //注意 webpack2.x 是不支持 loader 简写
      options: { // loader的可选项
        presets: ["es2015"]
      }
    }
  ]
},
```

### 使用 typescript 
``` js
{
  module: {
    rules: [{
      test: /\.(tsx|ts)?$/,
      use: ["ts-loader"]
    }]
  }
}
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
  test: /\.(png|jpe?g|gif)$/,
  loader: 'url-loader?limit=8192&name=images/[name].[hash:8].[ext]'
}

//也可以这样设置
{
  test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
  loader: "url-loader",
  options: { // loader 的可选项
    limit: 8192,
    name: path.join(__dirname, 'src/images/[name].[hash:8].[ext]')
    // [name]：指定文件输出路径和输出文件命令规则
    // [hash:8]：表示使用7位哈希值代表文件名称
    // [ext]：表示保持文件原有后缀名
  }
}
```

file-loader 是解析图片地址，把图片从源文件拷贝到目标文件并且修改源文件名字。

使用 url-loader 对引入的图片进行编码,将小于 limit 设置的阈值 8192 字节(8kb)的图片转为 DataURL(base64) 格式直接写入 src 里面，可以降低网络请求次数。大于 limit 字节的会自动调用 file-loader 进行图片路径的处理，name 对应文件本来名称，以及 8 位 md5 编码， ext 对应扩展名。

在网速不好的时候先于内容加载和减少 http 的请求次数来减少网站服务器的负担。

### svg 和字体
很多时候我们会引入 svg 图片来减小体积或者使用字体图标，在下载的素材文件里，会有一些 `.woff、.svg、.eot` 的文件，则必须对这些文件进行处理：

``` js
{ 
  test: /\.(woff|woff2|svg|eot|ttf)?$/, 
  loader: "file-loader",
  options: {
    name: 'fonts/[name].[ext]?[hash]' //该参数是打包后的文件名
  }
},

//或者使用下面这种方式也行
{
  test:/\.(woff2?|eot|ttf|otf)(\?.*)?$/,
  loader:"url-loader",
  options:{
    limit:10000,
    name:'fonts/[name].[ext]?[hash]'
  }
},
```

### webpack-dev-server
这是一个基于 Express.js 框架开发的 web server，默认监听 8080 端口。提供了热更新 "Live Reload" 以及热替换 "Hot Module Replacement"（HMR）。

第一步、安装：

``` bash
npm install webpack-dev-server --save-dev
```

第二步、配置：


``` js
devServer: {
  port:'4001', //端口号
  open: true, //自动打开浏览器
  compress: true, //启用 gzip 压缩
},
```

第三步、运行：

``` js
// 方式 1：终端输入
webpack-dev-server --inline --hot

// 方式 2：在 package.json 的 scripts 中添加以下字段
//--progress 是编译过程显示进程百分比的
//--color 显示颜色
"scripts": {
 "dev": "webpack-dev-server --inline --hot --progress --color"
 }
 
// 然后终端中运行以下命令： 
$ npm run dev

// 最后在浏览器中预览以下地址：
http://localhost:4001
```

> 注意：webpack-dev-server 打包的文件是存在内存中的，所以只用来在开发环境使用。

> 如果 hot、inline 两个参数都传入，当资源改变时，webpack-dev-server 将会先尝试HRM，如果失败则重新加载整个入口页面。

其他配置项：
#### contentBase
本地服务器所在的目录，默认为当前目录，推荐使用绝对路径。

``` js
devServer: {
  contentBase: path.join(__dirname, "dist")
  //也可以设置多个目录
  //contentBase: [path.join(__dirname, "dist"), path.join(__dirname, "assets")]
}
```

#### host
设置主机名，默认是 localhost。

``` js
devServer: {
  host:'192.168.102.103'
}
```

#### inline
热更新。

#### hot
热替换。

``` js
devServer: {
  inline: true,
  hot:true
}
```

> 注意：直接在 devServer 配置项中设置 hot: true 和 inline: true 可能不生效，所以最好的方式是直接在 CLI 中设置。

#### historyApiFallback
依赖于 HTML5 history API，非 hash 模式路由不刷新（适用于单页面开发调试）。也就是说当设置为 true 时，任意的 404 响应都可能需要被替代为 index.html。

``` js
devServer: {
  historyApiFallback: true
}
```

通过传入一个对象，此行为可进一步地控制：

``` js
devServer: {
  historyApiFallback: {
    rewrites: [
      { from: /^\/$/, to: '/views/landing.html' },
      { from: /^\/subpage/, to: '/views/subpage.html' },
      { from: /./, to: '/views/404.html' }
    ]
  }
}
```

#### disableHostCheck: 
设置为 true, 为了手机可以访问。

#### proxy
配置代理。

``` js
devServer: {
  proxy: {
    "/api": "http://localhost:3000"
  }
}
```

请求到 /api/users 会被代理到 http://localhost:3000/api/users。如果不想传递 /api ：

``` js
devServer: {
  proxy: {
    "/api": {
      target: "http://localhost:3000",
      pathRewrite: {"^/api" : ""}
    }
  }
}
```

默认情况下，不接受 HTTPS ，且使用了无效证书的后端服务器。如果想要接受，修改配置如下：

``` js
proxy: {
  "/api": {
    target: "https://other-server.example.com",
    secure: false
  }
}
```

解决跨域。

``` js
devServer: {
  proxy: 192.168.0.0:8080
}
```

#### allowedHosts
设置访问白名单。

``` js
devServer: {
  allowedHosts: [
    'host1.com',
    'host2.com'
  ]
}
```

#### https
默认情况下，dev-server 通过 HTTP 提供服务。也可以选择带有 HTTPS 的 HTTP/2 提供服务。

``` js
devServer: {
  https: true
}
```

使用以下设置自签名证书：

``` js
devServer: {
  https: {
    key: fs.readFileSync("/path/to/server.key"),
    cert: fs.readFileSync("/path/to/server.crt"),
    ca: fs.readFileSync("/path/to/ca.pem"),
  }
}
```

#### noInfo
启用 noInfo 后，诸如「启动时和每次保存之后，那些显示的 webpack 包(bundle)信息」的消息将被隐藏。错误和警告仍然会显示。

``` js
noInfo: true
```

## devtool
使用 devtool 可以调试打包的代码，自动生成 source maps 文件，map 文件是一种对应编译文件和源文件的方法。

``` js
module.exports = {
  //...
  devtool: 'inline-source-map',//生成sourceMaps(方便调试)
  //...
}
```

## resolve

``` js
resolve: { // 解析模块的可选项
  modules: [ // 模块的查找目录
    "node_modules",
    path.resolve(__dirname, "app")
  ],
  extensions: [".js", ".vue", ".json"], // 导入的时候可以忽略文件的扩展名
  alias: { // 模块别名列表
    // import Vue from 'vue/dist/vue.js'可以写成 import Vue from 'vue'
    // 键后加上$,表示精准匹配！
    vue$: "vue/dist/vue.js",
    //用@直接指引到src目录下，如：'./src/main'可以写成、'@/main' 
    "@": resolve("src"),
    "~": resolve("src/components")
  }
},
```

``` js
// alias 使用案例
alias: {
  'src': path.resolve(__dirname, '../src'),
  'components': path.resolve(__dirname, '../src/components'),
  'api': path.resolve(__dirname, '../src/api'),
  'utils': path.resolve(__dirname, '../src/utils'),
  'store': path.resolve(__dirname, '../src/store'),
  'router': path.resolve(__dirname, '../src/router')
}

//使用
import stickTop from 'components/stickTop'
import getArticle from 'api/article'
```

## 插件
### 自动删除 dist 目录
每次打包之前, 删除上一次打包的 dist 目录。

安装：

``` bash
npm i -D clean-webpack-plugin
```

配置：

``` js
/* webpack.prod.js */
const cleanWebpackPlugin = require('clean-webpack-plugin')

plugins: [
  // 创建一个删除文件夹的插件，删除dist目录
  new cleanWebpackPlugin(['./dist'])
]
```

### 热替换
热替换是指修改文件内容之后不用手动刷新页面，修改的部分会自动刷新，webpack 内部已经支持，不需要下载，直接在 plugins 中添加以下代码就行：

``` js
plugins:[
  new webpack.HotModuleReplacementPlugin()
]
```

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
plugins: [
new HtmlWebpackPlugin({
  favicon: './src/favicon.ico', //favicon路径，会同时可以生成hash值,可选
  title: "hello webpack", //title标签的内容
  filename: 'index.html'  //最终生成的文件名，默认为 index.html
  template: 'path.join(__dirname, 'src/index.html')',  //html 文件模版，可选
  inject: true, //js 插入的位置
  hash: true, //为静态资源生成 hash 值
  minify: { //压缩HTML文件
    removeComments: true, //移除HTML中的注释
    collapseWhitespace: true, //删除空白符与换行符
    removeAttributeQuotes: true // 移除属性的引号
    ignoreCustomFragments:[
      // regexp  //不处理 正则匹配到的 内容
    ]
  }
})
]
```

其他几个属性指介绍：
#### inject
有四个选项值：

* true：默认值，script标签位于html文件的 body 底部
* body：同 true
* head：script 标签位于 head 标签内
* false：不插入生成的 js 文件，只是单纯的生成一个 html 文件

#### cache
默认值是 true。表示只有在内容变化时才生成一个新的文件。

#### showErrors
showErrors 的作用是，如果 webpack 编译出现错误，webpack会将错误信息包裹在一个 pre 标签内，属性的默认值为 true ，也就是显示错误信息。

#### chunks
chunks 选项的作用主要是针对多入口(entry)文件。当你有多个入口文件的时候，对应就会生成多个编译后的 js 文件。那么 chunks 选项就可以决定是否都使用这些生成的 js 文件。

``` js
// webpack.config.js
entry: {
  index: path.resolve(__dirname, './src/index.js'),
  index1: path.resolve(__dirname, './src/index1.js'),
  index2: path.resolve(__dirname, './src/index2.js')
}
//...
plugins: [
  new HtmlWebpackPlugin({
    //...
    chunks: ['index','index2']
  })
]
```

如果没有指定 chunks 选项，默认会在生成的 html 文件中引用所有的 js 文件。但是通过以上的方式指定，在生成的 index.html 文件中，只引用了 index.js 和 index2.js 。

#### excludeChunks
跟 chunks 是相反的，排除引入某些 js 文件。 如

``` js
excludeChunks: ['index1.js']
```

#### chunksSortMode
这个选项决定了 script 标签的引用顺序。默认有四个选项:

* dependency：按照不同文件的依赖关系来排序。
* auto：默认值，插件的内置的排序方式。
* none：无序。
* {function}：提供一个函数。

``` js
chunksSortMode: 'dependency'
```

#### xhtml
一个布尔值，默认值是 false ，如果为 true ,则以兼容 xhtml 的模式引用文件。

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

### 拆分css
我们也可以将 css 文件单独拆分出来，这样的好处就是打包的 css 文件我们可以放到 cdn 上，然后缓存到浏览器客户端中。这样就尽可能的减小文件的体积，以及不必要的资源重新加载，浪费带宽。

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
        use: ["css-loader","postcss-loader","sass-loader"]
      })
    }]
  },
  postcss: function() {
    return [autoprefixer, cssnext, precss, cssnano]
  },
  plugins: [
    new ExtractTextPlugin('./css/[name].min.css') // 生成到css文件夹下
  ],
}
```

webpack 会将所有引用到的 css 文件打包，最终生成 ./css/[name].min.css 文件。

### 分离业务代码和第三方的代码
我们在使用 vue 或者 react 等第三方库的时候，webpack 会将自己写的业务代码和引用的第三方库一起打包成一个 js 文件，一是会导致这个文件很大，用户在首次打开时可能会出现白屏等待时间过长的问题，二是业务代码更新频率高，而第三方库更新迭代速度慢，如果将第三方库进行抽离，这样可以充分利用浏览器的缓存来加载第三方库。

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
不过，我们在项目上线的时候不可能会带有 node_modules ,所以我们就需要使用一个 copy 插件将 react 和 react-dom 文件复制出来

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

这样我们的 index.html 文件中就要写成下面这种形式:

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

### 按需加载
比如在使用 React-Router 的时候，当用户需要访问到某个路由的时候再去加载对应的组件，那么用户没有必要在一开始的时候就将所有的路由组件下载到本地。

### 抽取公共代码(CommonsChunkPlugin)
抽取公共代码：是指在有多个 entry 时（多页面应用），这些入口文件可能会有一些共同的代码，我们便可以将这些共同的代码抽取出来成独立的文件。比如 header, footer 等等，这样页面在进行跳转的时候这些公共模块因为存在于缓存里，就可以直接进行加载了，而不是再进行网络请求了。

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
      name: "commons", 
      filename: "commons.js"
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
      name: "commons", 
      filename: "commons.js", 
      chunks: ['A', 'B']
	}),
    //...
  ]
}
```

#### 例子
最后来一个综合按需加载、拆分第三方库、分离公共库、分离 css 的案例：

``` js
// src/pageA.js
import componentA from './common/componentA';

// 使用到 jquery 第三方库，需要抽离，避免业务打包文件过大
import $ from 'jquery';

// 加载 css 文件，一部分为公共样式，一部分为独有样式，需要抽离
import './css/common.css'
import './css/pageA.css';

console.log(componentA);
console.log($.trim('    do something   '));

// src/pageB.js
// 页面 A 和 B 都用到了公共模块 componentA，需要抽离，避免重复加载
import componentA from './common/componentA';
import componentB from './common/componentB';
import './css/common.css'
import './css/pageB.css';

console.log(componentA);
console.log(componentB);

// 用到异步加载模块 asyncComponent，需要抽离，加载首屏速度
document.getElementById('xxxxx').addEventListener('click', () => {
  import( /* webpackChunkName: "async" */
    './common/asyncComponent.js').then((async) => {
      async();
  })
})

// 公共模块文件中基本长这样
export default "component X";
```

```js
// webpack 配置
const path = require('path');
const webpack = require('webpack');
const ExtractTextPlugin = require('extract-text-webpack-plugin');
module.exports = {
  entry: {
    pageA: [path.resolve(__dirname, './src/pageA.js')],
    pageB: path.resolve(__dirname, './src/pageB.js'),
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'js/[name].[chunkhash:8].js',
    chunkFilename: 'js/[name].[chunkhash:8].js'
  },
  module: {
    rules: [
      {
        // 用正则去匹配要用该 loader 转换的 CSS 文件
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: ["css-loader"]
        })  
      }
    ]
  },
  plugins: [
    //抽离公共模块
    new webpack.optimize.CommonsChunkPlugin({
      name: 'common',
      //某个模块被加载两次即以上，移到 common chunk 里
      minChunks: 2,
    }),
    //提取第三方代码
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      minChunks: ({ resource }) => (
        //判断资源是否来自 node_modules，并且是以 .js 结尾的话，则说明是第三方模块
        resource && resource.indexOf('node_modules') >= 0 && resource.match(/\.js$/)
      )
    }),
    //将 css 从打包好的 js 文件中抽离，生成独立的 css 文件
    new ExtractTextPlugin({
      filename: `css/[name].[chunkhash:8].css`,
    }),
  ]
}
```

### 引入第三方库（如：jQuery）
第一步、在 html 模板中直接引入 cdn 地址：

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>vue demo</title>
</head>
<body>
  <div id="app">
  </div>

  <script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.js"></script>
</body>
</html>
```

第二步、在 webpack.config.js 配置中加一项“外部引入”（externals）：

``` js
// webpack.config.js
externals:{
  'jquery':'window.jQuery'
}
```

第三步、在需要使用的地方引入：

``` js
//app.vue中引入
import $ from 'jquery'
```

## 项目发布
### 谈谈 NODE_ENV
我们经常在工程代码中见到如下使用方法：

``` js
process.env.NODE_ENV
```

但是很显然，NODE_ENV 属性并不在原生 process.env 属性对象上。而这个属性是一个自定义变量，主要用途是在使用 nodejs 环境执行脚本时，通过这个属性来区分不同环境（开发、生产、测试等）下的应用程序打包、构建、运行策略。

最常见的两个值：

``` js
process.env.NODE_ENV === 'development'; // 或简写 dev，意为开发环境
process.env.NODE_ENV === 'production'; // 或简写 prod，意为生产环境
```

#### 如何使用
package.json 中 scripts 属性是一个对象，它的每一个键名都可以在命令行中通过`npm run 键名` 来运行，在键值中添加如下代码:

``` js
{
  "scripts": {
    "dev": "NODE_ENV=development webpack --config webpack.dev.config.js"
  }
}
```

这样就把 NODE_ENV 属性添加到了 `process.env` 对象上，值为 development，然后就只能在 webpack.dev.config.js 脚本中访问到 `process.env.NODE_ENV` ，而无法在其它脚本中访问。

#### 在 webpack 中的使用
webpack 只打包入口 js 文件，这个入口 js 文件及其引用的 js 文件无法访问为 webpack.dev.config.js 脚本提供的 `process.env.NODE_ENV`，但是可以通过 webpack 的插件来让入口 js 文件及其引用的 js 文件都能访问到 `process.env.NODE_ENV`：

``` js
const webpack = require('webpack');
module.exports = {
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': 'development'
    })
  ]
}
```

### 开发 VS 生产
一般情况下 webpack 的配置文件分为两种：开发模式、生产模式。在不同模式下的配置是稍微有一些区别的。

webpack.development.config.js 是在开发环境下运行 webpack 的配置文件。

webpack.production.config.js 是在生产环境下运行 webpack 的配置文件。

而区别这两种模式的方法就是依据 NODE_ENV 的值。比如说设置 `NODE_ENV=production` 就是将运行环境设置成生产环境。

然后在 package.json 文件加入如下的 scripts 项来运行 webpack：

``` js
"scripts": {
  // 运行npm run build 来编译生成生产模式下的bundles
  "build": "webpack --config webpack.production.config.js",
  // 运行npm run dev 来生成开发模式下的 bundles 以及启动本地 server
  "dev": "webpack-dev-server --progress"
 }
```

### cross-env
上面这种运行不同文件的方式显然不是很智能，所以通常做法是通过 cross-env 这个包来改变 NODE_ENV 的值来实现的。

所以首先第一步安装 cross-env ：

``` bash
npm install --save cross-env
```

接着如何来改变 NODE_ENV 的值呢？ cross-env 可以帮助我们通过命令来修改, 执行以下命令，就能将 process.env.NODE_ENV 的值变为 'development'

``` bash
cross-env NODE_ENV=development
```

所以我们经常可以看到 package.json 有这样的配置：

``` json
"scripts": {
  "dev": "cross-env NODE_ENV=development webpack-dev-server --open --hot",
  "build": "cross-env NODE_ENV=production webpack --progress --hide-modules"
}
```

在运行 webpack 命令之前运行了 cross-env NODE_ENV=develpoment 或者 production,这就是给环境变量赋值的过程。

然后在 webpack.config.js 中通过获取 NODE_ENV 的值来判断当前的环境：

``` js
if (process.env.NODE_ENV === 'production') {
  //生产模式下进行打包优化
}
```

## 版本升级
### 4.0
与 3.x 最大的差别在于新增了 `--mode` ，可以实现零配置区分开发环境与生产环境。

开发模式：

``` bash
webpack-dev-server --mode development --progress --hot --hotOnly --config ./webpack.config.js
```

生产环境：

``` bash
webpack --mode production --progress --config ./webpack.config.js
```

开启了 --mode production，会自动开启 js、css 代码压缩、scope hoist 等插件，以及自动传递环境变量给 lib 包，所以已经不需要 plugins 这个配置项了。同理，开启了 --mode development 会自动开启 sourceMap 等开发插件，我们只要关心更简单的配置，这就是 4.0 零配置的重要改变。

