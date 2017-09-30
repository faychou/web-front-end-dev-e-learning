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

MongoDB是一个开源的NoSQL数据库，相比MySQL那样的关系型数据库，它更显得轻巧、灵活，非常适合在数据规模很大、事务性不强的场合下使用。同时它也是一个对象数据库，没有表、行等概念，也没有固定的模式和结构，所有的数据以文档的形式存储。

在MongoDB中，一行纪录就是一个文档，它是一个由键值对构成的数据结构，MongoDB文档与JSON对象类似。键的值可以包含其他的文档，数组，文档数组。

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

### Window 平台安装
官网直接下载.msi安装包，按照提示进行安装；### 启动服务
1、 MongoDB需要一个目录来保存数据，默认的数据目录是`\data\db`，但是这个目录需要我们自己去创建：

``` bash
# mkdir –p的意思是同时创建data及下级目录dbmkdir –p data/db  
```

2、 创建存放日志的文件夹：

``` bash
mkdir logs
```

3、 启动MongoDB（注意路径中不应该包含空格）

``` bash
# 启动MongoDB服务主进程，并指定数据目录
C:\mongodb\bin\mongod.exe --dbpath C:\mongodb\data\db
```

执行完此命令后，在控制台会打印一系列的启动信息，包括MongoDB的版本，是否根据journal日志执行recovery，进程的信号，操作系统的信息等乖。最后一行会提示你启动成功，监听了27017端口，等待连接消息。

4、 连接MongoDB，打开另一个命令行窗口，输入如下命令：

``` bash
C:\mongodb\bin\mongo.exe
```

执行些命令后，就能连接上MongoDB服务。

> mongod 是用来连接到mongodb数据库服务器的，即服务器端。
> 
> mongo 是用来启动MongoDB shell的，是mongodb的命令行客户端。

## 二、设置用户
在默认情况下，MongoDB 不会进行身份验证，也没有账号，只要能连接上服务就可以对数据库进行各种操作，但是这样是不安全的。

执行以下步骤：

1、 首次连接：

	mongod --port 27017 --dbpath /data/db
	
2、 连接shell：

	mongo --port 27017
	
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

4、 重启mongodb：

	mongod --auth --port 27017 --dbpath /data/db
	
5、 使用账号连接：

	mongo --port 27017 -u "myUserAdmin" -p "abc123" --authenticationDatabase "admin"
	
6、创建用户角色：

```
use test
db.createUser(
  {
    user: "myTester",
    pwd: "xyz123",
    roles: [ { role: "readWrite", db: "test" },
             { role: "read", db: "reporting" } ]
  }
)
```

7、以用户账号连接：

	mongo --port 27017 -u "myTester" -p "xyz123" --authenticationDatabase "test"

添加用户：

``` bash
db.addUser("guest", "pass", true)
```
该方法包含三个参数：

* user - 字符串，表示用户名
* password - 字符串，对应的密码
* readOnly - boolean，可选参数，默认值为 false，表示是否是只读用户

修改用户密码：

``` bash
db.addUser("guest", "newpass")
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
use database-name```

#### 查看数据库
默认为存在“admin”和“local”两个数据库。

``` bash
show dbs```

新创建的数据库并不在数据库的列表中， 要显示它，需要向数据库插入一些数据，所以看不到test这个数据库，因为里面还没有数据。

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

### 删除数据
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

# 格式化输出内容db.usercollection.find().pretty()# 查询集合中所有borough字段值为"Manhattan"的文档
db.usercollection.find({ "borough": "Manhattan" })

# 查询grades包括一个内嵌文档
db.usercollection.find({ "grades.grade": "B" })

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
db.usercollection.find().sort({ "borough": 1, "address.zipcode": 1 })```

#### 修改数据
``` bash
db.usercollection.update({'name':'faychou'},{'name','jaychou'})# 默认更新会替换掉之前所有的，加上$set后只更新部分，其他不变db.usercollection.update({'age':18},{$set:{'name':'jaychou'}})```

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

## 三、node.js 链接 mongodb
	# 安装
	npm install mongodb --save
	
创建 server对象：

```
new mongo.Server(host,port,[options]);host：必须，服务器所在地址port：必须，服务器端口号options：一个对象，服务器相关属性```

创建一个代表MongoDB数据库的Db对象：

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