# webpack-V4
webpack 可以看做是模块打包机：它做的事情是，分析你的项目结构，找到 JavaScript 模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript 等），并将其打包为合适的格式以供浏览器使用。

## 安装
第一步、初始化配置:

``` bash
mkdir webpack-demo
cd webpack-demo

# npm i yarn -g
yarn init -y
# yarn init --yes 即全部选项默认为 yes 
```

第二步、安装（拆分了 webpack-cli）：

``` bash
yarn add webpack webpack-cli @webpack-cli/init -D
```

第三步、初始化配置：

``` bash
npx webpack-cli init
```

webpack.config.js 基本配置：

``` js
// webpack.config.js

const webpack = require('webpack');
const path = require('path');

const UglifyJSPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
	module: {
		rules: [
			{
				include: [path.resolve(__dirname, 'src')],
				loader: 'babel-loader',

				options: {
					plugins: ['syntax-dynamic-import'],

					presets: [
						[
							'@babel/preset-env',
							{
								modules: false
							}
						]
					]
				},

				test: /\.js$/
			},
			{
				test: /\.css$/,

				use: [
					{
						loader: 'style-loader',

						options: {
							sourceMap: true
						}
					},
					{
						loader: 'css-loader'
					}
				]
			}
		]
	},

	output: {
		chunkFilename: '[name].[chunkhash].js',
		filename: '[name].[chunkhash].js'
	},

	mode: 'development',

	optimization: {
		splitChunks: {
			cacheGroups: {
				vendors: {
					priority: -10,
					test: /[\\/]node_modules[\\/]/
				}
			},

			chunks: 'async',
			minChunks: 1,
			minSize: 30000,
			name: true
		}
	},
	plugins: [
	    new UglifyJSPlugin() // 压缩 JS 代码
	]
};
```

## mode
在 4.0 的版本中，引入了 mode 概念，为开发，及生产环境提供两种不同的默认配置，极大的简化了 webpack 的配置,默认是 production。

``` js
module.exports = {
  mode: 'development' // 必须，development || production
}
```

或者：

``` json
// package.json
"scripts": {
  "build": "webpack --mode development",
  "dev": "webpack-dev-server --open --mode development"
}
```

开发模式更关注开发体验：

* 更利于浏览器调试
* 更快的增量编译速度
* 更详细的报错消息

生成模式更关注用户体验：

* 性能（文件大小，运行性能，打包速度）

在 production 模式下，代码是默认会压缩的。

在 webpack 中区分两种 模式：

``` js
if(process.env.NODE_ENV === 'development'){
    //开发环境 do something
}else{
    //生产环境 do something
}
```

## 基本配置
``` js
module.exports = {
  entry: '',  // 打包文件的入口
  output: {}, // 打包后的出口文件配置
  devServer: {}, // 开发服务器
  module: {}, // 模块配置
  plugins: [], // 插件的配置
  mode: 'development', // ['development', 'production']模式
  resolve: {} // 配置解析
}
```

### context
Webpack 在寻找相对路径的文件时会以 context 为根目录，context 默认为执行启动 webpack 时所在的当前目录。

``` js
module.exports = {
  context: path.resolve(__dirname, 'app')
}
```

### 入口(entry)
指 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。默认值是 `./src/index.js`。

``` js
module.exports = {
  entry: './src/index.js'
};

// 或者对象形式
entry: {
  app: ['./src/index.js', './src/index.html'], // 创建多个住入口
  vendor: ['react'] 
},
```

* 在入口中配置 vendor，可以 code spliting ，将这些公共的复用代码最终抽取成一个chunk，单独打包出来

* 要想在开发模式中 HMTL 文件也热更新，需要加入 `index.html` 为入口文件

多页面应用程序: 

``` js
module.exports = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```

### 输出(output)
打包输出的位置，主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。

``` js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'), // 打包后路径,必须是绝对路径
    filename: 'js/[name].bundle.js' // 打包后文件名字
    // filename: 'js/[name].[hash:8].js',
  }
}
```

