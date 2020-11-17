# mongoose入门
Mongoose 是 MongoDB 的一个对象模型工具，是基于 node-mongodb-native 开发的MongoDB nodejs 驱动，是在异步的环境下执行。同时它也是针对 MongoDB 操作的一个对象模型库，封装了 MongoDB 对文档的的一些增删改查等常用方法，让 NodeJS 操作 Mongodb 数据库变得更加灵活简单。

## 一、环境准备
### 安装 mongoose
```
npm install mongoose --save
```

### 引用 mongoose
``` js
const mongoose = require("mongoose");
```

### 连接数据库
``` js
mongoose.connect("mongodb://user:pass@ip:port/database", options);

// 如
mongoose.connect("mongodb://root:root@127.0.0.1:27017", {
  server: {
    auto_reconnect: true, // 是否自动重连接
    poolSize: 10 // 连接池大小
  }
});
```

* user ：mongodb 的用户名
* pass ：mongodb 用户密码
* ip ：mongodb 服务器地址，比如本地为127.0.0.1
* port ：mongodb 服务器端口，默认是27017
* database ：mongodb 数据库名

options 可选选项对象，该对象将传递给底层驱动程序。这里所包含的所有选项优先于连接字符串中传递的选项：

* db            -数据库设置
* server        -服务器设置
* replset       -副本集设置
* user          -用户名
* pass          -密码
* auth          -鉴权选项
* mongos        -连接多个数据库
* promiseLibrary

连接多个数据库，只需要设置多个 url 以 `,` 隔开，同时设置 mongos 为 true：

``` js
mongoose.connect('urlA,urlB,...', {
  mongos : true 
});
```

connect() 函数还接受一个回调参数：

``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://localhost/test", options, function(err) {
  if(err) {
    console.log('连接失败');
  } else {
    console.log('连接成功');
}
});
```

## 二、概念
### Schema
schema 是 mongoose 里会用到的一种数据模式，相当于一个数据库的模板，不具备操作数据库的能力，每个 schema 会映射到 mongodb 中的一个 collection。定义 Schema 非常简单，指定字段名和类型即可：

``` js
const UserSchema = mongoose.Schema({
  name: String,
  friends: [String],
  age: Number
});

const BlogSchema = mongoose.Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: Date,
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
```

基本属性类型有：String、Number、Boolean、ObjectId、Array、Date、Buffer、Mixed等。

在 mongoose 中,提供了 Schema 的类，可以通过实例化来实现创建 Schema 的效果，从而不需要每次调用 mongoose.Schema() 这个 API：

``` js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

const UserSchema = new Schema({
  name: String,
  friends: [String],
  age: Number
});

```

#### 文档验证
如果不进行文档验证，保存文档时，就可以不按照 Schema 设置的字段进行设置，这样肯定是要出问题的，所以在新建 Schema 的时候要设置验证：

* type：字段类型

* required: 数据必须填写
* default: 默认值
* unique：不可重复约束
* validate: 自定义匹配
* min: 最小值(只适用于数字)
* max: 最大值(只适用于数字)
* match: 正则匹配(只适用于字符串)
* enum:  枚举匹配(表示该属性值,只能出席哪些，只适用于字符串)

``` js
const UserSchema = mongoose.Schema({
  username: {// 真实姓名
    type: String,
    unique: true, // 不可重复约束
    required: true
  },
  password: { // 密码
    type: String,
    required:[true,"password 是必须的"], // 第二个参数是错误提示信息
    match:/\d/ //必须包含数字
  },
  time: { 
    type:Date, 
    default:new Date() //如果没有设置，则采用这个值
  },
  token: {
    type: String
  }
});
```

创建 Schema 对象时，声明字段类型有两种方法，一种是首字母大写的字段类型，另一种是引号包含的小写字段类型：

``` js
var mySchema = mongoose.Schema({title:String, author:String});

//或者 
var mySchema = mongoose.Schema({title:'string', author:'string'});
```

如果需要在 Schema 定义后添加其他字段，可以使用 add() 方法：

``` js
mySchema.add({ name: 'string', color: 'string', price: 'number' });
```

在 schema 中设置 timestamps 为 true，schema 映射的文档 document 会自动添加 createdAt 和 updatedAt 这两个字段，代表创建时间和更新时间：

``` js
var UserSchema = mongoose.Schema(
  {...},
  { timestamps: true }
);
```

#### 设置索引：
设置索引分两种,一种设在 Schema filed, 另外一种设在 Schema.index 里。

``` js
//在 field 设置
var animalSchema = new Schema({
  name: String,
  type: String,
  tags: { type: [String], index: true } // field level
});

