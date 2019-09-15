### 删除数据
``` sql# 删除当前集合指定内容，option为匹配条件，如{'name':'jaychou'}：db.usercollection.remove(option)

# 默认地，remove()方法将删除匹配指定条件的所有文档。使用justOne可选参数可以限制删除操作只删除一条。
db.usercollection.remove({ "borough": "Queens" }, { justOne: true })

# 删除一个集合中的所有文档，传递一个空的条件文档即可
db.usercollection.remove( {} )

# db.collection.deleteOne(<filter>,
#    {
#       writeConcern: <document>,
#       collation: <document>
#    }) New in version 3.2
db.orders.deleteOne( { "_id" : ObjectId("563237a41a4d68582c2509da") } );

# db.collection.deleteMany(<filter>,
#    {
#       writeConcern: <document>,
#       collation: <document>
#    }) New in version 3.2
db.orders.deleteMany( { "client" : "Crude Traders Inc." } );

db.orders.deleteMany( { "stock" : "Brent Crude Futures", "limit" : { $gt : 48.88 } } );
```
``` sql
# 删除当前数据库指定集合：db.usercollection.drop()

# 删除数据库
use my-dbdb.dropDatabase()
```