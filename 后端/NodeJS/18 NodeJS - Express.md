# Express 入门
Express 是一个基于 Node.js 平台的极简、灵活的 web 应用开发框架，它提供一系列强大的特性，帮助你创建各种 Web 和移动设备应用。

## 安装
### 全局安装
在 Express4 之前的版本，只需要全局安装 Express 即可，后面由于 Express 的构建器被拆分出来，独立成为了一个项目 express-generator，现在需要全局安装 express-generator：

```
npm install -g express-generator
```
	
然后选择模版引擎即可自动生成快速项目，如使用默认 jade 模版：

```
express project-name && cd project-name

npm install

npm start
```

也可以选择使用和 HTML 文件类似的 ejs 模版：

``` bash
# express -e project-name && cd project-name #老版本写法
# 新版本最新写法
express --view=ejs project-name && cd project-name

npm install 

npm start

# 其他模版引擎 handlebars
express --hbs project-name
```

### 本地安装
express-generator 其实更多的只是为我们提供了一个快速搭建 express 环境的途径，而我们完全可以手动配置 express 环境，使之更适合当前的项目：

``` bash
# 本地项目安装
npm i express --save
```

``` js
//app.js
var express = require('express'); 
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

// 加载路由控制
var routes = require('./routes/index');
//var users = require('./routes/users');

// 创建项目实例
var app = express();

// 定义EJS模板引擎和模板文件位置，也可以使用jade或其他模型引擎
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

// 定义icon图标
app.use(favicon(__dirname + '/public/favicon.ico'));
// 定义日志和输出级别
app.use(logger('dev'));
// 定义数据解析器
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
// 定义cookie解析器
app.use(cookieParser());
// 定义静态文件目录
app.use(express.static(path.join(__dirname, 'public')));

// 匹配路径和路由
app.use('/', routes);
//app.use('/users', users);

// 404错误处理
app.use(function(req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});

// 开发环境，500错误处理和错误堆栈跟踪
if (app.get('env') === 'development') {
    app.use(function(err, req, res, next) {
        res.status(err.status || 500);
        res.render('error', {
            message: err.message,
            error: err
        });
    });
}

// 生产环境，500错误处理
app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});

// 输出模型app
module.exports = app;
```

## 入门案例
1、新建文件夹并在终端中进入该文件夹；

2、执行 npm init 命令，创建一个 package.json 文件；

3、在终端中运行 `npm install express --save` 来安装 Express ；

4、创建 app.js 文件：

``` js
/* app.js */

//引入 express 模块
const express = require('express'),
  app = express();

//程序运行时的环境所设置的，如果没有这个设置，默认 3000.
app.set('port', process.env.PORT || 3000);

//设置路由
app.get('/', (request,response)=> {
  response.send('Home page');
});

app.get('/about',(request,response)=> {
  response.send('About page');
});

//设置错误中间件
app.use((request,response)=> {
  response.type('text/plain');
  response.status(505);
  response.send('Error page');
});

//设置端口号
app.listen(3000, ()=> {
  console.log('Express server started at port 3000');
});
```

5、在终端中通过以下命令运行文件：

``` bash
node app.js
```

6、打开浏览器，输入 127.0.0.1:3000 进行访问，然后在 url 地址的最后添加 '/about'，观察页面变化。

## app
路由（Routing）是由一个 URI（或者叫路径）和一个特定的 HTTP 方法（GET、POST 等）组成的，每一个路由都可以有一个或者多个处理器函数，当匹配到路由时，这个/些函数将被执行。

路由的定义由如下结构组成：

`app.METHOD(PATH, HANDLER)`。

* METHOD：是某个 HTTP 请求方式中的一个；
* PATH：是服务器端的路径；
* HANDLER：是当路由匹配到时需要执行的函数。

### app.get
``` js
app.get('/', function(req, res) {
  res.send('hello world');
});
```

匹配规则：

``` js
// 匹配 acd 或者 abcd
app.get('/ab?cd', function(req, res) {
  res.send('ab?cd');
});

// 匹配 abcd、abbcd、abbbcd 等
app.get('/ab+cd', function(req, res) {
  res.send('ab+cd');
});

// 匹配 abcd、abxcd、abRABDOMcd、ab123cd等
app.get('/ab*cd', function(req, res) {
  res.send('ab*cd');
});

// 匹配 /abe 和 /abcde
app.get('/ab(cd)?e', function(req, res) {
 res.send('ab(cd)?e');
});

//使用正则表达式的路由路径示例：
// 匹配任何路径中含有 a 的路径：
app.get(/a/, function(req, res) {
  res.send('/a/');
});

// 匹配以 fly 结尾的路径，如 butterfly
app.get(/.*fly$/, function(req, res) {
  res.send('/.*fly$/');
});
```

