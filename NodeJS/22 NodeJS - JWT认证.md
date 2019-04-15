# JWT
## 前话 - 前端密码加密是否有意义？
首先我们明确知道走 HTTPS 是目前唯一负责的方式。但在 HTTP 环境下，无论如何都可能会被劫持流量，不管前端做不做加密都会被轻易成功登录。这个时候保护密码明文是否有意义？

首先，前端系统的控制权是完全在用户手里的，也就是说，前端做什么事情，用户有完全的控制权。如果是前端做过了 md5 加密，而后端没有做加密，一旦数据库泄露了，黑客就直接拿到了每个用户的密码 md5 值，由于黑客知道密码是在前端进行哈希的，所以他不需要爆破出该 md5 对应的原文是什么，而是直接修改客户端向服务器发出的请求，把密码字段换成数据库中 md5 就可以了，由于与数据库中记录一致，直接就会登录成功。这跟直接存储明文密码没有任何区别！所以不管前端是不是加密了密码，后台使用安全的哈希算法对内容再次转换是非常有必要的。（当然 md5 可能不再满足你的要求，需要用到 bcrypt）

其次，前端加密的原因是不让你的账户密码被泄露，因为劫持者可以拿着你的用户密码去碰撞其他资源，绝大多数人都是同一套账户密码。当然即使只能拿到加密后的密码，也可以采用 HASH 碰撞来逆推密码，不过这个成本就高了，而且不同的网站会有不同的加密算法，逆推不一定有意义。

所以一般第一步：前端使用 md5 来加密（除了 md5 还有 sha1.js，base64.js 等）：

``` bash
#安装
npm install md5
```
``` js
//使用
var md5 = require('md5');
 
console.log(md5('message'));
```

第二步、后端收到密码后，也要先进行加密，一般为了添加安全性，会额外在密文的基础上加盐再做一次加密然后存入数据库。

## Token 认证
Token 是在服务端产生的。如果前端使用用户名/密码向服务端请求认证，服务端认证成功，那么在服务端会返回 Token 给前端。前端可以在每次请求的时候带上 Token 证明自己的合法地位。如果这个 Token 在服务端持久化（比如存入数据库），那它就是一个永久的身份令牌。

而为了安全考虑 Token 都需要设有效期。一般有效期越短越好，但也要符合用户习惯。如果用户在正常操作的过程中，Token 过期失效了，要求用户重新登录。为了解决在操作过程不能让用户感到 Token 失效这个问题，有一种方案是在服务器端保存 Token 状态，用户每次操作都会自动刷新（推迟） Token 的过期时间——Session 就是采用这种策略来保持用户登录状态的。然而仍然存在这样一个问题，在前后端分离、单页 App 这些情况下，每秒种可能发起很多次请求，每次都去刷新过期时间会产生非常大的代价。如果 Token 的过期时间被持久化到数据库或文件，代价就更大了。所以通常为了提升效率，减少消耗，会把 Token 的过期时保存在缓存或者内存中。

还有另一种方案，使用 Refresh Token，它可以避免频繁的读写操作。这种方案中，服务端不需要刷新 Token 的过期时间，一旦 Token 过期，就反馈给前端，前端使用 Refresh Token 申请一个全新 Token 继续使用。这种方案中，服务端只需要在客户端请求更新 Token 的时候对 Refresh Token 的有效性进行一次检查，大大减少了更新有效期的操作，也就避免了频繁读写。当然 Refresh Token 也是有有效期的，但是这个有效期就可以长一点了，比如，以天为单位的时间。

## JWT的构成
JWT（json web token）是为了在网络应用环境间传递声明而执行的一种基于 JSON 的开放标准。一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，比如用在用户登录上。

在传统的用户登录认证中，因为 http 是无状态的，所以都是采用 session 方式。用户登录成功，服务端会保证一个 session，当然会给客户端一个 sessionId，客户端会把 sessionId 保存在 cookie 中，每次请求都会携带这个 sessionId。

而 JWT 只需要服务端生成 token，客户端保存这个 token，每次请求携带这个 token，服务端认证解析就可。