* path：配置输出文件存放在本地的目录，必须是 string 类型的绝对路径。

* publicPath：用来为项目中的所有资源指定一个基础路径。使用的是相对路径。

* filename：打包后的文件名。

* chunkFilename：就是用来给拆分后的 chunk 们起名字的配置项。


对于单个入口起点，filename 会是一个静态名称。然而，当通过多个入口起点(entry point)、代码拆分(code splitting)或各种插件(plugin)创建多个 bundle，应该使用以下一种替换方式，来赋予每个 bundle 一个唯一的名称

``` js
module.exports = {
  //...
  output: {
    filename: 'js/[name].bundle.js', // 使用入口名称命名
    
    filename: 'js/[id].bundle.js', // 使用内部 chunk id 命名
    
    filename: 'js/[name].[hash].bundle.js', // 使用每次构建过程中，生成唯一的 hash
    
    filename: 'js/[name].[chunkhash].bundle.js', // 使用基于每个 chunk 内容的 hash
    
    filename: 'js/[name].[contenthash].bundle.css', // 使用提取内容生成的散列
    
    filename: (chunkData) => { // 使用函数
      return chunkData.chunk.name === 'main' ? 'js/[name].js': 'js/[name]/[name].js';
    },
  }
}
```

chunkhash：代表的是chunk的hash值。chunk在webpack中的就是模块的意思，那么chunkhash就是根据模块内容计算得出的hash值。

hash：是compilation的hash值。

总之一句话： hash 是整体的文件计算所得，chunkhash 是具体模块文件所得。

``` js
filename:'[name].[hash:8].js'
publicPath: 'https://faychou.cn/assets/'
```

那么发布上线的时候，路径就是: https://faychou.cn/assets/a.12345678.js

## devServer
一个小型的 Node.js Express服务器，程序开发时 本地运行项目。

``` bash
# 安装
yarn add webpack-dev-server -D
```

``` js
// webpack.config.js 配置
devServer: { 
  index: 'index.html',    // 服务器启动的页面（同 html-webpack-plugin 中 filename 选项）; 默认值为 index.html
  contentBase: path.join(__dirname, "dist"), // 告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要
  port: 9090, // 端口
  host: 'localhost', // 指定host; 默认 localhost
  overlay: true, // 编译器错误或警告时, 在浏览器中显示全屏覆盖; 默认 false
  open: true, // 启动本地服务后，自动打开页面
  compress: true, // 服务器返回浏览器的时候是否启动 gzip 压缩
  progress: true, // 是否将运行进度输出到控制台; 默认 false
}
```
``` json
// package.json
"scripts": {
  "dev": "webpack-dev-server --hot --inline --mode=development"
  // "dev": "webpack-dev-server --config webpack/config.js"
},
```

webpack-dev-server 默认去找根目录下名为 `webpack.config.js` 的 webpack 配置文件，进行打包、运行,如果有场景要自定义 webpack 配置文件名称/路径，可以使用 --config 指定 webpack 配置文件。

### 模块热替换
即在不重载页面的情况下，实时替换更新修改的模块。提高开发效率。

``` bash
# 安装
yarn add react-hot-loader -D
```

`.babelrc` 文件新添加内容：

``` js
{
  "plugins": ["react-hot-loader/babel"]
}
```


## source maps
source maps 文件是一种对应编译文件和源文件的方法调试打包的代码，webapck 通过配置可以自动生成 source maps 文件。

``` js
// webpack.config.js 配置
module: {
  devtool: 'eval-source-map'
}
```

* source-map 把映射文件生成到单独的文件，最完整最慢

* cheap-module-source-map(开发环境 推荐) 在一个单独的文件中产生一个不带列映射的 Map

* eval-source-map 使用 eval 打包源文件模块,在同一个文件中生成完整 sourcemap