### app.post
``` js
app.post('/', function (req, res) {
  res.send('POST request');
});
```

### app.use(path, callback)
如果 path 没有设置，默认为 `/`。

``` js
var express = require('express');
var app = express();

app.set('port',process.env.PORT || 3001 );

app.get('/',function(req, res){
  res.send('hello express!');
});

// 获取动态参数
app.get('/users/:name', function (req, res) {
  res.send('Hello, ' + req.params.name);
});

// 定制404页面
app.use(function(req,res){
  res.status(404).send('404 - Not Found!');
});

// 定制500页面
// 异常处理中间件与其他中间件相比的区别是函数参数为四个(err, req, res, next)
// 通常来说错误处理中间件定义在最后
app.use(function(err,req,res,next){
  console.error(err.stack);
  res.status(500).send('500 - Server Error!');
});

app.listen(app.get('port'),function(){
  console.log('Express started on http://localhost:' + app.get('port'));
});
```
#### req.host
返回请求头里取的主机名(不包含端口号)。

#### req.path
返回请求的URL的路径名。

#### req.query
处理 get 请求，获取 get 请求参数。

如 ?name=fay，req.query 的值为 {name: 'fay'}

#### req.params
处理 /:xxx 形式的 get 或 post 请求，获取请求参数。

如 /:name，访问 /fay，req.params 的值为 {name: 'fay'}

#### req.body
处理 post 请求，获取 post 请求体。

需要引入 body-parser 模块，请求体为 {"name": "fay"}，则 req.body 为 {name: 'fay'}

``` js
const bodyParser = require("body-parser");
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

#### req.cookies
需要cookieParser()中间件来解析，生成用户代理包含cookie的键值对，默认为{}。

#### res.status(code)
用于设置HTTP响应状态码。

``` js
res.status(403).end();
res.status(400).send('Bad Request');
```

#### res.set(field, [value])
设置响应头field值，或者传递对象设置多个field。

``` js
res.set('Content-Type', 'text/plain');

res.set({
    'Content-Type': 'text/plain',
    'Content-Length': '123',
    'ETag': '12345'
});
```

#### res.send([body])
发送响应。

#### res.json([body])
返回 json 响应，参数为对象或者数组时与 res.send() 等价。

``` js
res.json(null);
res.json({user: 'tobi'});
res.status(500).json({error: 'message'});
```

#### res.redirect
网址的重定向。

``` js
var express = require('express');
var app = express();

app.get("/hello",function(req,res){
    res.send("HELLO-->1");
});

app.get("/r1",function(req,res){
    res.redirect("/hello");
});

app.listen(3000);
```

#### res.sendFile
用于发送文件,注意必须使用完整路径。需要 express 版本高于 4.8.0

``` js
var express = require('express');
var app = express();

app.get("*",function(req,res){
  res.sendFile(__dirname + "/public/logo.png");
  // res.sendFile("/public/logo.png", {root: __dirname});
});

app.listen(3000);
```

#### res.render
该方法用于渲染网页模板，第一个参数是路径，第二个参数是传递的数据。

``` js
var express = require('express');
var app = express();

app.set('view engine','ejs');

app.get("/",function(req,res){
    res.render("index",{ message: "HTML Template"})
});

app.listen(3000);
```

### app.all(path,callback)
匹配所有HTTP方法。

### app.route
创建路由路径的链式路由句柄，可以帮助减少代码冗余和拼写错误。

``` js
// 可链式调用
app.route('/book')
  .get(function(req, res) {
    res.send('Get a random book');
  })
  .post(function(req, res) {
    res.send('Add a book');
  })
  .put(function(req, res) {
    res.send('Update the book');
  });
```

### app.set
为程序name设置value

``` js
app.set('title', 'My site');

// 获取设置的name值
app.get('title');   // My site
```

### app.listen()
指定端口。

## 中间件
中间件（middleware）就是处理HTTP请求的函数。它最大的特点就是，一个中间件处理完，可以通过调用 next() 传递给下一个中间件，如果没有调用 next()，则请求不会往下传递。

每个中间件可以从App实例，接收三个参数，依次为req、res，next()（下一个中间件）。

``` js
var express = require('express');
var app = express();