// schema level
animalSchema.index({ name: 1, type: -1 }); // 1 表示正序, -1 表示逆序
```

由于在创建字段时, 数据库会自动根据自动排序(ensureIndex)，有可能严重拖慢查询或者创建速度，所以一般需要将该 option 关闭：

``` js
mongoose.connect('mongodb://user:pass@localhost:port/database', { config: { autoIndex: false } });  //真心推荐
// or  
mongoose.createConnection('mongodb://user:pass@localhost:port/database', { config: { autoIndex: false } });  //不推荐
// or
animalSchema.set('autoIndex', false);  //推荐
// or
new Schema({..}, { autoIndex: false }); //懒癌不推荐
```

每一个文档 document 都会被 mongoose 添加一个不重复的 `_id`，`_id` 的数据类型不是字符串，而是 ObjectID 类型。如果在查询语句中要使用 `_id`，则需要使用 findById 语句，而不能使用 find 或 findOne 语句。

用 mongoose 来插入数据，插入后数据库的数据中会多一个字段 `_v`，用来记录版本的，如果不需要，则可以设置 `versionKey`。

``` js
const mongoose = require('../index');

const UserSchema = new mongoose.Schema({
	username: { type: String, unique: true },
	password: { type: String }
}, { versionKey: false })

module.exports = mongoose.model('User', UserSchema);
```

#### 静态方法
通过 Schema 对象的 statics 属性添加静态方法：

``` js
const UserSchema = mongoose.Schema({
  name: String, 
  age: Number
});

UserSchema.statics.findByName = function (name, fn) {
  return this.find({name: new RegExp(name,'i')}, fn);
};

var User = mongoose.model('user', userSchema);
User.findByName('fido', function (err, doc) {
  console.log(doc);
});
```

#### 查询方法
通过 schema 对象的 query 属性，给 model 添加查询方法：

``` js
const UserSchema = mongoose.Schema({
  name: String, 
  age: Number
});

UserSchema.query.byName = function (name) {
  return this.find({name: new RegExp(name)});
}
```

#### 实例方法
Schema 类似创建表时的数据定义(不仅仅可以定义文档的结构和属性，还可以定义文档的实例方法、静态模型方法、复合索引等)：

``` js
//定义一个schema
let Schema = mongoose.Schema({
  category:String,
  name:String
});

// 定义实例方法
Schema.methods.eat = function(){
  console.log("I've eatten one "+this.name);
};
    
```

### Model
model 是由 schema 生成的模型，是一个构造器或者称为类，具有抽象属性和行为，实际上就相当于一个 collectio，可以对数据库进行增删查改。

``` js
// 它在数据库中会创建一个名为 users 的 collection
var User = mongoose.model('User', UserSchema);
```

* 参数一：模型名称，对应 mongodb 的集合名称，默认模型名称为小写。如果名称的最后一个字符是字母，则会变成复数；如果名称的最后一个字符是数字，则不变，所以这里创建的集合名字为'users',当我们对其添加数据时如果 users 已经存在，则会保存到其目录下，如果未存在，则会创建 users 集合，然后再保存数据。

* 参数二：是 Schema 的名字；

* 参数三：可选，用来指定集合名，如：let User = mongoose.model("User", UserSchema, "admin")。

> 注意：一定要将 model() 方法的第一个参数和其返回值设置为相同的值，否则会出现不可预知的结果。

当然也可以直接指定要链接的数据库中的哪个集合：

``` sql
const UserSchema = mongoose.Schema({
  name: String,
  friends: [String],
  age: Number
}, { collection: 'Users'});

const User = mongoose.model('User', UserSchema);
```

### Entity
由 Model 创建的真实的数据，他的操作也会影响数据库。

``` js
var user = new User({ 
  name: 'faychou',
  password: '0123456789'
});

console.log(user.name);  // 'faychou'
```

为 Entity 添加自定义方法，必须在 Schema 对象的 methods 属性中扩展 ：

``` js
const UserSchema = mongoose.Schema({
  name: String, 
  age: Number
});

