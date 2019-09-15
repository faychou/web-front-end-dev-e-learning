## 三、基础
### 创建数据库
MongoDB 中默认的数据库为 test，如果你没有创建新的数据库，集合将存放在 test 数据库中。

``` bash
# 如果数据库不存在，则创建数据库，否则切换到该数据库。
use database-name```

#### 查看数据库
默认为存在“admin”和“local”两个数据库。

``` bash
show dbs```

新创建的数据库并不在数据库的列表中， 要显示它，需要向数据库插入一些数据，所以看不到 test 这个数据库，因为里面还没有数据。

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