app.use(function(req, res, next) {
  console.log('1');
  next();
});

app.use(function(req, res, next) {
  console.log('2');
  res.status(200).end();
});

app.listen(3000);
```

### 内存管理
因为 Node 是单线程的，这也意味着所有的用户都会共享同一块内存空间。所以一般使用 `res.locals` 来存储当前用户的信息，这只在该用户的请求和响应循环中可用。

``` js
app.use((req, res, next) => {
        res.locals.user = req.user;
        res.locals.authenticated = !req.user.anonymous;
        next();
    });
```

## express.Router
创建模块化、可挂载的路由句柄。

``` js
// 根目录下新建 routes/index.js  

var express = require('express');
var router = express.Router(); 

// 访问根路由 渲染 index 模板
router.get('/', function (req, res) {
    res.render('index');
});

module.exports = router;
```

``` js
// 修改入口文件 app.js

var express = require('express');
var path = require('path');
var ejs = require('ejs');
var app = express();

var port = process.env.PORT || 3000;

// 引入路由模块
var router = require('./routes/index');
app.use('/', router);

// 设置视图文件目录
app.set('views', path.join(__dirname,'views'));  

app.set('view engine' , 'ejs'); //设置模板引擎为ejs

// 配置静态资源目录
app.use(express.static(path.join(__dirname, 'public')));

app.listen(port);
console.log('server started at port ' + port);
```

也可以使用 `router.route` 或 `app.route` 直接在应用程式上新增路由,这种方式的好处是程式可以在 `/login` 这个路由上將 GET 与 POST 分开处理，而且这样的写法既方便又简洁。

``` js
// app.js文件

var express = require('express');
var app = express();
var router = express.Router();

router.route('/login')
    // 显示登陆(GET http://localhost:3000/login)
    .get(function(req,res){
        res.send('this is the login form');    
    })
    // 处理登陆表单 (POST http://localhost:3000/login)
    .post(function(req,res){
        console.log('processing');
        res.send('processing the login form');
    });

app.use('/',router);
app.listen(3000);
```

router对象的param方法用于路径参数的处理。

``` js
// app.js文件

var express = require('express');
var app = express();
var router = express.Router();

router.get('/hello/:name',function(req,res){
    res.send('hello ' + req.params.name + '!');
});

app.use('/',router);
app.listen(3000);
```

## 模版引擎
模板引擎（Template Engine）是一个将页面模板和要显示的数据结合起来生成 HTML 页面的工具。相当于 MVC 中的视图。

### jade
这是express默认模版。是一个高性能的模板引擎，它深受 Haml 影响，它是用 JavaScript 实现的，并且可以供 Node 使用。

#### 语法：
``` html
<!-- 标签 -->
p ==> <p></p>
input ==> <input/>

<!-- 文本 -->
p 欢迎加入 ==> <p>欢迎加入</p>

<!-- 嵌套 -->
p Welcome, <b> LiLi </b> ==> <p>Welcome, <b>LiLi</b></p>

<!-- 多行嵌套,在标签后面添加 . -->
script. 
console.log('Welcome!') 
console.log('LiLi')
  ==> 
<script> 
  console.log('Welcome!') 
  console.log('LiLi u') 
</script>

<!-- 属性,以 ()来分割属性 -->
a(rel="nofollow", href="http://www.faychou.com")飞舟
  ==> 
<a rel="nofollow" href="http://www.faychou.com">飞舟</a>
```

### ejs
#### 安装
	npm install ejs --save

#### 设置

``` js
// app.js
var path = require('path');
var express = require('express');
var app = express();

var userRouter = require('./routes/users');

app.set('views', path.join(__dirname, 'views')); // 设置模板目录
app.set('view engine', 'ejs'); // 设置模板引擎为ejs

app.use('/users', userRouter);
```

``` js
// users.js
var express = require('express');
var router = express.Router();

router.get('/:name', function(req, res) {
  res.render('users', {
    name: req.params.name
  });
});

