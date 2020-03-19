
## node.js 链接 mongodb
### 项目中安装 mongodb
``` bash
npm install mongodb --save
```

### 创建连接文件 connect.js
``` js
var MongoClient = require('mongodb').MongoClient;

// 连接数据库
var url = 'mongodb://localhost:27017/test'; //数据库test本不存在，连接时会自动创建

var insertData = function(client) {
  // 向 test 数据库里新建一个 site 集合，并插入一条数据
  client.collection('site').insertOne({name: 'guojc', age: 99, hobby: 'movie'}, function(err, result){
    console.log('inserted successly');
    console.log(result);
    client.close();
    console.log('close');
  });
}

MongoClient.connect(url, function(err, client) {
  console.log('Connected successly to server.');
  insertData(client);
});
```

也可以使用以下方法创建 server 对象：

``` js
new mongo.Server(host,port,[options]);// host：必须，服务器所在地址// port：必须，服务器端口号// options：一个对象，服务器相关属性```

创建一个代表 MongoDB 数据库的 Db 对象：

``` js
new mongn.Db(databaseName,server,[options])// databaseName：必须，连接的数据库名// server：必须，数据库所在服务器// options：可选，一个设置数据库配置选项的对象```

案例：

``` js
var mongo = require('mongodb');

var server = new mongo.Server('localhost', 27017, {auto_reconnect: true});
var db = new mongo.Db('foo', server);

db.open(function(err, db) {
  if(!err) {
    console.log("We are connected");
  }
});

// 关闭数据库：db.close([forceClose],function (err) {    //})
```