#### JWT生成编码后的样子：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiend6IiwiYWdlIjoiMTgifQ.UQmqAUhUrpDVV2ST7mZKyLTomVfg7sYkEjmdDI5XF8Q
```

从上面可以看出 JWT 由三部分组件，分别是 头部（header),载荷（payload) 以及 签证（signature)。

### header
* 令牌类型：如 jwt；
* 加密的算法：默认为 HMAC SHA256（HS256）或者 RSA。

完整的头部就像是下面这样的一个 JSON 对象：

``` json
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```

然后将头部进行 base64 加密（该加密是可以对称解密的),构成了第一部分：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

### playload
载荷就是存放有效信息的地方。总共提供了七个可选字段：

* iss: jwt 签发者
* sub: jwt 所面向的用户
* aud: 接收 jwt 的一方
* exp: jwt 的过期时间，这个过期时间必须要大于签发时间
* nbf: 生效时间
* iat: jwt 的签发时间
* jti: 编号，jwt 的唯一身份标识，主要用来作为一次性 token ,从而回避重放攻击

除了这几个可选字段外，我们还可以在这个部分定义私有字段：

``` json
{
  "sub": "admin",
  "exp": 1441594722,
  "name": "zf",
  "admin": true
}
```

然后将其进行 base64 加密，得到 JWT 的第二部分：

```
eyJuYW1lIjoiend6IiwiYWdlIjoiMTgifQ
```

> 注意：JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

### signature
这个部分是对前两个部分进行 base64 加密，再用 Header 里面指定的签名算法加密，加密的时候要指定一个 Secret ，这个相当于是一个密码，该密码存储在服务端，不能泄露给用户用于防止数据篡改。

最后把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用 `.` 分隔，然后返回给用户：

``` js
var encodedString = base64UrlEncode(header) + "." + base64UrlEncode(payload); 
HMACSHA256(encodedString, 'secret');
```

``` bash
# 处理完成以后看起来像这样：
UQmqAUhUrpDVV2ST7mZKyLTomVfg7sYkEjmdDI5XF8Q
```

客户端收到这个 Token 以后把它存储下来，下回向服务端发送请求的时候就带着这个 Token 。服务端收到这个 Token ，然后进行验证，通过以后就会返回给客户端想要的资源。

## 基于 Token 的身份验证方法
### 流程：

* 客户端使用用户名跟密码请求登录
* 服务端收到请求，去验证用户名与密码
* 验证成功后，服务端会签发一个 Token，再把这个 Token 发送给客户端
* 客户端收到 Token 以后可以把它存储起来，比如放在 Cookie 里或者 Local Storage 里
* 客户端每次向服务端请求资源的时候需要带着服务端签发的 Token
* 服务端收到请求，然后去验证客户端请求里面带着的 Token，如果验证成功，就向客户端返回请求的数据

如果想自己生成 JWT 会有点复杂，所以可以直接利用第三方库来支持 JWT。如 ： jsonwebtoken。

### jsonwebtoken 的使用
``` bash
# 安装
npm install jsonwebtoken
```

jsonwebtoken 主要提供两个方法：

* sign 用于生成 token

* verify 用于检验 token

#### sign
``` js
var jwt = require('jsonwebtoken');
// 语法 
jwt.sign(payload, secretOrPrivateKey, [options, callback])
```

* payload：必须是一个 object, buffer 或者 string。请注意， exp 只有当 payload 是 object 字面量时才可以设置。

* secretOrPrivateKey：是包含 HMAC 算法的密钥或 RSA 和 ECDSA 的 PEM 编码私钥的 string 或 buffer。

* options 为配置对象：
  
  * algorithm: 加密算法，默认值 HS256
  * expiresIn：过期时间，如果值为数值，则默认为多少秒，如果值为字符串，请提供时间单位（"2 days", "10h", "7d"），否则默认为毫秒，如 "120" 等于“120ms”

在 expiresIn，notBefore，audience，subject，issuer 没有默认值时。也可以直接在 payload 中用 exp，nbf，aud，sub 和 iss 分别表示，但是你不能在这两个地方同时设置。
  
#### verify
验证 token 的合法性。

``` js
jwt.verify（token，secretOrPublicKey，[options，callback]）
```

但是 jsonwebtoken 提供的 verify 比较简单，如果想要实现更加复杂的验证，可以通过 express-jwt 来实现。

### express-jwt
验证指定 http 请求的 JsonWebTokens 的有效性。

``` bash
npm install express-jwt
```

``` js
var jwt = require('express-jwt');

app.use(jwt({ 
  secret: 'secretOrPrivateKey' // 加密 token 的秘钥
}).unless({ // 选择一些不受保护的路径
  path: ['/token']
}));
```

jsonwebtoken 是用来生成 token 给客户端的，express-jwt 是用来验证token的。

#### 校验 token 失败时的处理
``` js
app.use(function (err, req, res, next) {
  if (err.name === 'UnauthorizedError') {   
      // 具体的 err 值 请看下面
    res.status(401).send('invalid token...');
  }
});

// token 过期时的 err 值：
{
    "name": "UnauthorizedError",
    "message": "jwt expired",
    "code": "invalid_token",
    "status": 401,
    "inner": {
        "name": "TokenExpiredError",
        "message": "jwt expired",
        "expiredAt": "2017-08-03T10:08:44.000Z"
    }
}

