# update
``` bash
update user set name='tom' where age= '20';
```

这里的 where 子句是可选的，如果不指定的话，表中的每个记录都被更新。

``` bash
# 将所有人的年龄增加1
update user set age=age+1;
```

也可以一次更改多列数据：

``` bash
# 将名为 faychou 的数据修改为 名 jack ，年龄 10
update user set name= 'jack',age=10 where name='faychou';
```