module.exports = router;
```

#### 语法
##### 1. 表达式
(1)、 <% message %> ：是不显示内容的,是用于执行js代码；

``` html
<% if (user) { %>
  <h2><%= user.name %></h2>
<% } %>  
```

(2)、 <%= %> ：是用于显示js变量文本(显示转义后的 HTML内容，即显示标签)；；

(3)、 <%- %> ：可以同时解析js变量和html字符串(显示解析后的 HTML 内容)。

(4)、<%# %> ：注释

``` html
<ul>
  <% users.forEach(function(user){ %>
    <%- include('user/show', {user: user}) %>
  <% }); %>
</ul>
```

``` html
<!-- views/users.ejs -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <style type="text/css">
      body {padding: 50px;font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;}
    </style>
  </head>
  <body>
    <h1><%= name.toUpperCase() %></h1>
    <p>hello, <%= name %></p>
  </body>
</html>
```

##### 2. includes
``` html
<!-- users.ejs -->
<%- include('header') %>
  <h1><%= name.toUpperCase() %></h1>
  <p>hello, <%= name %></p>
<%- include('footer') %>

<!-- header.ejs -->
<!DOCTYPE html>
<html>
    <head>
          <style type="text/css">
              body {padding: 50px;font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;}
       </style>
    </head>
    <body>

<!-- footer.ejs -->
    </body>
</html>
```

#### 修改渲染模版为 html：

``` js
/*
* 将上面的 app.set('view engine' , 'ejs')
* 修改成
* */

app.set('view engine' , 'html'); //修改模板文件的后缀名为html
app.engine('.html' , ejs.__express);   //"__express"，ejs模块的一个公共属性，表示要渲染的文件扩展名。
```

### handlebars
	npm install --save express-handlebars

``` js

//引用express
var express = require('express');
   
//引用express-handlebars模板引擎
var hbs = require('express-handlebars').create({ 
        defaultLayout: 'main', //默认布局模板为main.hbs
        extname: '.hbs'         //设置文件后缀名为.hbs
    });   
var app = express();

app.set('port',process.env.PORT || 3000);   //设置端口

//设置模板引擎为express-handlebars
app.engine('hbs',hbs.engine);
app.set('view engine','hbs');

//使用static中间件 制定public目录为静态资源目录,其中资源不会经过任何处理
app.use(express.static(__dirname + '/public'));

//home页路由
app.get('/', function (req, res) {
   res.render('home',{
       title:'Home Page'    //传入页面的title
   });
});

//about页路由
app.get('/about', function (req, res) {
    res.render('about',{
        title:'About Page'    //传入页面的title
    });
});

app.listen(app.get('port'), function () {
    console.log( '服务器启动完成，端口为： '+app.get('port') );
});
```

表达式：

```
{{{body}}}
```

## 静态资源
通过 Express 内置的 express.static 可以方便地托管静态文件。

``` js
app.use(express.static(path.join(__dirname, 'public')));
```

如果你的静态资源存放在多个目录下面，你可以多次调用 express.static 中间件：

``` js
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, 'files')));
```

> 注意：添加路径时，不写 public 这个路径，如：

``` js
http://localhost:3000/css/style.css
```

如果你希望所有通过 express.static 访问的文件都存放在一个“虚拟（virtual）”目录（即目录根本不存在）下面，可以通过为静态资源目录指定一个挂载路径的方式来实现，如下所示：

``` js
app.use('/static', express.static(path.join(__dirname, 'public')));
```

然后就可以通过带有 “/static” 前缀的地址来访问 public 目录下面的文件了：

```
http://localhost:3000/static/images/kitten.jpg
http://localhost:3000/static/css/style.css
```

## 错误处理
### 404
在 Express 中，404 并不是一个错误。而是因为 Express 执行了所有中间件、路由之后还是没有获取到任何输出。所以需要在其他所有中间件的后面添加一个处理 404 的中间件：

``` js
app.use(function(req, res, next) {
  res.status(404).send('Sorry cant find that page!');
});
```

# 第三方中间件
## body-parser
body-parser 对 post 请求的请求体进行解析。

	npm install body-parser

``` js
var express = require('express');
var bodyParser = require('body-parser');

var app = express();

// 解析urlencoded 请求体,extended:ture->使用queryString库（默认） false->使用qs库
app.use(bodyParser.urlencoded({extended: true}));

