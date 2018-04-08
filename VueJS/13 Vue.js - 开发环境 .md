# Vue 开发环境
	
## 手动创建 Vue 的开发环境
vue-loader 是必须的，用于解析后缀名为 `.vue` 的文件，然后转换为 JavaScript 模块。vue-template-compiler 是 vue-loader 必须的依赖。

``` bash
npm install --save-dev vue-loader vue-template-compiler
```

``` js
//webpack 配置
module: {
  rules: [
    {
      test: /\.vue$/,
      loader: 'vue-loader',
      options: {
        loaders: {
          css: ["vue-style-loader", "css-loader"],
          less: ["vue-style-loader", "css-loader", "postcss-loader", "less-loader"]
        },
        cssSourceMap: true
      }
    },
    //使用vue-style-loader!css-loader!postcss-loader 处理以 css 结尾的文件！
    {
      test: /\.css$/,
      use: [
        "vue-style-loader",
          {
            loader: "css-loader",
	         options: {
	         sourceMap: true
	       }
	     },
	     {
	       loader: "postcss-loader",
	       options: {
	         sourceMap: true
	       }
	     }
	   ]
	 },
	 //使用vue-style-loader!css-loader!postcss-loader处理以less结尾的文件！
    {
      test: /\.less$/,
      use: [
        "vue-style-loader",
        {
          loader: "css-loader",
          options: {
            sourceMap: true
          }
        },
        {
          loader: "less-loader",
          options: {
            sourceMap: true
          }
        },
        {
          loader: "postcss-loader",
          options: {
            sourceMap: true
          }
        }
      ]
    }
  ]
}
```

## 脚手架：vue-cli
Vue.js 提供一个官方命令行工具，可用于快速搭建大型单页应用。

	# 全局安装 vue 的脚手架	
	npm install -g vue-cli
	
	# 查看版本号，判断是否安装成功
	vue -V
	
	# 创建一个基于 webpack 模板的新项目，项目名字叫 my-project
	vue init webpack my-project
	cd my-project
	
	# 安装依赖（这里一定要从官方仓库安装，而不是国内镜像cnpm安装否则会导致后面缺了很多依赖库）
	npm install
	
	# 运行
	npm run dev
	
> 注意，目前提供了4套官方模板：

* browserify
* browserify-simple
* webpack
* webpack-simple（初始是没有vue-router的，需要另外安装）

目录结构：

```
|-- build                            // 项目构建(webpack)相关代码
|   |-- build.js                     // 生产环境构建代码
|   |-- check-version.js             // 检查node、npm等版本
|   |-- dev-client.js                // 热重载相关
|   |-- dev-server.js                // 构建本地服务器
|   |-- utils.js                     // 构建工具相关
|   |-- vue-loader.conf.js           // vue-loader基础配置
|   |-- webpack.base.conf.js         // webpack基础配置
|   |-- webpack.dev.conf.js          // webpack开发环境配置
|   |-- webpack.prod.conf.js         // webpack生产环境配置
|-- config                           // 项目开发环境配置
|   |-- dev.env.js                   // 开发环境变量
|   |-- index.js                     // 项目一些配置变量
|   |-- prod.env.js                  // 生产环境变
|   |-- test.env.js                  // 测试环境变量
|-- node_modules                     // 存放 npm 下载的模块
|-- src   这是我们经常会用的一个文件夹  // 源码目录
|   |-- assets                     // 存放静态资源文件会被webpack处理解析为模块依赖
|   |-- components                     // vue公共组件
|   |-- store                          // vuex的状态管理
|   |-- router                         // 路由管理文件
|   |-- App.vue                        // 页面入口文件
|   |-- main.js                        // 程序入口文件，加载各种公共组件
|-- static                           // 静态文件，比如一些图片，json数据等
|-- test                             // 测试文件
|-- .babelrc                         // ES6语法编译配置
|-- .editorconfig                    // 定义代码格式
|-- .gitignore                       // git上传需要忽略的文件格式
|-- README.md                        // 项目说明
|-- favicon.ico 
  |-- index.html                       // 入口页面
|-- package.json                     // 项目基本信息

static放不会变动的文件 assets放可能会变动的文件。
```

## vue-cli 3.0
### 安装
``` bash
npm install -g @vue/cli
# or
yarn add global @vue/cli
```

### 创建项目
``` bash
vue create <project-name>
```

这里根据提示选择即可，注意新版本把插件以及模板等移植到命令行界面了。

### 启动项目
``` bash
# 先进入项目，然后运行
yarn serve 
# or
npm run serve
```

### 区别
大部分配置都集成到 vue.config.js 这里了，包括了配置 常用的输出路径名、跟目录、预处理、devServer 配置、pwa、dll、第三方插件等。

## 开发工具
vue-devtools