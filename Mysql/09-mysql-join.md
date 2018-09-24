# join

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

### 内连接
返回两张表共有的数据，相当于求交集：

``` sql
select * from user inner join user_info on user.id=user_info.user_id;
# 也可以对表重命名
select * from user a inner join user_info b on a.id=b.user_id;

# 或者
SELECT * FROM user,user_info WHERE user.id=user_info.user_id;

# 只返回固定字段
SELECT user.id,user.username,user_info.email,user_info.phone FROM user,user_info WHERE user.id=user_info.user_id;
```

### 左连接
返回两个表的交集然后再外加左表剩下的数据：

``` sql
select * from user a left join user_info b on a.id=b.user_id;
```

扩展：返回左表独有数据：

``` sql
select * from user a left join user_info b on a.id=b.user_id where b.user_id is null;
```

### 右连接
返回两个表的交集然后再外加右表剩下的数据：

``` sql
select * from user a right join user_info b on a.id=b.user_id;
```

扩展：返回右表独有数据：

``` sql
select * from user a right join user_info b on a.id=b.user_id where a.id is null;
```

### 全连接
返回两张表所有的数据，也就是两个集合的并集：

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
