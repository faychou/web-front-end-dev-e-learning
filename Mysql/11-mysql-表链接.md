# 连接查询

建立两张有外键关联的表：

``` sql
CREATE TABLE `user`(  
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(30),
  `userpass` VARCHAR(40),
  PRIMARY KEY (`id`)
) ENGINE=INNODB CHARSET=utf8mb4;

CREATE TABLE `user_info`(  
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `realname` VARCHAR(20),
  `email` VARCHAR(20),
  `phone` VARCHAR(20),
  `user_id` INT(11),
  PRIMARY KEY (`id`),
  FOREIGN KEY (`user_id `) REFERENCES `user`(`id`)
) ENGINE=INNODB CHARSET=utf8mb4;
```

## 内连接
返回两张表共有的数据，相当于求交集：

### 语法
``` sql
-- 方式一：
select 列名 from 表1 [inner] join 表2 on 表1.列名 = 表2.列名

-- 方式二：
select 列名 from 表1,表2 where 表1.列名 = 表2.列名
```

### 案例：

``` sql
select * from user inner join user_info on user.id=user_info.user_id;
# 也可以对表重命名
select * from user a inner join user_info b on a.id=b.user_id;

# 或者
SELECT * FROM user,user_info WHERE user.id=user_info.user_id;

# 只返回固定字段
SELECT user.id,user.username,user_info.email,user_info.phone FROM user,user_info WHERE user.id=user_info.user_id;
```

> 注意：INNER JOIN 查询语法：

> 1. 先确定主表，仍然使用FROM <表1>的语法；
> 2. 再确定需要连接的表，使用INNER JOIN <表2>的语法；
> 3. 然后确定连接条件，使用ON <条件...>，这里的条件是s.class_id = c.id，表示students表的class_id列与classes表的id列相同的行需要连接；
> 4. 可选：加上WHERE子句、ORDER BY等子句。

### 多张表的连接
``` sql
-- 需求：列出学生每门课程对应的成绩
select * from students,scores,courses,teachers
where students.studentsId=scores.studentsId
and scores.coursesId=courses.coursesId
and courses.teachersId=teachers.teachersId

-- 或者
select * from students join scores on students.studentsId=scores.studentsId
join courses on scores.coursesId=courses.coursesId
join teachers on courses.teachersId=teachers.teachersId
```

## 外连接
### 左外连接
将左表的信息全部显示，右边的表与之匹配，如果没有匹配的则相应的显示为 null:

``` sql
-- 语法
select * from 表1 left [outer] join 表2 on 表1.列名=表2.列名;

-- 例子
select * from user a left join user_info b on a.id=b.user_id;
```

扩展：返回左表独有数据：

``` sql
select * from user a left join user_info b on a.id=b.user_id where b.user_id is null;
```

### 右外连接
以显示完所有右表的信息为核心,如果左表没有符合的信息，直接为空(NULL)：

``` sql
-- 语法
select * from 表1 right [outer] join 表2 on 表1.列名=表2.列名;

-- 例子
select * from user a right [outer] join user_info b on a.id=b.user_id;
```

扩展：返回右表独有数据：

``` sql
select * from user a right join user_info b on a.id=b.user_id where a.id is null;
```

### 全外连接
全连接为 full outer join ，返回两张表所有的数据，也就是两个集合的并集，但是 mysql 不支持，需要用到 union 来实现全连接：

``` sql
select * from user a right join user_info b on a.id=b.user_id 
union 
select * from user a left join user_info b on a.id=b.user_id;
```

扩展：返回两张表中特有数据的并集：

``` sql
select * from user a right join user_info b on a.id=b.user_id where a.id is null 
union 
select * from user a left join user_info b on a.id=b.user_id where b.user_id is null;
```

## USING 子句
MySQL 中连接 SQL 语句中，ON 子句的语法格式为：table1.column_name = table2.column_name。当模式设计对联接表的列采用了相同的命名样式时，就可以使用 USING 语法来简化 ON 语法，格式为：USING(column_name)。 

``` sql
SELECT * FROM user INNER JOIN user_info USING(id);
```
