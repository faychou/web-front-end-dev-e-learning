# Vue 开发环境
	
## 手动创建 Vue 的开发环境
vue-loader 是必须的，vue-template-compiler 是 vue-loader 必须的依赖。

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

## 开发工具
vue-devtools