// 解析 json
app.use(bodyParser.json());
```

## express-session
会话是一种持久的网络协议，用于完成服务器和客户端之间的一些交互行为。会话是一个比连接粒度更大的概念， 一次会话可能包含多次连接，每次连接都被认为是会话的一次操作。在网络应用开发中，有必要实现会话以帮助用户交互。例如网上购物的场景，用户浏览了多个页面，购买了一些物品，这些请求在多次连接中完成。许多应用层网络协议都是由会话支持的，如 FTP、Telnet 等，而 HTTP 协议是无状态的，本身不支持会话，因此在没有额外手段的帮助下，前面场景中服务器不知道用户购买了什么。

为了在无状态的 HTTP 协议之上实现会话，Cookie 诞生了。Cookie 是一些存储在客户端的信息，每次连接的时候由浏览器向服务器递交，服务器也向浏览器发起存储 Cookie 的请求，依靠这样的手段服务器可以识别客户端。我们通常意义上的 HTTP 会话功能就是这样实现的。具体来说，浏览器首次向服务器发起请求时，服务器生成一个唯一标识符并发送给客户端浏览器，浏览器将这个唯一标识符存储在 Cookie 中，以后每次再发起请求，客户端浏览器都会向服务器传送这个唯一标识符，服务器通过这个唯一标识符来识别用户。 对于开发者来说，我们无须关心浏览器端的存储，需要关注的仅仅是如何通过这个唯一标识符来识别用户。很多服务端脚本语言都有会话功能，如 PHP，把每个唯一标识符存储到文件中。

express-session 是基于 express 框专门用于处理 session 的中间件。session 的认证机制离不开 cookie，所以需要同时使用 cookie-parser 中间件。

注意：sesison 数据仅仅保存在服务器端，cookie 中只保存 sessionID。

	npm install express-session
	npm install cookie-parser

``` js
var express = require('express');  
var session = require('express-session');  
var cookieParser = require('cookie-parser');

var app = express();

// 解析cookie
app.use(cookieParser('secret'));

// session 挂载
app.use(session({ 
  secret: 'secret', // 用于对sessionID的cookie进行签名
  name: 'testapp',   // cookie 中保存 sessionID 的字段名称，默认：connect.sid
  cookie:{ // 存放 session id 的 cookie 的相关选项，默认 {path: '/', httpOnly: true, secure: false, maxAge: null}
    maxAge: 1000*60*30; // 过期时间，默认关闭浏览器的时候，session失效
  }
}));

// 通过req参数来存储和访问session对象的数据
// req.session是一个JSON格式的JS对象，可以在使用的过程中随意的增删成员
app.get('/home', function(req, res){  
      
  if(req.session.lastPage) {  
    console.log('Last page was: ' + req.session.lastPage + ".");      
  }
  
  req.session.lastPage = '/awesome'; //更新session对象的lastPage 
  res.send("session expired time is: " + req.session.cookie.maxAge);   
});

app.get('/', function (req, res) {  
  // 检查 session 中的 isVisit 字段
  // 如果存在则增加一次，否则为 session 设置 isVisit 字段，并初始化为 1。
  if(req.session.isVisit) {
    req.session.isVisit++;
    res.send('<p>第 ' + req.session.isVisit + '次来此页面</p>');
  } else {
    req.session.isVisit = 1;
    res.send("欢迎第一次来这里");    console.log(req.session);
  }
});

app.use(function(req,res,next){ 
  res.locals.user = req.session.user;   // 从session 获取 user对象
  var err = req.session.error;   //获取错误信息
  delete req.session.error;
  res.locals.message = "";   // 展示的信息 message
  
  if(err){ 
    res.locals.message = '<div>'+err+'</div>';
  }
  
  next();  //中间件传递
});
app.listen(5000);
```

当maxAge时间过期后，session会自动移除，对应的还有浏览器的cookie。

## connect-mongo
有时候，我们需要session的声明周期要长一点，比如好多网站有个免密码两周内自动登录的功能。基于这个需求，session必须寻找内存之外的存储载体，比如mongodb数据库。express框架提供了针对mongodb的中间件：`connect-mongo`，我们只需在挂载session的时候在options中传入mongodb的参数即可，程序运行的时候, app 会自动管理session的存储，更新和删除。

``` js
var express = require('express');  
var session = require('express-session');  
var cookieParser = require('cookie-parser');  
var MongoStore = require('connect-mongo')(session);  
var app = express();  
  
app.use(cookieParser());  
app.use(session({  
  secret: '12345',  
  name: 'testapp',  
  cookie: {maxAge: 80000 },  
  resave: false,  // 即使 session 没有被修改，也保存 session 值，默认为 true
  saveUninitialized: false,  // 强制没有“初始化”的session保存到storage中，默认true
  store: new MongoStore({  // session 的存储方式，默认存放在内存中，这里选择存在 mongodb 数据库中
    host: 'localhost',    // 数据库的地址
    port: 27017,          // 数据库的端口号  
    db: 'test-app'        // 数据库的名称   
  })  
}));

