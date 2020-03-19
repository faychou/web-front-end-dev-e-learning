# 编写 RESTful API
广义的 API（Application Programming Interface）是指应用程序编程接口，包括在操作系统中的动态链接库文件例如 dll\so，或者基于 TCP 层的 socket 连接，用来提供预定义的方法和函数，调用者无需访问源码和理解内部原理便可实现相应功能。而当前通常指通过 HTTP 协议传输的 web service 技术。

简单来说 Restful 定义 URI 和 HTTP 状态码，让你的 API 设计变得更简洁、清晰和富有层次，对缓存等实现更有帮助。一个 RESTful API 应该不以服务器/客户端的状态改变而改变。通过使用一个 REST 接口，不同的客户端，即使它们的状态各不相同，但是在访问相同的 REST 终端时，应该做出同一种动作，并且接收到相同的数据。

编写一个 RESTful API 涉及到使用 JSON 或是 XML 格式传输数据。

### RESTful 原则
* 有一个标准 URL（如：http://faychou.com/api/article 或 http://faychou.com/api/articles/:article_id）；

* 使用适当的 HTTP 请求方法使其 RESTful(GET, POST, PUT, 和 DELETE)；

* 返回  JSON 数据；

* 将所有请求 log 到控制台

### 正确使用 HTTP Method 和路由
* POST /users 或者 PUT /users/:id 用来创建新用户；
* GET /users 用来获取用户列表；
* GET /users/:id 用来获取单个用户；
* PATCH /users/:id 用来更新用户信息；
* DELETE /users/:id 用来删除用户；

## 案例
### 目录结构
``` bssh
-app/
-----models/                //模块
----------blog.js
----------category.js
----------user.js
-----routes/                //路由
----------blog.js
----------category.js
----------comment.js
----------setup.js
----------user.js
-node_modules/              //依赖环境安装后生成
-config.js                  //一些配置
-package.json               //项目依赖环境(初始化后自动生成)
-server.js                  //项目启动文件
```

### 启动文件
``` js
// server.js

const express = require('express'); 
const app = express(); 
const bodyParser = require('body-parser');

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

const port = process.env.PORT || 8080; // 端口

// 路由
// =============================================================================
const router = express.Router(); 

// 测试 API
router.get('/', function(req, res) {
  res.json({ message: 'Welcome to my api!' });   
});

// more routes for our API will happen here

// REGISTER OUR ROUTES -------------------------------
app.use('/api', router);

// 启动
// =============================================================================
app.listen(port);
console.log('The server is starting on port ' + port);
```

