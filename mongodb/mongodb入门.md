# mongoDB入门
MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

| SQL术语/概念  | MongoDB术语/概念 | 解释/说明                         |
| ------------ | -------------- | -------------------------------  |
|  database    |    database    |  数据库                           |
|  table       |    collection  |  数据库表/集合                     |
|  row         |    document    |  数据记录行/文档                   |
|  column      |    field       |  数据字段/域                       |
|  index       |    index       |  索引                             |
|  table joins |                |  表连接,MongoDB不支持              |
|  primary key |    primary key |  主键,MongoDB自动将_id字段设置为主键 |

MongoDB 是一个开源的 NoSQL 数据库，相比 MySQL 那样的关系型数据库，它更显得轻巧、灵活，非常适合在数据规模很大、事务性不强的场合下使用。同时它也是一个对象数据库，没有表、行等概念，也没有固定的模式和结构，所有的数据以文档的形式存储。

在 MongoDB 中，一行纪录就是一个文档，它是一个由键值对构成的数据结构，MongoDB 文档与 JSON 对象类似。键的值可以包含其他的文档，数组，文档数组。

``` js
{
   "_id" : ObjectId("54c955492b7c8eb21818bd09"),
   "address" : {
      "street" : "2 Avenue",
      "zipcode" : "10075",
      "building" : "1480",
      "coord" : [ -73.9557413, 40.7720266 ],
   },
   "borough" : "Manhattan",
   "cuisine" : "Italian",
   "grades" : [
      {
         "date" : ISODate("2014-10-01T00:00:00Z"),
         "grade" : "A",
         "score" : 11
      },
      {
         "date" : ISODate("2014-01-16T00:00:00Z"),
         "grade" : "B",
         "score" : 17
      }
   ],
   "name" : "Vella",
   "restaurant_id" : "41704620"
}
```

## 一、环境安装
### Lunix 平台安装

### Mac 平台安装
``` bash
# 安装
brew install mongodb
# brew install mongodb --devel

# 创建一个数据库存储目录 /data/db
mkdir -p /data/db

# 设置环境变量
export PATH=<mongodb-install-directory>/bin:$PATH

# 启动 Mongodb
mongod
# mongod --dbpath <path to data directory>
## 如果看到类似这样信息代表启动成功 [initandlisten] waiting for connections on port 27017

# 重新打开一个新开窗口，进入 mongodb 命令行模式
mongo
# mongo --host 127.0.0.1:27017


## 更新
brew update
```

### Window 平台安装
官网直接下载 `.msi` 格式的安装包，按照提示进行安装。启动服务：

1、 MongoDB 需要一个目录来保存数据，默认的数据目录是 `\data\db`，但是这个目录需要我们自己去创建，进入 mongodb 的安装目录：

``` bash
# mkdir –p 的意思是同时创建 data 及下级目录 dbmkdir –p data/db  
```

2、 同样还是在安装目录下创建存放日志的文件夹：

``` bash
mkdir logs
```

3、 创建配置文件夹（可以省略）：

```
mkdir etc
```

4、在 etc 文件夹下新建 mongodb.config 文件配置文件：

``` bash
# 数据库路径
dbpath=C:\Program Files\data\db

# 日志输出文件路径
logpath=C:\Program Files\logs\mongodb.log

# 错误日志采用追加模式
logappend = true

# 启用日志文件，默认启用
journal=true

# 端口号,默认为 27017
port = 27017

fork = true
auth = true
```

5、添加环境变量：

``` bash
# mongodb 安装目录
"C:\Program Files\MongoDB\bin"
```

6、 启动 MongoDB（注意路径中不应该包含空格）:

``` bash
# 通过配置文件启动 MongoDB 服务主进程
mongod --config C:\mongodb\etc\mongodb.conf

# 也可以自定义数据库位置启动 MongoDB 服务主进程
mongod --dbpath C:\mongodb\data\db
```