* cheap-module-eval-source-map sourcemap 和打包后的 JS 同行显示，没有映射列

## resolve 解析
指定 extensions 之后可以不用在 require 或是 import 的时候加文件扩展名,会依次尝试添加扩展名进行匹配。

``` js
resolve: {
  // 自动补全后缀，后缀一定以点开头,默认值是['.js', '.json']
  extensions: ['.js', '.json', '.css'],
  // 配置别名可以加快 webpack 查找模块的速度
  alias: {
    '@': path.resolve(__dirname, 'src'),
    'bootstrap': path.resolve(__dirname, 'src/bootstrap/dist/css/bootstrap.css')
  }
},
```

## loader
loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。可以将所有类型的文件转换为 webpack 能够处理的有效模块。

几乎所有 loader 都 需要安装， 但 不需要 在 webpack 配置文件中通过 require 引入。

### css
css-loader 用来处理 css 中 url 的路径，style-loader 可以把 css 文件变成 style 标签插入 head 中，多个 loader 是有顺序要求的，从右往左写，因为转换的时候是从右往左转换的。

``` bash
# 安装
yarn add style-laoder css-loader -D
```

``` js
// webpack.config.js 配置

module.exports = {
  module: {
    rules: [{
      test: /\.css$/,
      use: ['style-laoder', 'css-loader'],
      include: path.join(__dirname, 'src'), // 限制范围，提高打包速度
      exclude: /node_modules/
    }]
  },
}

// or
module.exports = {
  module: {
    rules: [{
      test: /\.css$/,
      include: path.join(__dirname, 'src'), //限制范围，提高打包速度
      exclude: /node_modules/,
      use: ['style-laoder', {
        loader: 'css-loader',
        options: {
          modules: true
        }
      }]
    }]
  },
}
```

### 处理 css3 属性前缀
``` bash
# 安装
yarn add autoprefixer postcss-loader -D
```

``` js
// webpack.config.js 配置

// const autoprefixer = require('autoprefixer')

module: {
  rules: [{
    test: /\.css$/, // 转换文件的匹配正则
    use: ['style-loader', 'css-loader', 'postcss-loader']
    // use: ['style-loader', 'css-loader', 'postcss-loader', 'sass-loader']
    // use: ['style-loader', 'css-loader', 'postcss-loader', 'less-loader']
  }],
}
```

然后在根目录新建 postcss.config.js 文件：

``` js
module.exports = {
  plugins: [
    require('autoprefixer')({
      'browsers': [ '> 1%', 'last 2 versions', 'not ie <= 8' ]
    })
  ]
};
```

或者在根目录下新建 `.browserslistrc` 配置文件：

``` js
> 1% // 表示包含所有使用率 > 1% 的浏览器
last 2 versions // 表示包含浏览器最新的两个版本
not ie <= 8 // 表示不包含 ie8 及以下版本
```

### Sass
``` bash
# 安装
yarn add sass-loader node-sass -D
```

``` js
// webpack.config.js 配置
module: {
  rules: [
    {
      test: /\.(css|scss|sass)$/,
      use: [ 'style-loader', 'css-loader', 'sass-loader' ]
    },
  ]
}
```

### Less
``` bash
yarn add less  less-loader -D
```

``` js
{
  test: /\.less$/,
  use: [ 'style-loader', 'css-loader', {
      loader: 'less-loader',
      options: {
        sourceMap: true,
        javascriptEnabled: true,
        modifyVars: {
          'primary-color': '#531dab'
        }
      }
    }
  ]
}
```

### ES6
``` bash
# 安装
yarn add babel-loader @babel/core @babel/preset-env -D
```
 
* @babel/preset-react 解析 jsx 语法

* @babel/preset-env 解析 es6 语法

* @babel/plugin-syntax-dynamic-import 解析 react-loadable 的 import 按需加载，附带 code spliting 功能。


