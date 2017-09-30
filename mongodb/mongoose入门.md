# mongoose入门
Mongoose是MongoDB的一个对象模型工具，是基于node-mongodb-native开发的MongoDB nodejs驱动，可以在异步的环境下执行。同时它也是针对MongoDB操作的一个对象模型库，封装了MongoDB对文档的的一些增删改查等常用方法，让NodeJS操作Mongodb数据库变得更加灵活简单。

## 一、环境准备
### 安装mongoose
	npm install mongoose --save
	
### 引用mongoose
``` js
const mongoose = require("mongoose");
```

### 连接数据库
``` js
mongoose.connect("mongodb://user:pass@ip:port/database");
```

* user ：mongodb的用户名
* pass ：mongodb用户密码
* ip ：mongodb服务器地址，比如本地为127.0.0.1
* port ：mongodb服务器端口，默认是27017
* database ：mongodb数据库名
	
## 二、语法
### Schema
schema是mongoose里会用到的一种数据模式，可以理解为数据库结构的定义；每个schema会映射到mongodb中的一个collection，它不具备操作数据库的能力。

``` js
const kittySchema = mongoose.Schema({
  name: String,
  friends: [String],  age: Number
});

const blogSchema = mongoose.Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
  
const userSchema = mongoose.Schema({
  username: {// 真实姓名
    type: String,
    unique: true, // 不可重复约束
    required: true
  },
  password: { // 密码
    type: String,
    required: true
  },
  time: { 
    type:Date, 
    default:new Date()
  },
  token: {
    type: String
  }
});
```

基本属性类型有：String、Number、Boolean、null、Object、Array、Date、Buffer、Mixed等。

### Model
model是由schema生成的模型，可以对数据库进行操作。

``` js
// 它在数据库中会创建一个名为 users 的 collection
var User = mongoose.model('User', kittySchema);
```

User 是模型名称，它对应到 mongodb 里就是数据库中的集合名称，默认会转成复数，变为'users',当我们对其添加数据时如果 users 已经存在，则会保存到其目录下，如果未存在，则会创建 users 集合，然后在保存数据。

为Model添加方法：``` js
// methods 必须加在 mongoose.model() 之前userSchema.methods.speak = function () {  var greeting = this.name ? "my name is " + this.name : "I don't have a name";  console.log(greeting);}var User = mongoose.model('User', kittySchema);```

### Entity
由Model创建的实体，他的操作也会影响数据库。

``` js
var user = new User({ 
  name: 'faychou',
  password: '0123456789'
});

console.log(user.name);  // 'faychou'
```

> Schema、Model、Entity的关系：Schema生成Model，Model创造Entity，Model和Entity都可对数据库操作造成影响，但Model比Entity更具操作性。

### 创建
1. 方法一：调用 save 方法后，就会在数据库中存入一条记录。

``` js
user.save(function (err,docs) {  if (err) return console.error(err);  console.log('数据插入成功！' + docs);});
```

2. 方法二：

``` js
var doc = {name:’fay’ , password:'0123456789'}; User.create(doc,function(err,docs) {  if(err) return console.log(err);   console.log(’数据插入成功：' + docs);});
```

3. 插入多条数据方法：``` js
User.insertMany([   {name: ”jack", password:'0123456789'},   {name : ”baur", password:'0123456789'}  ], function(err, docs) {   if(err) return console.log(err);   console.log('保存成功：' + docs); });```

### 删除
``` js
Model.remove([conditions], [callback]) // 根据条件查找到并删除

Model.findByIdAndRemove(id, [options], [callback]) // 根据id查找到并删除

Model.findOneAndRemove(conditions, [options], [callback])
```

``` js
var param = {name:’fay’}; Kitten.remove(param , function(err,docs) {  if(err) return console.log(err);   console.log('删除成功：' + docs);});```

### 查询
``` js
Model.find(conditions, [fields], [options], [callback])```

* Model：集合名字，如：User
* conditions：查询条件；* fields：控制返回的字段；* options：控制选项；* callback：回调函数

