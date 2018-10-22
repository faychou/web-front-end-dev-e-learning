# webpack-V4

### 代码进行拆包，抽取出公共代码
``` js
module.export = {
  // ...
  optimization: {
    splitChunks: {
      chunks: 'all'
    }
  }
}
```

上面的 chunks 配置建议配置为 all 。