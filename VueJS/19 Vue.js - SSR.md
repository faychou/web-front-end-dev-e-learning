# Vue 服务器渲染
所谓的 Vue 服务端渲染就是，将 vue 实例在服务端渲染成 HTML 字符串，将它们直接发送给浏览器，最后将静态标记“混合”为客户端上完全交互的应用程序。

### 服务端渲染（SSR） VS 预渲染（Prerendering）
相同点：都是解决单页面 SEO 的问题，更快的内容到达时间。

不同点：

* 1、实现原理和方案不同：SSR 的实现依赖于 node.js 服务器做服务端构建静态资源，   prerender 的实现依赖于 webpack 集成为 prerender-spa-plugin，将静态资源提取出来展示给前端。

* 2、服务端渲染可以做到服务端的实时编译，prerender 只是在构建时简单的生成针对特定路由的静态 HTML 文件，来达到 SEO 的效果，prerender 的优点是配置更简单，并可以把前端作为一个完全静态的站点。

#### Prerender 的 webpack 配置
``` js
const path = require('path')
const PrerenderSPAPlugin = require('prerender-spa-plugin')

module.exports = {
  plugins: [
    ...
    new PrerenderSPAPlugin({
      // Required - The path to the webpack-outputted app to prerender.
      staticDir: path.join(__dirname, 'dist'),
      // Required - Routes to render.
      routes: [ '/', '/about', '/some/deep/nested/route' ],
    })
  ]
}
```

### hello world 示例
``` js
const Vue = require('vue')
const server = require('express')()

// 创建一个 renderer
const renderer = require('vue-server-renderer').createRenderer()
server.get('*', (req, res) => {
  // 创建一个 Vue 实例
  const app = new Vue({
    data: {
      url: req.url
    },
    template: `<div>访问的 URL 是： {{ url }}</div>`
  })
  
  // 将 Vue 实例渲染为 HTML
  renderer.renderToString(app, (err, html) => {
    if (err) {
      res.status(500).end('Internal Server Error')
      return
    }
    res.end(`
      <!DOCTYPE html>
      <html lang="en">
        <head><title>Hello</title></head>
        <body>${html}</body>
      </html>
    `)
  })
})
server.listen(8080);
```

### 编写通用代码注意事项
* 服务端渲染过程中只有 beforeCreate 和 created 生命周期函数会被调用，其他生命周期只能在浏览器环境调用；

* 通用代码不可接受特定平台的 API（比如 document、window），使用跨平台的 axios；

* 大多数自定义指令直接操作 DOM，因此会在服务器端渲染(SSR)过程中导致错误。

## Nuxt.js
于 Vue 生态的更高层的框架，为开发服务端渲染的 Vue 应用提供了极其便利的开发体验。
