# update
``` sql
# 语法：UPDATE 表名 SET 字段1=值1, 字段2=值2 WHERE 更新条件;
UPDATE user SET name='tom' WHERE age= '20';

# 把所有80分以下的同学的成绩加10分
UPDATE students SET score=score+10 WHERE score<80;
```

这里的 where 子句是可选的，如果不指定的话，表中的每个记录都被更新。

``` sql
# 将所有人的年龄增加1
UPDATE user SET age=age+1;
```

也可以一次更改多列数据：

``` sql
# 将名为 faychou 的数据修改为 名 jack ，年龄 10
UPDATE user SET name= 'jack',age=10 WHERE name='faychou';
```