执行完此命令后，在控制台会打印一系列的启动信息，包括 MongoDB 的版本，是否根据 journal 日志执行 recovery，进程的信号，操作系统的信息等乖。最后一行会提示你启动成功，监听了 27017 端口，等待连接消息。

7、 连接 MongoDB，打开另一个命令行窗口，输入如下命令：

``` bash
# 默认配置
C:\mongodb\bin\mongo.exe

# [or] 配置启动
mongo --host localhost --port 27017

# [or] 连接其他网络下
mongo --username <user> --password <pass> --host <Host.IP.Adrs> --port 27017
```

执行些命令后，就能连接上 MongoDB 服务。

> mongod 是用来连接到 mongodb 数据库服务器的，即服务器端。
> 
> mongo 是用来启动 MongoDB shell 的，是 mongodb 的命令行客户端。

#### 关闭服务
1、关闭服务：Ctrl + C

2、关闭进程：

``` bash
ps -ef | grep mongo

# 返回信息
# 501 28883     1   0 10:16上午 ??         0:15.23 mongod --config /Users/faychou/web/faychou-blog/code/blog-react-v2/server/data/mongodb.config
# 501 29744 25278   0 11:34上午 ttys002    0:00.00 grep mongo

# 根据返回信息选择相应进程关闭
kill 28883
```

## 二、设置用户
在默认情况下，MongoDB 不会进行身份验证，也没有账号，只要能连接上服务就可以对数据库进行各种操作，但是这样是不安全的。

执行以下步骤：

1、 首次连接：

```
mongod --port 27017 --dbpath /data/db
```
	
2、 连接 shell：

``` bash
mongo
# mongo --port 27017
```
	
3、 创建超级管理员账号（必须先添加）：

```
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: "abc123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```

db：对指定数据库的操作。

roles 角色： 

* Read：允许用户读取指定数据库

* readWrite：允许用户读写指定数据库

* dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问 system.profile

* userAdmin：允许用户向 system.users 集合写入，可以找指定数据库里创建、删除和管理用户

* clusterAdmin：只在 admin 数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。

* readAnyDatabase：只在 admin 数据库中可用，赋予用户所有数据库的读权限

* readWriteAnyDatabase：只在 admin 数据库中可用，赋予用户所有数据库的读写权限

* userAdminAnyDatabase：只在 admin 数据库中可用，赋予用户所有数据库的 userAdmin 权限

* dbAdminAnyDatabase：只在 admin 数据库中可用，赋予用户所有数据库的 dbAdmin 权限。

* root：只在 admin 数据库中可用。超级账号，超级权限

这一步完成后，也可以不重启服务，直接使用以下方式也能继续：

``` bash
db.auth('myUserAdmin','abc123');
# 状态1表示验证成功，0表示验证失败
```

4、 重启 mongodb：

```
mongod --auth --port 27017 --dbpath /data/db
```
	
5、 使用账号连接：

```
mongo --port 27017 -u "myUserAdmin" -p "abc123" --authenticationDatabase "admin"
```
	
6、创建用户角色：

```
use test
db.createUser({
  user: "myTester",
  pwd: "xyz123",
  roles: [ { role: "readWrite", db: "test" },
     { role: "read", db: "reporting" } ]
  }
)
```

删除用户：

``` bash
db.removeUser("guest")
```

## 三、基础
### 创建数据库
MongoDB 中默认的数据库为 test，如果你没有创建新的数据库，集合将存放在 test 数据库中。

``` bash
# 如果数据库不存在，则创建数据库，否则切换到该数据库。
use database-name```

#### 查看数据库
默认为存在“admin”和“local”两个数据库。

``` bash
show dbs```

新创建的数据库并不在数据库的列表中， 要显示它，需要向数据库插入一些数据，所以看不到 test 这个数据库，因为里面还没有数据。

### 新建集合
``` bash
db.createCollection('collection_name')```

#### 查看当前数据库下集合： 
``` bash
show collections
```

