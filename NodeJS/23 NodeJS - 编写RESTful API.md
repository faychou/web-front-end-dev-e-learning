# 编写 RESTful API
一个 RESTful API 应该不以服务器/客户端的状态改变而改变。通过使用一个 REST 接口，不同的客户端，即使它们的状态各不相同，但是在访问相同的 REST 终端时，应该做出同一种动作，并且接收到相同的数据。

编写一个 RESTful API 涉及到使用 JSON 或是 XML 格式传输数据。

### 简单案例
1、创建一个叫 node-api 的文件夹；

2、通过命令行进入这个文件夹，输入 npm init，创建一个 package.json 文件；

3、输入 npm install --save express 来安装 express；

4、在根目录新建3个文件：app.js，users.js 和 index.html；

``` js
//app.js
const express = require('express'),
  app = express(),
  users = require('./users');

//setting the port.
app.set('port', process.env.PORT || 3000);

//Adding routes
app.get('/',(request,response)=> {
 response.sendFile(__dirname + '/index.html');
});

app.get('/users',(request,response)=> {
 response.json(users);
});

//Binding to localhost://3000
app.listen(3000,()=> {
 console.log('Express server started at port 3000');
});
```

``` js
//users.js
module.exports.users = [
 {
  name: 'Mark',
  age : 19,
  occupation: 'Lawyer',
  married : true,
  children : ['John','Edson','ruby']
 },
  
 {
  name: 'Richard',
  age : 27,
  occupation: 'Pilot',
  married : false,
  children : ['Abel']
 },
  
 {
  name: 'Levine',
  age : 34,
  occupation: 'Singer',
  married : false,
  children : ['John','Promise']
 },
  
 {
  name: 'Endurance',
  age : 45,
  occupation: 'Business man',
  married : true,
  children : ['Mary']
 },
]
```

``` html
<!-- index.html -->

<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Home page</title>
</head>
<body>
  <button>Get data</button>

  <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
  <script>
  
    const btn = document.querySelector('button');
    btn.addEventListener('click',getData);
    function getData(e) {
      $.ajax({
        url : '/users',
        method : 'GET',
        success : function(data) {
          console.log(data);
        },
      
        error: function(err){
          console.log('Failed');
        }
      });
    } 
  </script>
</body>
</html>
```
