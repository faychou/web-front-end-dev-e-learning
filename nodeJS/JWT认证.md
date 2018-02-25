# JWT
JWT(json web token)是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准。一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源。比如用在用户登录上。

在传统的用户登录认证中，因为http是无状态的，所以都是采用session方式。用户登录成功，服务端会保证一个session，当然会给客户端一个sessionId，客户端会把sessionId保存在cookie中，每次请求都会携带这个sessionId。

而JWT只需要服务端生成token，客户端保存这个token，每次请求携带这个token，服务端认证解析就可。

## Token 认证
Token 是在服务端产生的。如果前端使用用户名/密码向服务端请求认证，服务端认证成功，那么在服务端会返回 Token 给前端。前端可以在每次请求的时候带上 Token 证明自己的合法地位。如果这个 Token 在服务端持久化（比如存入数据库），那它就是一个永久的身份令牌。

而为了安全考虑 Token 都需要设有效期。一般有效期越短越好，但也要符合用户习惯。如果用户在正常操作的过程中，Token 过期失效了，要求用户重新登录。为了解决在操作过程不能让用户感到 Token 失效这个问题，有一种方案是在服务器端保存 Token 状态，用户每次操作都会自动刷新（推迟） Token 的过期时间——Session 就是采用这种策略来保持用户登录状态的。然而仍然存在这样一个问题，在前后端分离、单页 App 这些情况下，每秒种可能发起很多次请求，每次都去刷新过期时间会产生非常大的代价。如果 Token 的过期时间被持久化到数据库或文件，代价就更大了。所以通常为了提升效率，减少消耗，会把 Token 的过期时保存在缓存或者内存中。

还有另一种方案，使用 Refresh Token，它可以避免频繁的读写操作。这种方案中，服务端不需要刷新 Token 的过期时间，一旦 Token 过期，就反馈给前端，前端使用 Refresh Token 申请一个全新 Token 继续使用。这种方案中，服务端只需要在客户端请求更新 Token 的时候对 Refresh Token 的有效性进行一次检查，大大减少了更新有效期的操作，也就避免了频繁读写。当然 Refresh Token 也是有有效期的，但是这个有效期就可以长一点了，比如，以天为单位的时间。

## JWT的构成
### JWT生成编码后的样子：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiend6IiwiYWdlIjoiMTgifQ.UQmqAUhUrpDVV2ST7mZKyLTomVfg7sYkEjmdDI5XF8Q
```

### 构成
JWT由三部分组件，分别是 头部（header),载荷（payload) 以及 签证（signature)。

#### header
* 类型，如 jwt
* 加密的算法 通常直接使用 HMAC SHA256

完整的头部就像下面这样的JSON：

``` json
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```

然后将头部进行base64加密（该加密是可以对称解密的),构成了第一部分：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

#### playload
载荷就是存放有效信息的地方。这些有效信息包含三个部分：

##### 标准中注册的声明 (建议但不强制使用) ：
* iss: jwt签发者
* sub: jwt所面向的用户
* aud: 接收jwt的一方
* exp: jwt的过期时间，这个过期时间必须要大于签发时间
* nbf: 定义在什么时间之前，该jwt都是不可用的.
* iat: jwt的签发时间
* jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。

##### 公共的声明
公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息.但不建议添加敏感信息，因为该部分在客户端可解密。

##### 私有的声明
私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为base64是对称解密的，意味着该部分信息可以归类为明文信息。所以不可以在载荷部分保存用户密码等敏感信息。

##### 定义一个payload:
``` json
{
  "name": "zwz",
  "age": "18"
}
```

然后将其进行base64加密，得到Jwt的第二部分：

```
eyJuYW1lIjoiend6IiwiYWdlIjoiMTgifQ
```

#### signature
* header (base64后的)
* payload (base64后的)
* secret

这个部分需要base64加密后的header和base64加密后的payload，再用加密算法加密一下，加密的时候要放进去一个 Secret ，这个相当于是一个密码，这个密码秘密地存储在服务端。

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
流程：

* 客户端使用用户名跟密码请求登录
* 服务端收到请求，去验证用户名与密码
* 验证成功后，服务端会签发一个 Token，再把这个 Token 发送给客户端
* 客户端收到 Token 以后可以把它存储起来，比如放在 Cookie 里或者 Local Storage 里
* 客户端每次向服务端请求资源的时候需要带着服务端签发的 Token
* 服务端收到请求，然后去验证客户端请求里面带着的 Token，如果验证成功，就向客户端返回请求的数据

## express中使用
``` bash
npm install express --save
npm install express-jwt --save
npm install body-parser --save
npm install jsonwebtoken --save
npm install shortid --save
```

``` js
var express = require("express");
var expressJwt = require("express-jwt");
var bodyParser = require("body-parser");
var jwt = require("jsonwebtoken");
var shortid = require("shortid");

var app = express();

app.use(bodyParser.json());

// express-jwt 作为 express 的一个中间件，需要设置 secret 作为秘钥，unless 可以排除某个接口
app.use(expressJwt({secret: "secret"}).unless({path: ["/login"]}));

// 默认的情况下，解析 JWT 失败会抛出异常，可以通过以下设置来处理该异常
app.use(function (err, req, res, next) {
  if (err.name === "UnauthorizedError") {
    res.status(401).send("invalid token");
  }
});


app.post("/login", function(req, res) {
  var username = req.body.username;
  var password = req.body.password;

  if (!username) {
    return res.status(400).send("username require");
  }
  if (!password) {
    return res.status(400).send("password require");
  }

  if (username != "admin" && password != "password") {
    return res.status(401).send("invaild password");
  }

  var authToken = jwt.sign({username: username}, "secret");
  res.status(200).json({token: authToken});

});

app.post("/user", function(req, res) {
  var username = req.body.username;
  var password = req.body.password;
  var country = req.body.country;
  var age = req.body.age;

  if (!username) {
    return res.status(400).send("username require");
  }
  if (!password) {
    return res.status(400).send("password require");
  }
  if (!country) {
    return res.status(400).send("countryrequire");
  }
  if (!age) {
    return res.status(400).send("age require");
  }

  res.status(200).json({
    id: shortid.generate(),
    username: username,
    country: country,
    age: age
  })
})

app.listen(3000);
```