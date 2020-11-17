# mongoDB入门
NoSQL 泛指非关系型数据库。
他的产生就是为了解决如海量数据的存储、弹性可伸缩和灵活性等方面的挑战。代表数据库为 Redis、MongoDB。

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
通常使用 Homebrew（[官网](https://brew.sh/)） 安装，

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

# 或者作为服务启动
# brew services start mongodb

# 重新打开一个新开窗口，进入 mongodb 命令行模式
mongo
# mongo --host 127.0.0.1:27017


## 更新
brew update
```

### Window 平台安装
官网直接下载 `.msi` 格式的安装包，按照提示进行安装。

启动服务：

1、 MongoDB 需要一个目录来保存数据，默认的数据目录是 `\data\db`，但是这个目录需要我们自己去创建，进入 mongodb 的安装目录（新版在安装时就已经设置了，可以省略这一步）：

``` bash
# mkdir –p 的意思是同时创建 data 及下级目录 db
mkdir –p data/db  
```

2、 同样还是在安装目录下创建存放日志的文件夹（新版在安装时就已经设置了，可以省略这一步）：

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

也可以通过打开浏览器 http://localhost:27017 查看是否启动成功，如果启动成功，会显示 It looks like you are trying to access MongoDB over HTTP on the native driver port.

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

#### 参数说明
* --config

* --port

* --dbpath

* --logpath

* --fork

#### 配置 windows 服务（在4.0版本后安装时就可以选择设置）
管理员身份启动 cmd，进入 `D:\Program Files\MongoDB\bin` 目录，输入
 `mongod --config "D:\Program Files\MongoDB\mongo.config" --install --serviceName "MongoDB"` 完成后打开服务，之后可使用 `net start MongoDB` 来启动 mongodb。

#### 关闭服务
1、关闭服务：Ctrl + C

2、mongod --shutdown

3、在 mongo shell 中执行：

``` bash
use admin
db.shutdownServer()
```

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
### 设置
在默认情况下，MongoDB 不会进行身份验证，也没有账号，只要能连接上服务就可以对数据库进行各种操作，但是这样是不安全的。

执行以下步骤添加权限：

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

``` sql
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: "abc123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
    # roles: [ "userAdminAnyDatabase"]
  }
)
```

db：对指定数据库的操作。

roles 角色： 

* read：允许用户读取指定数据库

* readWrite：允许用户读写指定数据库

* dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问 system.profile

* userAdmin：允许用户向 system.users 集合写入，可以指定数据库里创建、删除和管理用户

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

``` sql
# 新建 test 数据库
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

### Mongodb 忘记密码的解决办法
``` bash
vim /etc/mongodb.conf          # 修改 mongodb 配置，将 auth = true 注释掉，或者改成 false
service mongodb restart        # 重启 mongodb 服务
 
mongo                          # 运行客户端（也可以去mongodb安装目录下运行这个）
use admin                      # 切换到系统帐户表
db.system.users.find()         # 查看当前帐户（密码有加密过）
db.system.users.remove({})     # 删除所有帐户
db.addUser('admin','password') # 添加新帐户
 
vim /etc/mongodb.conf          # 恢复 auth = true
service mongodb restart        # 重启 mongodb 服务
```