// token 无效时的 err 值：
{
    "name": "UnauthorizedError",
    "message": "invalid signature",
    "code": "invalid_token",
    "status": 401,
    "inner": {
        "name": "JsonWebTokenError",
        "message": "invalid signature"
    }
}
```

### express 中使用

``` bash
# 安装
npm install express-jwt --save
npm install jsonwebtoken --save
```

``` js
//app.js 配置
const express = require('express');
const ejs = require('ejs');
const path = require('path');
const bodyParser = require('body-parser');
const logger = require('morgan');

const app = express();

const routes = require('./routes');

// 模版引擎设置
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

// 每一次服务请求都会将信息打印在控制台中
app.use(logger('dev'));

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

//设置静态资源
app.use(express.static(path.join(__dirname, 'public')));

routes(app); // 路由引入

app.listen(3000);
```

``` js
// routes/index.js 配置
const expressJWT = require('express-jwt');
const util = require('../util/util');

module.exports = (app) => {
  // 设置需要保护的 API，这里需要设置 secret 作为秘钥
  app.use(expressJWT({
    secret: util.secretOrPrivateKey   
  }).unless({
    path: ['/login']  //除了这个地址，其他的URL都需要验证
  }));

  app.get('/', (req, res) => {
    res.json({ message: 'hello!'});
  });

  app.use('/login', require('./login'));
  app.use('/list', require('./list'));

  // 404
  app.use(function(req, res, next) {
    next(createError(404));
  });

  // 5错误
  app.use(function(err, req, res, next) {
    //校验 token 失败时的处理
    // 默认的情况下，解析 JWT 失败会抛出异常，可以通过以下设置来处理该异常
    if (err.name === 'UnauthorizedError') {   
      // 这个需要根据自己的业务逻辑来处理
      res.status(401).json({
        message: 'invalid token',
        error: err
      });
      return;
    }

    res.status(err.status || 500).json({
      message: err.message,
      error: err
    });
  });
};
```

``` js
// routes/login.js
const express = require('express');
const jwt = require('jsonwebtoken'); // 引入 jsonwebtoken
const util = require('../util/util');
const router = express.Router();

//模拟用户数据
let user = {
  name:'admin',
  pass:'123',
  role:0
};

router.post('/', function(req, res) {
  console.log(req.body);
  if(req.body.name === user.name && req.body.pass === user.pass) {
    console.log('账号正确');

    const token = jwt.sign({
      user_name: req.body.name
    }, util.secretOrPrivateKey , { // 秘钥
        expiresIn: 60 * 60 // 过期时间
    });

    res.json({
      msg:'登陆成功',
      token:token
    });
  } else {
    res.json({
      msg:'登录失败'
    });
  }
});

module.exports = router;
```

前端拿到 token 后可以直接存储在 localStorage 中，然后每次请求的时候挂载 Authorization：

``` js
const token = localStorage.getItem('token');
axios.get(`/home`, {
  headers: {
    Authorization: Bearer ' + token
  }
})
```

封装 axios 请求：

``` js
// http.js
import axios from 'axios';
import { createBrowserHistory } from 'history';

// 创建历史对象
const history = createBrowserHistory();

// 添加一个新的 axios 实例
var http = axios.create();

// 拦截请求，给所有的请求都带上 token
http.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token');
    if (token) {
      // 设置 token ，一般是在 headers 里加入 Authorization，并加上 Bearer 标注
      // 最好通过此种形式设置 request.headers['Authorization']
      config.headers['Authorization'] = 'Bearer ' + token;
    }
    return config;
  },
  error => {
    console.log(error);
    return Promise.reject(error);
});

// 拦截响应，遇到 token 不合法则报错
http.interceptors.response.use(
  response => {
    if (response.data.token) {
      localStorage.setItem('token', response.data.token);
    }
    return response;
  },
  error => {
    if (error.response.status === 401) {
      // 401 说明 token 验证失败
      // 可以直接跳转到登录页面，重新登录获取 token
      localStorage.removeItem('token');
      console.log(error.response.data.error.message);
      history.replace('/login');
    } else if(error.response.status === 500) {
      // 服务器错误
      return Promise.reject('服务器出错：',error.response.data);
    }
    return Promise.reject(error.response.data);   // 返回接口返回的错误信息
  });

export default http;
```

react 引用：

``` js
home.js
import React, { Component } from 'react';
import http from './http';

export default class Home extends Component{
  componentDidMount() {
    let that = this;
    http.get(url)
    .then(response => {
      //...
    })
    .catch((err) => {
      console.log(err);
    });
  }
  render() {
    return (
      //...
    )
  }
}
```


