## Bug 解决
### mongodb 非正常关闭
``` bash
# 先查看进程
ps aux|grep mongo

# 结束 mongodb 进程
kill 12730

# 首先删除 mongod.lock 文件
rm data/db/mongod.lock

# 删除 mongodb.log 

# 修复
mongod --dbpath /Users/faychou/mongodb/data/db --repair
## 注意，这里一定要写 --dbpath

# 最后重启
mongod --config /Users/faychou/mongodb/data/db/mongodb.config
```

### mongoose 连接失败
通过 nodejs 连接数据库时，如果按照官网的例子来写时会发现一直连接失败，显示没有权限：

![](mdImgs/mongoose-connect-bug.png)

后来研究官网 API 文档时，发现必须设置一个 options 选项。

``` js
mongoose.connect("mongodb://username:password@localhost:27017/blog", { 
  authSource: "admin"
});
```

意思是如果数据库认证需要依赖于另外一个数据库名称时则需要设置 authSource=admin，因为我们的数据库账号密码是存储在 admin 数据库的，而现在需要连接的是另外一个数据库，所以必须加上这个参数，否则无法成功连接。

### mongoose插入数据后发现文档中多了‘'__v‘' 字段
``` json
{
    "_id" : ObjectId("5d6d4dab3492d9497e07e1e6"),
    "label_name" : "新增加",
    "__v" : 0
}
```

这是用来记录版本号的，可以在 Scheam 中添加一个 {versionKey: false} 去掉：

``` js
const labelSchema = new Schema({
  label_name: {
    type: String,
    required: true
  }
}, {versionKey: false});
```