#### 得到集合：``` bash
db.getCollection('collection_name')
```

### 增删查改
#### 添加数据
``` bash
db.usercollection.insert({'key':'value'})
db.usercollection.insert({ 'username' : 'faychou', 'email' : 'faychou@163.com' })```

#### 删除数据
``` bash# 删除当前集合指定内容，option为匹配条件，如{'name':'jaychou'}：db.usercollection.remove(option)

# 默认地，remove()方法将删除匹配指定条件的所有文档。使用justOne可选参数可以限制删除操作只删除一条。
db.usercollection.remove({ "borough": "Queens" }, { justOne: true })

# 删除一个集合中的所有文档，传递一个空的条件文档即可
db.usercollection.remove( {} )

# 删除当前数据库指定集合：db.usercollection.drop()

# 删除数据库
use my-dbdb.dropDatabase()
```

#### 查询数据
``` bash
# 查询集合中的所有文档
db.usercollection.find()

# 格式化输出内容db.usercollection.find().pretty()# 返回匹配文档的所有字段
db.usercollection.find( { type: 'food' } )

# 返回匹配文档的指定字段（只有 item 和 qty 字段，默认 _id 字段也是返回的）
db.usercollection.find( { type: 'food' }, { item: 1, qty: 1 } )

# 仅返回匹配文档的指定字段（排除 _id 字段）
db.usercollection.find( { type: 'food' }, { item: 1, qty: 1, _id:0 } )

# 查询所有grades数组的内嵌文档中score字段的值大于30的文档。
db.usercollection.find({ "grades.score": { $gt: 30 } })

# 小于操作
db.usercollection.find({ "grades.score": { $lt: 10 } })

# 使用逻辑AND查询条件，使用逗号隔开
db.usercollection.find({ "cuisine": "Italian", "address.zipcode": "10075" })

# 为多个查询条件中使用逻辑OR，使用$or查询操作
db.usercollection.find({ $or: [ { "cuisine": "Italian" }, { "address.zipcode": "10075" } ] })
# 查询第一条（支持条件）：
db.usercollection.findOne(条件)# 限制数量：
db.usercollection.find().limit(5)# 跳过指定数量：
db.usercollection.find().skip(2)

# 根据age升序,1表示升序，-1表示降序db.usercollection.find().sort({age:1})

# 先按borough字段升序排列，再按address.zipcode升序排。
db.usercollection.find().sort({ "borough": 1, "address.zipcode": 1 })

# 统计个数
db.usercollection.find({gender:true}).count()```

查询 tags 含有 a 的记录：

``` js
db.doc.find({"tags":{$in:[1]}});
{ "_id" : ObjectId("59f589f5641e44b4c51cb340"), "id" : 1, "name" : "one", "tags" : [ "a", "b", "c" ] }
{ "_id" : ObjectId("59f589f5641e44b4c51cb722"), "id" : 3, "name" : "polly", "tags" : [ "a", "c", "e" ] }
```

> 注意，`$in` 只能检查集合内每一个元素是否匹配，只要任意一个匹配就返回记录，并不能检查同时匹配多个元素的情况。

查询 tags 同时含有 a 和 b 的记录：

``` js
db.doc.find({"tags":{$all:['a','b']}});
{ "_id" : ObjectId("59f58ddfa874fe2b3da34065"), "id" : 4, "name" : "one", "tags" : [ "a", "b", "c" ] }
```

判断字段长度同时判断字段是否存在：

``` js
db.test.find({
  content: {
    $type:2, // 字段类型为2，表示有此字段，或者用: $exists: true
    $regex: /^.{600,}$/    // 长度大于600
  }     
});
```

#### 聚合查询
一般查询可以通过 find 方法，但如果是比较复杂的查询或者数据统计的话，find 可能就无能为力了，这时需要的是 aggregate（聚合）。它可以对数据文档进行变换和组合。聚合是基于数据流概念，数据进入管道经过一个或多个 stage，每个 stage 对数据进行操作（筛选，投射，分组，排序，限制或跳过）后输出最终结果。

mongoDB 中有许多操作符，在 aggregate 中每个 stage 可以使用的操作符叫做管道操作符：

* `$project`：投射操作符，用于重构每一个文档的字段，可以提取字段，重命名字段，甚至可以对原有字段进行操作后新增字段

* `$match`：匹配操作符，用于对文档集合进行筛选

* `$group`：分组操作符，用于对文档集合进行分组

* `$unwind`：拆分操作符，用于将数组中的每一个值拆分为单独的文档

* `$sort`：排序操作符，用于根据一个或多个字段对文档进行排序

* `$limit`：限制操作符，用于限制返回文档的数量

* `$skip`：跳过操作符，用于跳过指定数量的文档

* `$lookup`：连接操作符，用于连接同一个数据库中另一个集合，并获取指定的文档，类似于populate

* `$count`：统计操作符，用于统计文档的数量


#### 修改数据
``` bash
db.usercollection.update({'name':'faychou'},{'name','jaychou'})# 默认更新会替换掉之前所有的，加上$set后只更新部分，其他不变db.usercollection.update({'age':18},{$set:{'name':'jaychou'}})```

