# 内置模块-`readline`
是一个流内容的逐行读取模块，你可以用 `readline` 模块来读取 `stdin`，可以用来逐行读取文件流，也可用它来在控制台和用户进行一些交互。

``` js
const readline = require('readline')

const rl = readline.createInterface({
  // 监听的可读流
  input: process.stdin,
  // 逐行读取（Readline）数据要写入的可写流
  output: process.stdout
})

rl.question('你认识哪些课程内容设置不合理？', answer => {
  console.log(`感谢您的宝贵意见：${answer}`)
  rl.close()
})
```

