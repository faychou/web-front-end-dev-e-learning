# DBRef
MongoDB 建模有两种方式，一种是内嵌(Embed)，另一种是连接(Link)。

一对一用 Embed 这种，比如收集用户的 address 字段，该字段包含 省、市、区 三个字段，建模如下：

```sql
{
  address: {
    province: "四川",
    city: "成都",
    county: "锦江"
  }
}
```

一对多关系，如一篇文章有多条评论：

``` sql
comments: [
  {
    title: "",
    body: "",
    created: ""
  },
  {
    title: "",
    body: "",
    created: ""
  }
]
```

由于 MongoDB 对单个文档(document)有大小 16M 限制，设计时也要将这个限制纳入考虑。

而如果是多对多的关系，就需要连接连个集合。MongoDB 有两种方法，一种是手动引用（Manual References），另一种就是 DBRef。

语法：

``` sql
{$ref: <value>, $id: <value>, $db: <value>}
```

DBRef 接收主要3个参数：

* collection: 指定原始数据所在的集合
* id: 指定引用文档的 id
* database: 指定所引用数据库的名称

如：在用户表中使用 DBRef：
 
``` sql
{
    "_id":ObjectId("5d48d722ac452e644790c467"),
    "address": {
        "$ref": "address",
        "$id": ObjectId("5d4714dc5cac8cb2d59eebe8"),
        "$db": "blog"
    },
    "phone": "12345678901",
    "name": "Jack"
}
```

address 字段指定了引用的地址文档是在 blog 数据库下的 address 集合，id 为 5d4714dc5cac8cb2d59eebe8。

通过指定 `$ref` 参数（address 集合）来查找集合中指定 id 的用户地址信息：

``` sql
>var user = db.users.findOne({"name":"Jack"})
>var dbRef = user.address
>db[dbRef.$ref].findOne({"_id":(dbRef.$id)})
# MongoDB4.0 
# >db[dbRef.$ref].findOne({"_id":ObjectId(dbRef.$id)})
```

### 示例
创建标签集合：

``` sql
db.label.insert({name: "html"})
db.label.insert({name: "css"})
```

创建文章集合：

``` sql
db.article.insert({name: "认识HTML", author: 'faychou', labels: [ DBRef("label", ObjectId("59b51b36a36f6ee8c75b0f68"), "blog"), DBRef("label", ObjectId("59b51b44a36f6ee8c75b0f69"), "blog")]})
```

引用：

``` sql
db.article.findOne({"name": "认识HTML"}).labels[0].fetch()
```