``` js
{
  test: /\.js$/,
  use: {
    loader: 'babel-loader',
    query: {
      presets: [ '@babel/preset-env' ],
      cacheDirectory: true // 开启 babel 编译缓存
    }
  }
}
```

或者在根目录下新建 `.babelrc` 文件：

``` js
// .babelrc
{
    "presets": [
        '@babel/preset-env'
    ]
}
```

@babel/core、@babel/preset-env 只会将 ES6/7/8语法转换为ES5语法，但是对新api并不会转换。可以通过 babel-polyfill 对一些不支持新语法的客户端提供新语法的实现。

``` bash
yarn add @babel/polyfill -D
```

在 entry 中添加 @babel-polyfill：

``` js
entry: {
  app: ['@babel/polyfill', path.resolve(__dirname, 'src/index.js')]  
},
```

另外一种写法：

``` bash
yarn add babel-loader @babel/core @babel/preset-env @babel/plugin-transform-runtime @babel/runtime babel-plugin-transform-remove-console @babel/plugin-syntax-dynamic-import -D
```

``` js
// webpack.config.js 文件
const path = require('path');

module.exports = {
    module: {
        rules: [{
            test: /\.js$/,
            use: ['babel-loader'],
            exclude: /node_modules/, // 排除不要加载的文件夹
            include: path.resolve(__dirname, 'src') // 指定需要加载的文件夹
        }]
    }
};
```
``` js
// .babelrc 文件中 配置

{
  "presets": [
    [
      "@babel/preset-env", // 根据 preset-env 标准进行转换
      {
        "modules": false  // 不转换模块类型
      }
    ]
  ],
  "plugins": [
    "@babel/plugin-transform-runtime",  // 来处理全局函数和优化babel编译
    "transform-remove-console" // 打包时 移除 console 相关（开发环境下 不需要配置）
  ],
  "comments": false // 打包时 移除脚本中的注释 
}
```

### eslint
``` js
{
  enforce:'pre',
  test:/\.js$/,
  exclude:/node_modules/,
  include:resolve(__dirname,'/src/js'),
  loader:'eslint-loader'
}
```

### React
``` bash
# 安装
yarn add react react-dom
yarn add @babel/preset-react -D
```

``` js
{
  test: /\.(js|jsx)$/,
  use: {
    loader: 'babel-loader',
    query: {
      presets: [ "@babel/preset-env", "@babel/preset-react" ] 
    }
  }
}
```

### Vue
vue-loader 用于加载 .vue 后缀文件，vue-template-compiler 用于编译模板。

``` bash 
yarn add vue-loader vue-template-compiler -D
yarn add vue -S
``` 

``` js
const { VueLoaderPlugin } = require('vue-loader')

rules: [
  {
    test: /\.vue$/,
    loader: 'vue-loader'
  },
],

plugins: [
  new VueLoaderPlugin(), 
]
```

### Typescript 
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

注意 tsconfig.json 中模块解析策略使用： "module": "esnext"。

原因是 webpack 需要 es6 import 语句，才能进行 tree shaking 或者动态 import 优化，我们不再让 ts-loader 包办模块设置，换句话说，我们采用白名单方式看待 typescript 以及 babel，只让他做我们需要的工作，剩下的丢给 webpack 处理，可以获得最大程度性能优化。

如果仅使用 webpack + typescript，建议将 ts 编译输出模式调整为 es3，因为 webpack 自带的压缩工具对 es6 语法还存在报错，而且也不会做兼容处理。

#### 使用 typescript + babel + webpcak
注意处理顺序，ts -> babel -> webpack。

因为多出了 babel，我们将 ts 编译兼容模式关闭："target": "esnext"，模块也不要解析："module": "esnext"，ts-loader 仅仅将 typescript 代码转换成 js，其他一切优化都不要做，将 esnext 原生代码直接传给 babel 处理。

### 图片处理
file-loader 可以解析项目中的 url（不限于 css），会根据配置 将图片拷贝到相应的路径，打包时会根据规则修改引入路径。