app.get('/home', function(req, res){      
  if(req.session.lastPage) {  
    console.log('Last page was: ' + req.session.lastPage + ".");      
  }      
  
  req.session.lastPage = '/awesome';  
  res.send("session expired time is: " + req.session.cookie.maxAge);  
});

app.listen(5000);
```

## connect-flash
实现页面通知（即成功与错误信息的显示）的功能。flash 是一个在 session 中用于存储信息的特定区域。信息写入 flash ，下一次显示完毕后即被清除。典型的应用是结合重定向的功能，确保信息是提供给下一个被渲染的页面。

``` bash
# 安装
npm install connect-flash --save
```

``` js
const flash = require('connect-flash');
...
app.use(flash());
```

## serve-favicon
avicon是一个网站的图标，浏览器通过一个约定好的地址去获取该favicon，并将icon放在地址的前面作为标识。

``` bash
# 安装
npm install serve-favicon --save
```

``` js
// app.js
const favicon = require('serve-favicon');
app.use(favicon(__dirname + '/public/favicon.ico'));
```

然后在 public 目录下面添加一个 facicon.ico 这个图标文件。

## cors
允许 cors 请求。

``` js
// 允许所有跨域请求
var express = require('express')
var cors = require('cors')
var app = express()

app.use(cors())

// 允许某路由的跨域请求
app.get('/products/:id', cors(), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for a Single Route'})
})

// 允许某些域的请求
var whitelist = ['http://example1.com', 'http://example2.com']
var corsOptions = {
  origin: function (origin, callback) {
    if (whitelist.indexOf(origin) !== -1) {
      callback(null, true)
    } else {
      callback(new Error('Not allowed by CORS'))
    }
  }
}

app.get('/products/:id', cors(corsOptions), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for a whitelisted domain.'})
})

// 允许 GET/POST 以外的请求
app.options('/products/:id', cors()) // enable pre-flight request for DELETE request
app.del('/products/:id', cors(), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for all origins!'})
})

// 对所有路由允许
app.options('*', cors()) // include before other routes
```

## morgan
日志。将程序请求过程的信息显示在Terminal中，以便于我们调试代码

``` bash
# 安装
npm install morgan --save
```

``` js
var logger = require('morgan');
...
// 命令行中显示程序运行日志,便于bug调试
app.use(logger('dev')); // 参数可选 combined tiny 或自定义输出日志格式
```

``` js
// 导出日志文件
var express = require('express')
var fs = require('fs')
var morgan = require('morgan')
var path = require('path')

var app = express()

// create a write stream (in append mode)
var accessLogStream = fs.createWriteStream(path.join(__dirname, 'access.log'), {flags: 'a'})

// setup the logger
app.use(morgan('combined', {stream: accessLogStream}))
```

## http-proxy-middleware
设置代理服务。

``` js
var express = require('express');
var proxy = require('http-proxy-middleware');
var app = express();
app.use('/api', proxy({
    target: "http://faychou.com",
    changeOrigin: true
}));
// 省略各种配置  ... ...
app.listen(9000);
```

> 将基于名称的虚拟托管网站的选项changeOrigin设置为true。

例子：

``` js
var express = require('express');
var proxy = require('http-proxy-middleware');

// proxy middleware options 
var options = {
        target: 'http://www.example.org', // target host 
        changeOrigin: true,               // needed for virtual hosted sites 
        ws: true,                         // proxy websockets 
        pathRewrite: {
            '^/api/old-path' : '/api/new-path',     // rewrite path 
            '^/api/remove/path' : '/path'           // remove base path 
        },
        router: {
            // when request.headers.host == 'dev.localhost:3000', 
            // override target 'http://www.example.org' to 'http://localhost:8000' 
            'dev.localhost:3000' : 'http://localhost:8000'
        }
    };

// create the proxy (without context) 
var exampleProxy = proxy(options);

// mount `exampleProxy` in web server 
var app = express();
    app.use('/api', exampleProxy);
    app.listen(3000);
```

## express-http-proxy
使用express-http-proxy解决跨域问题。

### 安装
``` bash
npm install express-http-proxy --save
```

### 使用
``` js
const express = require('express');
const httpProxy = require('express-http-proxy');
const app = express();

