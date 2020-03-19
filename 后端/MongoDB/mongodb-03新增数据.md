# 插入文档
MongoDB 提供了3个插入文档的方法：

### db.collection.insert()

``` sql
db.usercollection.insert({'key':'value'})
db.usercollection.insert({ 'username' : 'faychou', 'email' : 'faychou@163.com' })

db.usercollection.insert([
  { 
    'username' : 'faychou', 
    'email' : 'faychou@qq.com' 
  },
  { 
    'username' : 'jack', 
    'email' : 'jack@163.com' 
  }
])```

### db.collection.insertOne()
用于向集合插入单个文档。

``` sql
db.usercollection.insertOne({ 
  'username' : 'faychou', 
  'email' : 'faychou@163.com' 
})
```

### db.collection.insertMany()
用于向集合插入多个文档。

``` sql
db.usercollection.insertMany([
  { 
    'username' : 'faychou', 
    'email' : 'faychou@qq.com' 
  },
  { 
    'username' : 'jack', 
    'email' : 'jack@163.com' 
  }
])
```