url-loader 当图片较小的时候会把图片 BASE64 编码，大于 limit 参数的时候还是使用 file-loader 进行路径拷贝。

``` bash
# 安装
yarn add file-loader url-loader -D
```

``` js
// webpack.config.js 配置
output: {
  publicPath: 'dist/' // 同时要处理打包图片路径
},
module: {
  rules: [
    {
      test: /\.(png|jpg|jpeg|gif|svg)/,
      use: [{
        loader: 'url-loader',
        options: {
          outputPath: 'static/assets/', // 图片输出的路径
          name: '[name].[hash:8].[ext]',
          limit: 5 * 1024 // 文件体积小于 5120kb 时，将被转为 base64 资源
        }
      }]
    },
  ]
}
```

webpack 建议所有图片都使用背景图的方式，所以默认情况下 在html中使用 img 标签，会显示路径错误：

``` bash
yarn add html-withimg-loader@0.1.16 -D
```

``` js
// webpack.config.js 文件

module.exports = {
    module: {
        rules: [{
            test: /\.(htm|html)$/,
             use:[{
                loader: 'html-withimg-loader'
             }] 
        }]
    }
};
```

### 多媒体
``` js
rules: [
  {
    test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
    use: [{
      loader: 'url-loader',
      options: {
        name: '[name].[ext]',
        outputPath: 'static/assets/'  // 资源 输出路径
      }
    }]
  }
]
```

### 字体处理

``` js
// webpack.config.js 配置
module: {
  rules: [
    {
        test: /\.(eot|woff2?|ttf|otf|svg)$/,
        use: [
          {
            loader: "url-loader",
            options: {
              name: "[name].[ext]",
              limit: 5000, // fonts file size <= 5KB, use 'base64'; else, output svg file
              outputPath: "fonts/"
            }
          }
        ]
      }
  ]
}
```

## 插件
可以处理各种任务，从打包优化和压缩，一直到重新定义环境中的变量。

有些插件需要单独安装，有些插件是 webpack 内置插件 不需要单独安装，但所有的插件都 需要 在 webpack 配置文件中通过 require 引入。

### html-webpack-plugin
自动创建创建 HTML 文件。解决每次编译完成后不用再去手动修改 index.html, 它会与 JS 生成在同一目录 dist 并引入 main.38de904fed135db4bf0a.js。

``` bash
# 安装
yarn add html-webpack-plugin -D
```

``` js
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin');

,
plugins: [
  new HtmlWebpackPlugin({
    filename: 'index.html', // 文件名; 默认是index.html
    template: './src/index.html', // 指定模板html文件
    title: 'webpack-cli', // html文件 title标签的内容
    inject: true, // 自动引入JS脚本的位置，默认值为 true
    /*
      * 此选项有如下四种值:
       *
       * true/'body': JS脚本在 body标签底部 引入
       * 'head': JS脚本在 bead标签底部 引入
       * false: 不插入任何JS（几乎不会用到）
    */
    hash: true, // 默认值为 false, 值为true时，html 引入的脚本、scss都加hash值（清除缓存）
    cache: true, // 默认值为true, 设置缓存
    minify: {
      minifycss: true, // 压缩 css
      minifyJS: true, // 压缩 JS            
      minifyURLs: true, // 压缩 URL
      removeComments: true, // 去掉注释
      removeAttributeQuotes: true, // 去掉标签上 属性的双引号
      collapseWhitespace: true, // 去掉空行
      removeRedundantAttributes: true, // 去掉多余的属性
      removeEmptyAttributes: true // 去掉空属性
	 }
  }),
],
```

### 从 js 中分离 css
``` bash
# 安装
yarn add extract-text-webpack-plugin -D
```

