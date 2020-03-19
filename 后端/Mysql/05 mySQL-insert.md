# insert
``` bash
insert into 表名 (字段1,字段2,...) values(值1,值2,...);
# or
insert 表名 (字段1,字段2,...) values(值1,值2,...);
```

使用 into 关键字，此时 values 里面的数据必须一一对应表中所有的字段，也就是说,表中有几个字段,那么 values 里面就必须有几个值；

第二种不使用 into 则不需要一一对应所有字段，只需要将第一个括号的字段名与第二个括号的值对应即可，

### 添加单条数据
``` bash
# 向 user 表中添加一条数据
insert into user values(null,'faychou',18);
```

如果设置了主键并且自动增长，添加的时候需要把对应的主键值设置为 null 或者数字 “0”：

插入部分数据, 或者不按照列的顺序进行插入, 可以使用这样的形式进行插入:

``` bash
insert user (name, sex, age) values("lily", "女", 21);
```

### 将每一列插入默认值
```
INSERT INTO users () VALUES();
```

### 添加多条数据
``` bash
insert user values(null,'lily',23),(null,'lucy',34);
```

### 添加部分列的数据
``` bash
insert user(name) values('baur');
insert user(name) values('will'),('roke');

# 使用这种形式 insert 语句不能插入多行
insert user set name='john';
```

### 使用 select 添加数据
``` bash
# 将 user_other 表中的数据添加到 user 表中，结构完全一致
insert into user select * from user_other;

# 取某个值添加
insert into user(name) select name from user_other;
```

### 插入或更新
插入一条新记录（INSERT），但如果记录已经存在，就更新该记录。

``` sql
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;
```

