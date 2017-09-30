# webpack
## 插件
### HtmlWebpackPlugin
这个插件能在构建过程中自动在你的HTML文件里插入对构建文件的引用。

``` js
new HtmlWebpackPlugin({
  filename: 'index.html'
  template: 'index.html',
  inject: true,
  chunksSortMode: 'dependency'
})
```

> 注意：去掉 index 文件里 js 的引用，带有 hash 值的构建文件将会自动增加到 index 文件。