``` js
// webpack.config.js 配置

const ExtractTextWebpackPligin = require('extract-text-webpack-plugin');
module: {
  rules: [
    {
      test: /\.css$/, // 转换文件的匹配正则
      use: ExtractTextWebpackPlugin.extract({
        fallback: 'style-loader',
        // 如果需要，可以在 sass-loader 之前将 resolve-url-loader 链接进来
        use: ['css-loader']
      })
    },
    {
      test: /\.less$/,
      use: ExtractTextWebpackPligin.extract({
        use: [
          { loader: 'css-loader' }, 
          { loader: 'less-loader' }
        ]
      })
    }
  ],
  
  // plugin
  plugins: [
    new ExtractTextWebpackPlugin({
      filename: 'css/[name].[hash].css' // 命名
    })
  ]
}
```

未来使用 mini-css-extract-plugin 替代 extract-text-webpack-plugin@next。

``` bash
yarn add mini-css-extract-plugin -D
```

``` js
const MiniCssTractPlugin = require('mini-css-extract-plugin');

module: {
	rules: [
	  {
	    test: /\.css$/,
	    use: [ MiniCssTractPlugin.loader,'css-loader' ]
	  },
	  {
        test: /\.scss$/,
        use: [ MiniCssTractPlugin.loader, 'css-loader', 'sass-loader']
        },
	  {
	    test: /\.less$/,
	    use: [ MiniCssTractPlugin.loader, 'css-loader', 'less-loader' ]
	  }
	]
},

plugins: [
  //...
  new MiniCssTractPlugin({
    filename: 'static/css/[name].[hash:8].css',
  }),
],
```

### 抽取公共代码
分离主要有三种方式：

* 入口起点：使用 entry 配置手动地分离代码。
* 防止重复：使用 SplitChunksPlugin 去重和分离 chunk。
* 动态导入：通过模块中的内联函数调用来分离代码。

SplitChunks 可以将公共的依赖模块提取到已有的 entry chunk 中，或者提取到一个新生成的 chunk。

``` js
module.export = {
  optimization: {
    splitChunks: {
       chunks: 'all'
    }
  }
}
```

通过设置 optimization.splitChunks.chunks: "all" 来启动默认的代码分割配置项。

通过设置 optimization.runtimeChunk: true 来为每一个入口默认添加一个只包含 runtime 的 chunk(webpack 会添加一个只包含运行时(runtime)额外代码块到每一个入口)。

``` js
module.export = {
  optimization: {
  runtimeChunk: true,  // 设置为 true, 一个chunk 打包后就是一个文件，一个 chunk 对应`一些 js css 图片等
    splitChunks: {
      cacheGroups: {
        commons: {
          chunks: 'all',
          minChunks: 2,
          maxInitialRequests: 5, // The default limit is too small to showcase the effect
          minSize: 0, // This is example is too small to create commons chunks
          name: 'common'
        },
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          minChunks: 2,
          enforce: true
        }
      }
    }
  }
}
```

### 分离第三方库
``` js
entry: {
  app: './src/index.js',
  ramda: ['ramda'],
}

new HtmlWebpackPlugin({
  template: './index.html',
  chunks: ['app', 'commons', 'ramda']
})
```

### js 优化
``` bash
# 安装
yarn add webpack-parallel-uglify-plugin -D
```

``` js
const WebpackParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')
plugins: [
new WebpackParallelUglifyPlugin({
      uglifyJS: {
        output: {
          beautify: false, //不需要格式化
          comments: false //不保留注释
        },
        compress: {
          warnings: false, // 在UglifyJs删除没有用到的代码时不输出警告
          drop_console: true, // 删除所有的 `console` 语句，可以兼容ie浏览器
          collapse_vars: true, // 内嵌定义了但是只用到一次的变量
          reduce_vars: true // 提取出出现多次但是没有定义成变量去引用的静态值
        }
      }
    })
],
```

### 对样式文件进行压缩
``` bash
npm install optimize-css-assets-webpack-plugin -D
```

``` js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')

plugins: [
	cssExtract,
	lessExtract,
	new OptimizeCSSAssetsPlugin(),
	...
],