// methods 必须加在 mongoose.model() 之前
UserSchema.methods.speak = function () {
  var greeting = this.name ? "my name is " + this.name : "I don't have a name";
  console.log(greeting);
}

const User = mongoose.model('User', userSchema);
const user = new User({name:'fay',age:18});

user.speak();
```

Mongoose 还有一个super featrue-- virtual property 该属性是直接设置在Schema上的。但是 VR 并不会真正的存放在 db 中，他只是一个提取数据的方法：

``` js
// schema 添加虚拟属性
userSchema.virtual('fullName').get(function(){
  return this.first+" "+this.last;
});

// 调用
user.fullName; 
```

> Schema、Model、Entity 的关系：Schema 生成 Model，Model 创造 Entity，Model 和 Entity 都可对数据库操作造成影响，但 Model 比 Entity 更具操作性。

存储数据步骤：定义Schema (骨架) > 创建model（模型）>  Entity实例化方法。

## CRUD
### 文档插入
通过 Model 创建的 Entity，必须通过 save() 方法才能将创建的文档保存到数据库的集合中：

```
save([options], [options.safe], [options.validateBeforeSave], [fn])
```

``` js
// err 为错误信息
// docs 为保存的文档对象
user.save(function (err,docs) {
  if (err) return console.error(err);
  console.log('数据插入成功！' + docs);
});
```

也可以使用模型 Model 的 create() 方法完成数据插入：

``` js
var user = {name:’fay’ , age:18}; 
User.create(user,function(err,docs) {
  if(err) return console.log(err); 
  console.log(’数据插入成功：' + docs);
});

// 也可以同时插入多条记录
User.create({name:’fay’,age:18},{name:’jay’,age:28},function(err,docs1,docs2) {
  if(err) return console.log(err); 
  console.log(’数据插入成功：',docs1,docs2);
});
```

也可以使用 Model 的 insertMany() 方法同时插入多条数据：

``` js
User.insertMany([ 
  {name: ”jack", password:'0123456789'}, 
  {name : ”baur", password:'0123456789'} 
 ], function(err, docs) { 
  if(err) return console.log(err); 
  console.log('保存成功：' + docs); 
});
```

``` js
// 简单 demo
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/test');
const con = mongoose.connection;
con.on('error', console.error.bind(console, '连接数据库失败'));
con.once('open',()=>{
  //定义一个schema
  let Schema = mongoose.Schema({
    name:String,
    age:Number
  });
    
  // 自定义方法
  Schema.methods.getAge = function() {
    console.log("I am "+this.age + "years old");
  }
    
  //继承一个 schema
  let Student = mongoose.model("Student",Schema);
  
  //生成一个 document
  let student = new Student({
    name:'faychou',
    age:18
  });
    
  //存放数据
  student.save((err,res) => {
    if(err) return console.log(err);
    res.getAge();
    
    //查找数据
    Student.find({name:'hanmeimei'},(err,data)=>{
      console.log(data);
    })
  });
});
```

### 查询
Mongoose 提供了三种方法来查询文档：find()、findById()、findOne()。

``` js
Model.find(conditions, [fields], [options], [callback]);

Model.findById(id, [projection], [options], [callback]);

// 返回查找到的所有实例的第一个
Model.findOne([conditions], [projection], [options], [callback]);

```

* Model：集合名字，如：User
* conditions：查询条件；
* fields：控制返回的字段；
* options：配置查询参数；
* callback：回调函数


``` js
Model.find({},callback) // 参数1忽略,或为空对象则返回所有集合文档

Model.find({},{'name':1, 'age':0},callback) //过滤查询,参数2: {'name':1, 'age':0} 查询文档的返回结果包含name , 不包含age.(_id默认是1)

Model.find({},null,{limit:20}) // limit 限制返回结果数量为20个,如不足20个则返回所有

Model.findOne({}, callback) // 查询找到的第一个文档

Model.findById('obj._id', callback) // 查询找到的第一个文档, 只接受 _id 的值查询

Model.count(conditions, [callback])  // 数量查询
```

查询条件：

* `$or`：满足数组中指定的条件的其中一个
* `$and`：满足数组中指定的所有条件
* `$nor`：不满足数组中指定的所有条件
* `$not`：反转查询，返回不满足指定条件的文档
* `$eq`：与指定的值相等
* `$gt`：大于指定值
* `$gte`：大于等于指定值
* `$lt`：小于指定值
* `$lte`：小于等于指定值
* `$ne`：与指定的值不相等
* `$in`：在多个值范围内
* `$nin`：与查询数组中指定的值中的任何一个都。，不匹配（不在多个值范围内）
* `$all`：匹配包含查询数组中指定的所有条件的数组字段
* `$regex`：正则，用于模糊查询
* `$size`：匹配数组字段的 length 与指定的大小一样的 document
* `$maxDistance`：范围查询，距离（基于LBS）
* `$mod`：取模运算
* `$near`：邻域查询，查询附近的位置（基于LBS）
* `$exists`：匹配指定字段是否存在
* `$type`：返回字段属于指定类型的文档
* `$elemMatch`：匹配数组字段中的某个值满足指定的所有条件
* `$within`：范围查询（基于LBS）
* `$box`：范围查询，矩形范围（基于LBS）
* `$center`：范围醒询，圆形范围（基于LBS）
* `$centerSphere`：范围查询，球形范围（基于LBS）
* `$slice`：查询字段集合中的元素（比如从第几个之后，第N到第M个元素）

``` js
// 查询年龄大于 18 的数据
User.find({age:{$gte:18}}, callback);

// 返回 age 字段大于 16 或者 age 字段 不存在 的文档
Model.find( { age: { $not: { $lte: 16 }}})

// 找出名字叫 john，并且年龄大于18的同学
User.find({ name: 'john', age: { $gte: 18 }}, callback);

// 使用点语法，可匹配嵌套的字段，其中字段名必须用引号引起来
Model.find({ 'name.last': 'wang' })

// 查询年龄大于等 21 而且小于等于 65 岁
User.find({age: {$gte: 21, $lte: 65}}, callback);

// 模糊查询
User.find({'username':{$regex:/m/i}}, callback);

// 找出年龄大于 18 且名字里存在'huo'的数据
User.find({age:{$gte:18},name:/huo/}, callback);

// 找出名字里存在'a'的数据，且只输出'name'字段
User.find({name:/a/},'name', callback);

// name LIKE john and only selecting the "name" and "friends" fields, executing immediately
User.find({ name: /john/i }, 'name friends', function (err, docs) { })

// 找出 age > 20 的文档中的第一个文档，且只输出 name 字段
User.findOne({age:{$gt : 20}},{name:1,_id:0}, callback);

// 找出 age>20 的文档中的第一个文档，且输出包含 name 字段在内的最短字段
User.findOne({age:{$gt : 20}},"name",{lean:true}, callback);

// 查找同时存在 2019 和 2020 的 document
Model.find({ year: { $all: [ 2019, 2020 ] } });
```

> 注意：如果使用第三个参数，前两个参数如果没有值，需要设置为 null:

``` js
// passing options
User.find({ name: /john/i }, null, { skip: 10 }, callback)

User.find(null,null,{skip:2}, callback);
```

如果要进行更复杂的查询，需要使用 `$where` 操作符，`$where` 操作符功能强大而且灵活，它可以使用任意的 JavaScript 语法作为查询的一部分，包含 JavaScript 表达式的字符串或者 JavaScript 函数：

``` js
User.find({$where:"this.x == this.y"},function(err,docs) {
  //[ { _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
  //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 } ]
});


User.find({$where:function() {
  return this.x !== this.y;
}},function(err,docs) {
  //[ { _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
  //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 } ]
});
```


案例：分页查询：

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

#### exec

使用 `query` 实例的 `exec()` 方法执行查询，即在链式语法的最后统一通过传入 `callback` 获取查询数据。

``` js
// 效果一样
Model.find(
  { name: /Dora/, age: { $gte: 16, $lt: 18 } },
  'name age -_id',
  { limit: 2, sort: 'age' },
  (err,res)=>{
    if (err) return handleError(err);
    console.log(res);
  }
});

let query = Model.
  find({ name: /Dora/ }).
  where('age').gte(16).lt(18).
  select('name age -_id').
  limit(2).sort('age');

  query.exec((err, res)=> {
    if (err) return handleError(err);
    console.log(res);
  });
```



### 就文档更新

更新文档可以使用以下几种方法：update()、updateOne()、updateMany()、findByIdAndUpdate()、fingOneAndUpdate()。

``` js
// Model.update(conditions, update, [options], [callback]);
User.update({_id: ObjectId(req.query.id)}, {name: req.query.name, author: req.query.author},(err,result) => {
  if(err) {
    res.send(err)
  }
  res.send('修改成功')
});

User.update({ age: { $gt: 18 } }, { oldEnough: true }, fn);
User.update({ name: 'Tobi' }, { ferret: true }, { multi: true },fn)

// 注意、新版本：将 update() 替换为 updateOne(), updateMany(),  replaceOne()
```
``` js
// Model.updateOne(conditions, doc, [options], [callback])
User.updateOne({ _id: Object("5d6cd43be93a8e41146ded63") }, { age: "1000" }, function (err, data) {
  if (err) console.log(err);
  console.log(data);
});

// 一次更新多条:
Model.updateMany(conditions, doc, [options], [callback])

Model.replaceOne(conditions, doc, [options], [callback])
```
``` js
// 更新指定ID：
// Model.findByIdAndUpdate(id, [update], [options], [callback])
User.findByIdAndUpdate(u._id, {
  username: 'sang',
}, (err, user) => {
  t.false(err);
  t.is(user.username, 'sang');
});
```
``` js
// 找到一条记录并更新
// Model.findOneAndUpdate([conditions], [update], [options], [callback])
User.findOneAndUpdate({
  username: 'i5ting for update 2',
}, {
  username: 'sang',
}, (err, user) => {
  t.false(err);
  t.is(user.username, 'sang');
});
```

* Model：集合名字，如：User
* conditions：查询条件；
* update：需要修改的数据，不能修改主键（_id）；
* options：控制选项；
* callback：回调函数

options 选项：

* safe (boolean)： 默认为 true。安全模式。

* upsert (boolean)： 默认为 false。如果不存在则创建新记录。

* multi (boolean)： 默认为 false。是否更新多个查询记录。

* runValidators： 如果值为 true，执行 Validation 验证。

* setDefaultsOnInsert： 如果 upsert 选项为 true，在新建时插入文档定义的默认值。

* strict (boolean)： 以 strict 模式进行更新。

* overwrite (boolean)： 默认为 false。禁用 update-only 模式，允许覆盖记录。

``` js
var param = {name:'fay',age:18}; 
var date = {$set:{name:'jack',age:26}};
Kitten.update(param,date,function(err,docs) {
  if(err) return console.log(err); 
  console.log('更改成功：' + docs);
});

// 查询 age 大于 20 的数据，并将其年龄更改为 40 岁
User.update({age:{$gte:20}},{age:40}, callback);

//同时更新多个记录，需要设置 options 里的 multi 为 true。下面将名字中有'a'字符的年龄设置为10岁
User.update({name:/a/},{age: 10},{multi:true}, callback)
```

如果设置的查找条件，数据库里的数据并不满足，默认什么事都不发生。

> 注意：update() 方法中的回调函数不能省略，否则数据不会被更新。如果回调函数里并没有什么有用的信息，则可以使用 exec() 简化代码：

``` js
User.update({name:/aa/},{age: 0},{upsert:true}).exec();
```

### 文档删除
同样也是提供多种方法：remove()、findOneAndRemove()、findByIdAndRemove()。

``` js
// Model.remove([conditions], [callback]) // 根据条件查找到并删除
User.remove({"_id": ObjectId(req.query.id)}, (err,result) => {
  if(err) {
    alert(err)
  }
  res.send('删除成功')
});

Model.findByIdAndRemove(id, [options], [callback]) // 根据id查找到并删除

Model.findOneAndRemove(conditions, [options], [callback])

// 注意、新版本：将 remove() 替换为 deleteOne() or deleteMany()
```
``` js
// Model.deleteOne(conditions, callback)
User.deleteOne({ name: 'Eddard Stark' }, function (err) {});

User.deleteOne({ _id: ObjectId("5d6cd43be93a8e41146ded63") }, function (err, data) {
  if (err) console.log(err)
  console.log(data)
});

// Model.deleteMany(conditions, callback)
User.deleteMany({ name: /Stark/, age: { $gte: 18 } }, function (err) {});
```

> 注意：
> 
> 1、remove 有两种形式，一种是文档的 remove() 方法，一种是 Model 的 remove() 方法。

> 2、回调函数不能省略，否则数据不会被删除。

``` js
var param = {name:’fay’}; 
Kitten.remove(param , function(err,docs) {
  if(err) return console.log(err); 
  console.log('删除成功：' + docs);
});
```

### 前后钩子
前后钩子即pre()和post()方法，又称为中间件，是在执行某些操作时可以执行的函数。

``` js
// 在执行find()方法之前，执行pre()方法
schema.pre('find',function(next) {
  console.log('我是pre方法');
  next();
});
```

post()方法并不是在执行某些操作后再去执行的方法，而在执行某些操作前最后执行的方法：

``` js
schema.post('find',function(docs){
  console.log('我是post方法1');
});
```

### 查询后处理方法
* sort() : 排序

* skip() : 跳过

* limit() : 限制

* select() : 显示字段

* exect() : 执行

* count() : 计数

* distinct() : 去重

``` js
// 按 age 从小到大排序
User.find().sort("age").exec(function(err,docs) {
  console.log(docs);
});

// 按 x 从小到大，age 从大到小排列
User.find().sort("x -age").exec(function(err,docs) {
  console.log(docs);
});

// 跳过1个，显示其他
User.find().skip(1).exec(function(err,docs) {
  console.log(docs);
});

//显示2个
User.find().limit(2).exec(function(err,docs) {
  console.log(docs);
});

// 显示 name、age 字段，不显示 _id 字段
User.find().select("name age -_id").exec(function(err,docs) {
  console.log(docs);
}); 

// 显示集合中的文档数量
User.find().count(function(err,count) {
  console.log(count);//4
}); 

// 返回集合中 x 的值
User.find().distinct('x',function(err,distinct) {
  console.log(distinct);//[ 1, 2 ]
}); 
```

## 三、搭配 node.js
### 连接数据库
使用 connect(url) 方法连接到 MongoDB 的数据库，数据库的名字是 test：

``` js
// 引入 mongoose
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/test');
```

如果还需要传递用户名、密码，则可以使用如下方式：

``` js
// mongoose.connect('mongodb://用户名:密码@主机地址:端口号/数据库,options');
mongoose.connect("mongodb://root:123456@localhost/test", options);
```

connect() 方法还接受一个可选对象 options，这里所包含的所有选项优先于连接字符串中传递的选项：

``` js
mongoose.connect(uri, options);
```

options的配置信息如下：

* db - 数据库设置
* server - 服务器设置
* replset - 副本集设置
* user - 用户名
* pass - 密码
* auth - 鉴权选项
* mongos - 连接多个数据库
* promiseLibrary

``` js
var options = {
  db: { native_parser: true },
  server: { poolSize: 5 },
  replset: { rs_name: 'myReplicaSetName' },
  user: 'myUserName',
  pass: 'myPassword'
}
mongoose.connect(uri, options);
```

如果要连接多个数据库，只需要设置多个 url 并以 `,` 隔开，同时设置 options 的字段 mongos:true：

``` js
mongoose.connect('urlA,urlB,...', {
  mongos : true 
});
```

connect() 函数也可以接受一个回调参数：

``` js
const mongoose = require('mongoose');
mongoose.connect("mongodb://localhost/test", function(err) {
  if(err) return console.log('连接失败');
  console.log('连接成功');
});
```

### 断开连接
使用 disconnect() 方法可以断开数据库的连接：

``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
  if(err) return console.log('连接失败');
  console.log('连接成功');
});

setTimeout(function(){
  mongoose.disconnect(function() {
    console.log("断开连接");
  });
}, 2000);
```

### 创建 schema
``` js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var mySchema = new Schema({
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
```

### 简单案例：

``` js
// app.js
const mongoose = require('mongoose');

// 连接数据库 test
mongoose.connect('mongodb://username:password@localhost/test', {
  authSource: "admin",
  useNewUrlParser: true,
  useCreateIndex: true
});

// 实例化连接对象
const db = mongoose.connection;

// 使用 Connetion 监听连接状态
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

### population
Mongodb 本来就是一门非关系型数据库。 但有时候又需要联合其他的集合进行数据查找。 MongoDB 3.2 之后，也有像 sql 里 join 的聚合操作，那就是 $lookup ,而 Mongoose，拥有更强大的 populate()，用来连接多集合查询数据。

Population 可以自动替换 document 中的指定字段，替换内容从其他 collection 获取。 我们可以填充（populate）单个或多个 document、单个或多个纯对象，甚至是 query 返回的一切对象。

只要提供某一个 collection 的`_id` , 使用关键字 ref 来指明外联的数据库的名字，就可以实现完美的联合查询。一般,我们需要在 schema 中就定义好：

语法：

``` js
populate(path, [select], [model], [match], [options])

// path：String 或 Object，指定要查询的表。

// select：Object 或 String，可选，指定填充 document 中的哪些字段，默认会填充 _id。
// Object类型的时，格式如: {name: 1, _id: 0}，为0表示不填充，为1时表示填充。
// String类型的时，格式如: “name -_id”，用空格分隔字段，在字段名前加上-表示不填充。

// model：可选，指定关联字段的 model，如果没有指定就会使用 Schema 的 ref。

// match：可选，Object，指定附加的查询条件。

// options：可选，指定附加的其他查询选项，如排序以及条数限制等等。
```

例如：

``` js
const mongoose = require('mongoose'),
  Schema = mongoose.Schema
  
const studentSchema = Schema({
  student_name: String,
  age: Number,
  classId: { 
    type: Schema.Types.ObjectId, 
    ref: 'Classes' 
  }
  // 如果是一对多，也可以写为数组形式：
  // friendsId: [{type: Schema.Types.ObjectId, ref: 'Friend'}]
});
 
const classesSchema = Schema({
  class_name: String,
  fans: [{ type: Schema.Types.ObjectId, ref: 'Person' }]
});

const Student = mongoose.model('Student', studentSchema);
const Classes = mongoose.model('Classes', classesSchema);
```

> 注意：ref 选项告诉 Mongoose 在填充的时候使用哪个 model，本例中为 Classes model，即 mongoose.model('Classes', classesSchema) 第一个参数值。 所有储存在此的 `_id` 都必须是 Classes model 中 document 的 `_id`。

> 注意: ObjectId、Number、String 以及 Buffer 都可以作为 refs 使用。 但是最好还是使用 ObjectId。

``` js
const classes = new Classes({
  class_name: 'class one'
});

classes.save(function (err) {
  if (err) return handleError(err);

  const student = new Student({
    student_name: 'fay',
    age: 18,
    classId: classes._id
  });

  student.save(function (err) {
    // ...
  });
});
```

在查询时将 populate 的第一个参数对应 studentSchema 中外链的字段

``` js
Student
  .findOne({ student_name: 'Casino' })
  .populate('classId')
  .exec(function (err, doc) {
    if (err) return handleError(err);
    console.log(doc);
  });
```

如果我们只想返回填充的文档某些字段，这时可以将所需的字段名称作为第二个参数传递给 populate ,每个字段空格隔开 加 "-" 表示不返回：

``` js
Student
  .findOne({ student_name: 'Casino'})
  .populate('classId', 'class_name -_id') // 返回 class_name 字段，不包括 _id
  .exec(function (err, doc) {
    if (err) return handleError(err);

    console.log(doc);

    // ...
  });
```

需要填充多个路径时，只需要多次调用populate()方法即可：

``` js
Student
  .find(...)
  .populate('fans')
  .populate('author')
  .exec();
```

但是，如果在同一个路径上多次调用 populate() 方法，仅最后一次调用会生效：

``` js
// The 2nd `populate()` call below overwrites the first because they
// both populate 'fans'.
Student
  .find()
  .populate({ path: 'fans', select: 'name' })
  .populate({ path: 'fans', select: 'email' });
// The above is equivalent to:
Student.find().populate({ path: 'fans', select: 'email' });
```

按年龄 age 来对的 fans 进行筛选，并且只返回他们的名字，并且最多返回其中的5个。这时，可以像下面这样操作：

``` js
Student
  .find(...)
  .populate({
    path: 'fans',
    match: { age: { $gte: 21 }},
    // Explicitly exclude `_id`, see http://bit.ly/2aEfTdB
    select: 'name -_id',
    options: { limit: 5 }
  })
  .exec();
```

#### 简单联表操作 demo
``` js
// 类别 category
const mongoose = require('mongoose');

const CategorySchema = mongoose.Schema({
  number: { 
    type: Number, 
    required: true, 
    index: true, 
    unique: true, 
    min:[1000000000, '位数不足'], 
    max: [9999999999, '位数过长'] 
  },
  name: { 
    type: String, 
    required: true, 
    validate: { 
      validator: (v) => v.trim().length, 
      message: '名称不能为空'
    } 
  },
  description: { type: String },
  posts: [{ 
    type: Schema.Types.ObjectId, 
    ref: 'Post' 
  }],
  recommend: { type: Boolean },
  index: { type: Number }
},
{ timestamps: true }
);

module.exports = mongoose.model('Category', CategorySchema)
```

``` js
// 文章 post
const mongoose = require('mongoose');

const PostSchema = mongoose.Schema({
  title: { 
    type: String, 
    required: true, 
    unique: true 
  },
  description: { type: String },
  content: { type: String },
  category: {
    type: Schema.Types.ObjectId, 
    ref: 'Category', 
    index: true 
  },
  comments: [{ type: Schema.Types.ObjectId, ref: 'Comment' }],
  likes: [{ type: Schema.Types.ObjectId, ref: 'Like' }],
  imgUrl: { type: String },
  recommend: { type: Boolean },
  index: { type: Number }
},
{
  timestamps: true
}
)

module.exports = mongoose.model('Post', PostSchema);
```

在对类别的操作中， 都需要使用 populate 操作符显示出所包括的 posts 中的 title ：

``` js
/* 加载所有类别 */
app.get('/categories', (req, res) => {
  Category.find().populate('posts','title').select("number name description recommend index").exec((err, docs) => {
    if (err) return res.status(500).json({code: 0, message: err.message, err});
    return res.status(200).json({code: 1, message: '获取类别成功', result: {docs}})
  });
});

/* 新增一个类别 */
app.post('/categories', adminAuth, (req, res) => {
  Category(req.body).save((err, doc) => {
    if (err) return res.status(500).json({code: 0, message: err.message, err});
    doc.populate({path:'posts',select:'title'}, (err, doc) => {
      if (err) return res.status(500).json({code:0, message: err.message, err});
      return res.status(200).json({code: 1, message: '新增成功', result: {doc}});
    })      
  })
})
// ...
```

在对文章的操作中，则需要显示出类别 category 的 number 属性：

``` js
/* 按照 id 加载一篇文章 */
app.get('/posts/:id', (req, res) => {
  Post.findById(req.params.id).populate('category','number').exec((err, doc) => {
    if (err) return res.status(500).json({code:0, message:err.message, err});
    if (doc === null) return res.status(404).json({code:0, message:'文章不存在'});
    return res.status(200).json({code:1, message:'获取文章成功', result:{doc}});
  })
})

/* 加载所有文章 */
app.get('/posts', (req, res) => {
  Post.find().select("title likes comments recommend imgUrl index").populate('category','number').sort("-createdAt").exec((err, docs) => {
    if (err) return res.status(500).json({code: 0, message: err.message, err});
    return res.status(200).json({code: 1, message: '获取文章成功', result: {docs}});
  });
});
```

在新增、更新和删除文章的操作中，都需要重建与 category 的关联

``` js
// 关联 category 的 posts 数组
fnRelatedCategory = _id => {
  Category.findById(_id).exec((err, categoryDoc) => {
    if (err) return res.status(500).json({ code: 0, message: err.message, err });
    if (categoryDoc === null) return res.status(404).json({code:0, message:'该类别不存在，请刷新后再试'});
    Post.find({ category: _id }).exec((err, postsDocs) => {
      if (err) return res.status(500).json({ code: 0, message: err.message, err })
      categoryDoc.posts = postsDocs.map(t => t._id)
      categoryDoc.save(err => {
        if (err) return res.status(500).json({ code: 0, message: err.message, err })
      })
    })
  })
}

/* 按照 id 更新一篇文章 */
app.put('/posts/:id', adminAuth, (req, res) => {
  Post.findById(req.params.id).exec((err, doc) => {
    if (err) return res.status(500).json({code: 0, message: err.message, err});
    if (doc === null) return res.status(404).json({code: 0, message: '文章不存在，请刷新后再试'});
    for (prop in req.body) {
      doc[prop] = req.body[prop]
    }
    doc.save((err) => {
      if (err) return res.status(500).json({code: 0, message: err.message, err});
      doc.populate({path:'category',select:'number'}, (err, doc) => {
        if (err) return res.status(500).json({code:0, message: err.message, err});
        fnRelatedCategory(doc.category._id)        
        return res.status(200).json({code: 1, message: '更新成功', result: {doc}})
      });
    });
  });
});
//...
```

### 创建连接池