### 创建索引
``` bash
db.usercollection.ensureIndex()```

### 查看索引
``` bash
db.usercollection.getIndexes()```

### 导入数据
在命令行中执行mongoimport命令将上面下载的数据文件中的数据导入到test数据库的restaurants集合中。如果此集合已经存在，下面的操作会先删除。

``` bash
mongoimport --db test --collection restaurants --drop --file C:\data\dataset.json
```

dataset.json的数据内容

``` json
{
  "address": {
     "building": "1007",
     "coord": [ -73.856077, 40.848447 ],
     "street": "Morris Park Ave",
     "zipcode": "10462"
  },
  "borough": "Bronx",
  "cuisine": "Bakery",
  "grades": [
     { "date": { "$date": 1393804800000 }, "grade": "A", "score": 2 },
     { "date": { "$date": 1378857600000 }, "grade": "A", "score": 6 },
     { "date": { "$date": 1358985600000 }, "grade": "A", "score": 10 },
     { "date": { "$date": 1322006400000 }, "grade": "A", "score": 9 },
     { "date": { "$date": 1299715200000 }, "grade": "B", "score": 14 }
  ],
  "name": "Morris Park Bake Shop",
  "restaurant_id": "30075445"
}
```

mongoimport命令连接到本机运行的mongod实例，如果要把数据导到不同主机，不同端口的实例，可以指定主机和端口，使用参数 --host和--port。

### 备份、还原
``` bash
# 还原数据库，–drop：先删除所有的记录，然后恢复
mongorestore -h IP –port 端口 -u 用户名 -p 密码 -d 数据库 –drop 文件存在路径

# -d 表示导出指定数据库，不加则表示导出所有数据库
mongodump -h IP –port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径

# 导出表格格式的数据库
mongoexport -h IP –port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 -f 字段 -q 条件导出 –csv -o 文件名

# -f：导出指定字段，以分号分割，如：-f name,email,age
# -q：根据查询条件导出，如：-q '{ "uid" : "100" }'
# –csv：导出文件格式为 csv 的，因为大部分的关系型数据库都是支持 csv
```

## 四、node.js 链接 mongodb
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

```
new mongo.Server(host,port,[options]);host：必须，服务器所在地址port：必须，服务器端口号options：一个对象，服务器相关属性```

创建一个代表 MongoDB 数据库的 Db 对象：

```
new mongn.Db(databaseName,server,[options])databaseName：必须，连接的数据库名server：必须，数据库所在服务器options：可选，一个设置数据库配置选项的对象```

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

## 五、bug 解决
### mongodb 非正常关闭
``` bash
# 首先删除 mongod.lock 文件
rm data/db/mongod.lock

# 删除 mongodb.log 

# 修复
mongod --dbpath /Users/faychou/mongodb/data/db --repair
## 注意，这里一定要写 --dbpath

# 最后重启
mongod --config /Users/faychou/mongodb/data/db/mongodb.config
```
