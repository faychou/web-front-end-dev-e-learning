## 导入数据
在命令行中执行mongoimport命令将上面下载的数据文件中的数据导入到test数据库的restaurants集合中。如果此集合已经存在，下面的操作会先删除。

``` bash
mongoimport --db test --collection restaurants --drop --file C:\data\dataset.json
```

dataset.json的数据内容

``` json
{
  "address": {
     "building": "1007",
     "coord": [ -73.856077, 40.848447 ],
     "street": "Morris Park Ave",
     "zipcode": "10462"
  },
  "borough": "Bronx",
  "cuisine": "Bakery",
  "grades": [
     { "date": { "$date": 1393804800000 }, "grade": "A", "score": 2 },
     { "date": { "$date": 1378857600000 }, "grade": "A", "score": 6 },
     { "date": { "$date": 1358985600000 }, "grade": "A", "score": 10 },
     { "date": { "$date": 1322006400000 }, "grade": "A", "score": 9 },
     { "date": { "$date": 1299715200000 }, "grade": "B", "score": 14 }
  ],
  "name": "Morris Park Bake Shop",
  "restaurant_id": "30075445"
}
```

mongoimport命令连接到本机运行的mongod实例，如果要把数据导到不同主机，不同端口的实例，可以指定主机和端口，使用参数 --host和--port。

## 备份、还原
``` bash
# 还原数据库，–drop：先删除所有的记录，然后恢复
mongorestore -h IP –port 端口 -u 用户名 -p 密码 -d 数据库 –drop 文件存在路径

# -d 表示导出指定数据库，不加则表示导出所有数据库
mongodump -h IP –port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径

# 导出表格格式的数据库
mongoexport -h IP –port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 -f 字段 -q 条件导出 –csv -o 文件名

# -f：导出指定字段，以分号分割，如：-f name,email,age
# -q：根据查询条件导出，如：-q '{ "uid" : "100" }'
# –csv：导出文件格式为 csv 的，因为大部分的关系型数据库都是支持 csv
```

