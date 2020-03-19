# 第三方模块 
## 设置 Node 路径
当你将应用划分为多个模块时，使用相对路径来引入另一个模块将会非常繁琐：

``` js
const app = require('../../app');
```

而更加简便的方法就是：把你的应用文件放在名为 source 或者 src 的目录中，然后通过 `cross-env` 来设置 `NODE_PATH` 环境变量，使他们可以跨平台使用（可以在 Windows 下读取并运行应用）。

``` bash
npm install --save cross-env
```

之后，在 `package.json` 脚本中设置环境变量：

``` json
"scripts": {
  "start": "cross-env NODE_PATH=source node source/server.js",
  "debug": "cross-env NODE_PATH=source node --debug-brk --inspect source/server.js",
  "test": "cross-env NODE_PATH=source node source/test/index.js"
}
```

然后引入模块：

``` js
const app = require('app');
```

## 进程管理( PM2)
``` bash
# 安装
npm install -g pm2

# 运行
pm2 start source/app.js
```

你可以用 `pm2 list` 管理运行的应用实例，也可以使用 pm2 stop 来终止实例。

## gzip 压缩
服务端开启 gzip 压缩一般的 css、js 文件能压缩 60、70% 。如果前端部署用node、express作服务器的话，使用中间件 compression 即可开启 gzip 压缩。

``` js
// server.js
var express = require('express');
var compression = require('compression');
var app = express();
app.use(compression());
```