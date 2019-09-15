## 更新数据
``` sql
# 语法：db.collection.update(query, update, options)¶
db.usercollection.update({'name':'faychou'},{'name','jaychou'})# 默认更新会替换掉之前所有的，加上$set后只更新部分，其他不变db.usercollection.update({'age':18},{$set:{'name':'jaychou'}})
```
``` sql
# db.collection.updateOne(filter, update, options) New in version 3.2.
db.restaurant.updateOne(
  { "name" : "Central Perk Cafe" },
  { $set: { "violations" : 3 } }
);

db.members.updateOne(
  { _id: 1 },
  [
    { $set: { status: "Modified", comments: [ "$misc1", "$misc2" ] } },
    { $unset: [ "misc1", "misc2" ] }
  ]
);
   
# db.collection.updateMany(filter, update, options) New in version 3.2.
db.restaurant.updateMany(
  { violations: { $gt: 4 } },
  { $set: { "Review" : true } }
);

# db.collection.replaceOne() New in version 3.2.
db.restaurant.replaceOne(
  { "name" : "Central Perk Cafe" },
  { "name" : "Central Pork Cafe", "Borough" : "Manhattan" }
);
```