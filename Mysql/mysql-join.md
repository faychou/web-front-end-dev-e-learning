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
返回两张表共有的数据：

``` sql
select * from user a inner join user_info b on a.id=b.user_id;
```

### 左外连接
返回左边表的所有的数据：

``` sql
select * from user a left join user_info b on a.id=b.user_id;
```

### 右外连接
返回右边表所有的数据：

``` sql
select * from user a right join user_info b on a.id=b.user_id;
```

### 左连接
返回左表独有数据：

``` sql
select * from user a left join user_info b on a.id=b.user_id where b.user_id is null;
```

### 右连接
返回右表独有数据：

``` sql
select * from user a right join user_info b on a.id=b.user_id where a.id is null;
```

### 全连接
返回两张表所有的数据：

``` sql
select * from user a right join user_info b on a.id=b.user_id 
union 
select * from user a left join user_info b on a.id=b.user_id;
```

返回两张表中特有数据的并集：

``` sql
select * from user a right join user_info b on a.id=b.user_id where a.id is null 
union 
select * from user a left join user_info b on a.id=b.user_id where b.user_id is null;
```
