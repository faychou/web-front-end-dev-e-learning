# Vue 挖坑
## 跨域问题
1、 CORS , 前后端都要对应去配置,IE10+

2、 nginx 反向代理,一劳永逸 <-- 线上环境可以用这个

3、 如果是使用 vue-cli 创建的环境：

``` js
// 在 config 目录下的index.js

proxyTable: {
  "/bp-api": {
    target: "http://new.d.st.cn",
    changeOrigin: true,
    // pathRewrite: {
    //   "^/bp-api": "/"
    // }
  }
}

// target : 就是 api 的代理的实际路径
// changeOrigin: 就是是变源,必须是...
// pathRewrite : 就是路径重定向,一看就知道
```