const userServiceProxy = httpProxy('https://user-service');

// 身份认证
app.use((req, res, next) => {
  // TODO: 身份认证逻辑
  next()
})

// 代理请求
app.get('/users/:userId', (req, res, next) => {
  userServiceProxy(req, res, next)
})
```

## bcrypt
在 NodeJS 中，直接使用官方内建的 crypto 包对用户密码进行不可逆的 MD5 或者 SHA-1 加密。

``` js
let pwt = '123456';

// MD5 Hash
require('crypto').createHash('md5').update(pwt).digest('hex');

// SHA-1 Hash
require('crypto').createHash('sha1').update(pwt).digest('hex');
```

当用户注册时，将用户提交的密码先不可逆加密，然后将密文保存在数据库内。

当用户登录时，将用户提交的密码先以相同方式加密，然后与数据库中的密文比对，来判断密码的正误。

## passport
非常流行的权限验证库，综合的用户验证解决方案，可以实现 qq，微信登录。

``` bash
# 安装
npm install passport --save
npm install passport-local --save
npm install express-session --save
```

第一步、引入passport：

``` js
// app.js
const session = require('express-session');
const passport = require('./auth/passport_config.js');
...
app.use(session({
  secret: 'secret',
  resave: false,
  saveUninitialized: false
}));

app.use(passport.initialize());
app.use(passport.session());
```

第二步：在路由中加载passport作为中间件提供认证服务：

``` js
const passport = require('./auth/passport_config.js');

// 登录认证
router.post('/user/login', passport.authenticate('local'), function(req, res) {
  log.info(req.user);
  res.send("success");
});

// 认证测试
router.get('/user/testauth', passport.authenticateMiddleware(), function(req, res) {
  res.send('允许访问');
});
```

第三步：配置核心文件 passport_config.js：

``` js
const passport = require('passport');
const LocalStrategy = require('passport-local');

const UserModel = require('../model/UserModel');// 实体数据库模块

var log = require('tracer').colorConsole({ level: require('config').get('log').level });// 日志

/**
* 用户名密码验证策略
* @param username 用户输入的用户名
* @param password 用户输入的密码
* @param done 验证验证完成后的回调函数，由passport调用
*/
passport.use(new LocalStrategy(
function(username, password, done) {
  UserModel.findOne({ username: username }).then(function(result) {
    if (result != null) {
      if (result.password == password) {
        return done(null, result);
      } else {
        log.error('密码错误');
        return done(null, false, { message: '密码错误' });
      }
    } else {
      log.error('用户不存在');
      return done(null, false, { message: '用户不存在' });
    }
  }).catch(function(err) {
    log.error(err.message);
    return done(null, false, { message: err.message });
  });
}));

// serializeUser 用户登录验证成功以后将会把用户的数据存储到 session 中
passport.serializeUser(function(user, done) {
  done(null, user);
});

// deserializeUser 每次请求的时将从 session 中读取用户对象，并将其封装到 req.user

passport.deserializeUser(function(user, done) {
  return done(null, user);
});

// 这是封装了一个中间件函数到 passport 中，可以在需要拦截未验证的用户的请求的时候调用

passport.authenticateMiddleware = function authenticationMiddleware() {
  return function(req, res, next) {
    if (req.isAuthenticated()) {
      return next();
    }

    // res.redirect('/user/login');
    res.send('非法访问');
  }
};

module.exports = passport;
```

## md5加密
``` js
var md5 = crypto.createHash('md5'),
  password = md5.update(req.body.password).digest('hex');

var newUser = new User({
  name: name,
  password: password,
  email: req.body.email
});
```

## XML2js
将 xml 与 json 相互转化的包。

``` bash
# 安装
npm install --save xml2js
```

使用：

``` js
const xml2js = require('xml2js');
//--> xml -> json
// xml2js 默认会把子节点的值变为一个数组, 所以这里配置 explicitArray 值为 false
const xmlParser = new xml2js.Parser({ explicitArray: false, ignoreAttrs: true });

var xml = "<root>xml2js to json</root>";

xmlParser.parseString(xml, (err, result) => {
  console.log(result);
});


//--> json -> xml
const jsonBuilder = new xml2js.Builder();

var obj = {name: "fay", age: 18};

var jsonxml = jsonBuilder.buildObject(obj);
console.log(jsonxml);
```