``` js
Model.find({},callback) // 参数1忽略,或为空对象则返回所有集合文档

Model.find({},{'name':1, 'age':0},callback) //过滤查询,参数2: {'name':1, 'age':0} 查询文档的返回结果包含name , 不包含age.(_id默认是1)

Model.find({},null,{limit:20}) // limit 限制返回结果数量为20个,如不足20个则返回所有

Model.findOne({}, callback) // 查询找到的第一个文档

Model.findById('obj._id', callback) // 查询找到的第一个文档, 只接受 _id 的值查询

Model.count(conditions, [callback])  // 数量查询
```

``` js
// 表示查询年龄大于等21而且小于等于65岁
User.find({age: {$gte: 21, $lte: 65}}, callback);

// 模糊查询
User.find({'username':{$regex:/m/i}}, callback);
```

其他筛选条件：

* `$or`：或关系
* `$nor`：或关系取反
* `$gt`：大于
* `$gte`：大于等于
* `$lt`：小于
* `$lte`：小于等于
* `$ne`：不等于
* `$in`：在多个值范围内
* `$nin`：不在多个值范围内
* `$all`：匹配数组中多个值
* `$regex`：正则，用于模糊查询
* `$size`：匹配数组大小
* `$maxDistance`：范围查询，距离（基于LBS）
* `$mod`：取模运算
* `$near`：邻域查询，查询附近的位置（基于LBS）
* `$exists`：字段是否存在
* `$elemMatch`：匹配内数组内的元素
* `$within`：范围查询（基于LBS）
* `$box`：范围查询，矩形范围（基于LBS）
* `$center`：范围醒询，圆形范围（基于LBS）
* `$centerSphere`：范围查询，球形范围（基于LBS）
* `$slice`：查询字段集合中的元素（比如从第几个之后，第N到第M个元素）

分页查询：

``` js
var User = require("./user.js");

function getByPager(){
  var pageSize = 5;                   //一页多少条
  var currentPage = 1;                //当前第几页
  var sort = {'logindate':-1};        //排序（按登录时间倒序）
  var condition = {};                 //条件
  var skipnum = (currentPage - 1) * pageSize;   //跳过数

  User.find(condition).skip(skipnum).limit(pageSize).sort(sort).exec(function (err, res) {
    if (err) {
      console.log("Error:" + err);
    } else {
      console.log("Res:" + res);
    }
  })
}

getByPager();
```

### 修改
``` js
Model.update(conditions, update, [options], [callback])```

* Model：集合名字，如：User
* conditions：查询条件；* update：需要修改的数据，不能修改主键（_id）；* options：控制选项；* callback：回调函数

``` js
// 一次更新多条:Model.updateMany(conditions, doc, [options], [callback])
// 找到一条记录并更新
Model.findOneAndUpdate([conditions], [update], [options], [callback])// 更新指定ID：Model.findByIdAndUpdate(id, [update], [options], [callback])
```

``` js
var param = {name:’fay’,age:18}; var date = {$set:{name:’jack’,age:26}};Kitten.update(param,date,function(err,docs) {  if(err) return console.log(err);   console.log('更改成功：' + docs);});```

## 三、搭配node.js
``` js
// app.js
const mongoose = require('mongoose');

//连接到 test 数据库
mongoose.connect('mongodb://localhost/test');

// 实例化连接对象
const db = mongoose.connection;

db.on('error', (error) => {
  console.error('数据库连接失败：' + error);
});

db.once('open', () => {
  console.log('MongoDB连接成功！');
});

db.on('disconnected', () => {    
    console.log('数据库连接断开');  
});

// 定义模型(model)
var Cat = mongoose.model('Cat', { name: String });

// 实例化模型
var kitty = new Cat({ name: 'faychou' });

// 保存到数据库
kitty.save(function (err) {
  if (err) {
    return console.log('save error:' + err);
  }

  console.log('save sucess');
});
```