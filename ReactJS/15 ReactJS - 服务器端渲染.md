# 服务器端渲染(SSR)
在学习 react 服务器端渲染之前，首先来了解一个概念--同构。

### 什么是同构？

一套代码既可以在服务端运行又可以在客户端运行，这就是同构应用。简而言之, 就是服务端直出和客户端渲染的组合, 能够充分结合两者的优势，并有效避免两者的不足。

### 为什么同构？
* 性能: 通过 Node 直出, 将传统的三次串行 http 请求简化成一次 http 请求，降低首屏渲染时间；

* SEO: 服务端渲染对搜索引擎的爬取有着天然的优势。

* 兼容性: 部分展示类页面能够有效规避客户端兼容性问题，比如白屏。

### 生态
* [react-server](https://react-server.io): React 服务端渲染框架；

* [Next.js](https://github.com/zeit/next.js): 是一个用于 React 应用的极简的服务端渲染框架。

* [beidou](https://github.com/alibaba/beidou): 阿里自己的同构框架，基于eggjs, 定位是企业级同构框架。

## Next.js
### 安装
``` bash
npm install --save next react react-dom
```

Next.js 4 只支持 React 16。

### 运行
首先在 package.json 中添加以下代码：

``` json
{
  "scripts": {
    "dev": "next"
  }
}
```

接着打开终端，进入项目目录并运行：

``` bash
npm run dev
```

最后打开浏览器 http://localhost:3000 ，此时我们会看到 404 页面。

此时在项目中新建一个 `./pages/index.js` 文件：

``` js
const Index = () => (
  <div>
    <p>Hello Next.js</p>
  </div>
)

export default Index
```

然后再次访问 http://localhost:3000 ，就可以看到 “Hello Next.js” 了。所以我们可以知道 Next.js 以 `./pages` 目录作为页面渲染目录的的服务器端渲染。

另外一点，如果我们的代码写错了，Next.js 会帮我们在浏览器中显示出来。

### 静态文件服务
在项目的根目录新建 static 文件夹，然后就可以通过 `/static/` 开头的路径来引用此文件夹下的文件：

``` js
export default () => <img src="/static/logo.png" />
```