// 配置
new OptimizeCSSAssetsPlugin ({
	// 默认是全部的CSS都压缩，该字段可以指定某些要处理的文件
	assetNameRegExp: /\.(sa|sc|c|le)ss$/g, 
	// 指定一个优化css的处理器，默认cssnano
	cssProcessor: require('cssnano'),
	cssProcessorPluginOptions: {
	preset: [  'default', {
		discardComments: { removeAll: true}, //对注释的处理
		normalizeUnicode: false // 建议false,否则在使用unicode-range的时候会产生乱码
	}]
	},
	canPrint: true  // 是否打印编译过程中的日志
}),
```

### 复制静态资源
``` bash
# 安装
yarn add copy-webpack-plugin
```

``` js
const CopyWebpackPlugin = require('copy-webpack-plugin')
new CopyWebpackPlugin([
  {
    from: path.resolve(__dirname, 'static'),
    to: path.resolve(__dirname, 'pages/static'),
    ignore: ['.*']
  }
])
```

### 按需加载
第一步：添加 chunkFilename 配置，给拆分后的 chunk 们起名字。

``` js
module.exports = {
  entry:'./a.js',
  output:{
    filename:'[name].js',
    chunkFilename:'[name].js'// 设置按需加载后的 chunk 名字
  }
}
```

第二步：使用 import() 来实现动态加载：

``` js
import('./b').then()

// 或者
import(/* webpackChunkName: "b" */ './b').then() // 注释就是为chunk命明的方式
```

第三步：配置 Public Path：

``` js
output:{
  filename:'[name].js',
  chunkFilename:'[name].js',// 设置按需加载后的 chunk 名字
  publicPath:'dist/' // 设置公共路径
}
```

按需加载 React 组件：

``` js
// .babelrc
{
  "plugins": [
    "@babel/plugin-syntax-dynamic-import"
  ]
}
```

### 清空打包输出目录
``` bash
# 安装
yarn add clean-webpack-plugin -D
```

``` js
const CleanWebpackPlugin = require('clean-webpack-plugin')

plugins: [ 
  // 默认情况下，此插件将删除 webpack output.path目录中的所有文件，以及每次成功重建后所有未使用的 webpack 资产
  new CleanWebpackPlugin(['./dist']), 
  new HtmlWebpackPlugin({ 
    title: 'webpack-demo', 
    template: resolve('../public/index.html') 
  }) 
],
```

### 暴露全局变量
``` js
new Webpack.ProvidePlugin({
  '$': 'jquery'
}),
```

### 热更新
使用 webpack 自带的热更新的插件:

``` js
const webpack = require('webpack');

//...
devServer: {
  contentBase: path.resolve(__dirname,'dist'),
  compress: true,
  host: 'localhost',   
  port:3000,
  hot: true // 热更新,必要的配置
},
plugins: [
  new webpack.NamedModulesPlugin(), // 必要的配置
  new webpack.HotModuleReplacementPlugin() // 必要的配置
]
```

然后在主模块中添加代码：

``` js
// index.js
if (module.hot) { // 开启热替换
  module.hot.accept()
}
```

### 多页配置
``` js
  entry: { // entry 是一个字符串或者数组都是单页，是对象则是多页，这里是 index 页和 base 页
    index: './pages/index/index.js',
    base: './pages/base/base.js'
  }
  
 //  一个页面对应一个 html 模板，在 plugin 中
  plugins: [
    new HtmlWebpackPlugin({
      template: './pages/index/index.html',
      filename: 'pages/index.html',
      hash: true,
      chunks: ['index', 'common'],
      minify: {
        removeAttributeQuotes: true
      }
    }),
    new HtmlWebpackPlugin({
      template: './pages/base/base.html',
      filename: 'pages/base.html',
      hash: true,
      chunks: ['base', 'common'],
      minify: {
        removeAttributeQuotes: true
      }
    }),
  ]
```


