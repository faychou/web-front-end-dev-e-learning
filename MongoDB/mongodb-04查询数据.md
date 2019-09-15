# 查询数据
## db.usercollection.find()

``` sql
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
db.usercollection.find({ $or: [ { "cuisine": "Italian" }, { "address.zipcode": "10075" } ] })# 限制数量：
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

## db.usercollection.findOne(query, projection)
``` sql
db.bios.findOne(
  {
    $or: [
      { 'name.first' : /^G/ },
      { birth: { $lt: new Date('01/01/1945') } }
    ]
  }
)
```

## MongoDB 条件表达式
表达式	含义
$gt	>
$gte	>=
$eq	=
$ne	!=
$lt	<
$lte	<=
$in	in(后面的值为bson对象数组)
$nin	not in(后面的值为bson对象数组)

## 聚合框架
一般查询可以通过 find 方法，但如果是比较复杂的查询或者数据统计的话，find 可能就无能为力了，这时就需要 aggregate（聚合）。聚合框架是 MongoDB 的高级查询语言，它允许我们通过转换和合并多个文档中的数据来生成新的单个文档中不存在的信息，包活对数据进行筛选，投射，分组，排序，限制或跳过。

mongoDB 中有许多操作符，在 aggregate 中可以使用的操作符叫做管道操作符：

* `$project`：投射操作符，用于重构每一个文档的字段，可以提取字段，重命名字段，甚至可以对原有字段进行操作后新增字段

* `$match`：匹配操作符，用于对文档集合进行筛选

* `$group`：分组操作符，用于对文档集合进行分组

* `$unwind`：拆分操作符，用于将数组中的每一个值拆分为单独的文档

* `$sort`：排序操作符，用于根据一个或多个字段对文档进行排序

* `$limit`：限制操作符，用于限制返回文档的数量

* `$skip`：跳过操作符，用于跳过指定数量的文档

* `$lookup`：连接操作符，多表关联（3.2版本新增）

* `$count`：统计操作符，用于统计文档的数量

* `$geoNear`：选择某个地理位置附近的的文档。

* `$out`：把管道的结果写入某个集合。

* `$redact`：控制特定数据的访问。

### `$lookup`
将每个输入待处理的文档，经过 `$lookup` 阶段的处理，输出的新文档中会包含一个新生成的数组列（户名可根据需要命名新 key 的名字 ）。数组列存放的数据是来自被 Join 集合的适配文档，如果没有，集合为空（即 为[ ]）。

``` js
// 语法：
{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
}

/*
* from：同一个数据库下等待被 Join 的集合。

* localField：源集合中的 match值，如果输入的集合中，某文档没有 localField 这个Key（Field），在处理的过程中，会默认为此文档含有 localField：null的键值对。

* foreignField：待 Join 的集合的 match 值，如果待 Join 的集合中，文档没有foreignField 值，在处理的过程中，会默认为此文档含有 foreignField：null 的键值对。

* as：为输出文档的新增值命名。如果输入的集合中已存在该值，则会覆盖掉
*/
```

例如：

``` sql
# 订单集合
db.orders.insert([
   { "_id" : 1, "item" : "almonds", "price" : 12, "quantity" : 2 },
   { "_id" : 2, "item" : "pecans", "price" : 20, "quantity" : 1 },
   { "_id" : 3  }
])

# 商品库存集合
db.inventory.insert([
   { "_id" : 1, "sku" : "almonds", description: "product 1", "instock" : 120 },
   { "_id" : 2, "sku" : "bread", description: "product 2", "instock" : 80 },
   { "_id" : 3, "sku" : "cashews", description: "product 3", "instock" : 60 },
   { "_id" : 4, "sku" : "pecans", description: "product 4", "instock" : 70 },
   { "_id" : 5, "sku": null, description: "Incomplete" },
   { "_id" : 6 }
])

# 查询订单表对应商品的库存
db.orders.aggregate([
   {
     $lookup:
       {
         from: "inventory",
         localField: "item",
         foreignField: "sku",
         as: "inventory_docs"
       }
  }
])
```

