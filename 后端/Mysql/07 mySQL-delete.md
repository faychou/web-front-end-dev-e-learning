# delete
``` sql
# 语法：DELETE from 表名 WHERE 删除条件;

# 将 user 中名为 jack 的数据删除
DELETE from user WHERE name= 'jack';

# 一次删除多条记录
DELETE FROM user WHERE id>=5 AND id<=7;
```

WHERE 子句指定哪些记录应该删除。它是可选的，如果不指定的话，将会删除所有的记录。

``` sql
DELETE from students;
```

