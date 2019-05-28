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
|   |-- check-version.js             // 检查 node、npm 等版本
|   |-- dev-client.js                // 热重载相关
|   |-- dev-server.js                // 构建本地服务器
|   |-- utils.js                     // 构建工具相关
|   |-- vue-loader.conf.js           // vue-loader 基础配置
|   |-- webpack.base.conf.js         // webpack 基础配置
|   |-- webpack.dev.conf.js          // webpack 开发环境配置
|   |-- webpack.prod.conf.js         // webpack 生产环境配置
|-- config                           // 项目开发环境配置
|   |-- dev.env.js                   // 开发环境变量
|   |-- index.js                     // 项目一些配置变量
|   |-- prod.env.js                  // 生产环境变
|   |-- test.env.js                  // 测试环境变量
|-- node_modules                     // 存放 npm 下载的模块
|-- src   这是我们经常会用的一个文件夹    // 源码目录
|   |-- assets                       // 存放静态资源文件会被 webpack 处理解析为模块依赖
|   |-- components                   // vue 公共组件
|   |-- store                        // vuex 的状态管理
|   |-- router                       // 路由管理文件
|   |-- App.vue                      // 页面入口文件
|   |-- main.js                      // 程序入口文件，加载各种公共组件
|-- static                           // 静态文件，比如一些图片，json 数据等
|-- test                             // 测试文件
|-- .babelrc                         // ES6 语法编译配置
|-- .postcssrc                       // postcss 配置
|-- .editorconfig                    // 编辑器配置，定义代码格式
|-- .gitignore                       // git 上传需要忽略的文件格式
|-- README.md                        // 项目说明
|-- favicon.ico 
  |-- index.html                     // 入口页面
|-- package.json                     // 项目基本信息
|-- package-lock.json                // 锁定安装时的包的版本号

static 放不会变动的文件 assets 放可能会变动的文件。
```

## vue-cli 3.0
vue-cli 3.0 的名字现在已经改为 @vue/cli 了。

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

第一次选择的时候只有两个选项：default(默认配置)和 Manually select features(手动配置)。

### 启动项目
``` bash
# 先进入项目，然后运行
yarn serve 
# or
npm run serve
```

然后打开 http://localhost:8080 预览。

### 插件
现有的项目中安装插件，但是需要注意的是该命令安装的包是以 @vue/cli-plugin 或者 vue-cli-plugin 开头，即只能安装 Vue 集成的包。

``` bash
vue add @vue/eslint
# 会解析为完整的包名 @vue/cli-plugin-eslint
```

如果安装的插件不存在，则会失败。

另外 vue add 中还有两个特例，如下：

``` bash
# 安装 vue-router
vue add router

# 安装 vuex
vue add vuex
```

这两个命令会直接安装 vue-router 和 vuex 并改变你的代码结构。

### 配置
与 vue-cli2.0 的很大区别是根目录下的 webpack 配置的目录不见了，如果需要自定义配置，则需要在根目录下创建 vue.config.js 文件，包括了配置 常用的输出路径名、跟目录、预处理、devServer 配置、pwa、dll、第三方插件等。 [官网配置](https://cli.vuejs.org/zh/config/)

``` js
  // vue.config.js
  module.exports = {
    // 去掉文件名中的 hash
    filenameHashing: false,
    // 如果你的应用被部署在 https://www.my-app.com/my-app/，则设置 publicPath 为 /my-app/
    publicPath: '/', // 部署应用包时的基本 URL，Default: '/'
    // publicPath: '/blog' 如果部署到 https://www.faychou.com/blog
    outputDir: 'dist', // 生产环境构建文件的目录，Default: 'dist'
    assetsDir: '', // 放置生成的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录，默认 ''
    devServer: {
      // proxy: 'http://localhost:4000' // 将请求代理到该地址
      proxy: {
        '/api': '/api': 'http://localhost:3000' // 将 api/users 请求代理到 http://localhost:3000/api/users
        // '/api': { // 请求中不带 api
	       // target: 'http://localhost:3000',
	       // pathRewrite: {'^/api' : ''}
	     // }
      }
    }
  }
```

### 图形化界面
3.0 还有一个最大的优化在于实现了图形界面化创建和操作项目。

启动：

``` bash
vue ui
```

### 零配置启动一个 vue
第一步安装模块：

``` bash
npm install -g @vue/cli-service-global
```

第二步：新建文件夹，并建立 app.vue：

``` html
<template>
  <h1> {{msg}} </h1>
</template>

<script>
  export default {
    data: {
      msg: 'hello world!'
    }
  }
</script>
```

第三步，启动文件：

``` bash
vue serve App.vue # 启动服务

vue build App.vue # 打包出生产环境的包并用来部署
```

通过这个功能，可以只用一个 vue 文件就能建立一个项目，不需要其他任意的配置，非常适合用于开发一个库、组件，做一些小 demo 等。

## 开发工具
vue-devtools