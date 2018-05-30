# mySQL
## DDL语句
``` bash
# 创建数据库
create database e_mall;

# 删除数据库
drop database e_mall;

# 创建表
create table if not exists `product_order`(
`id` bigint(20) unsigned auto_increment,
`product_id` bigint(20) unsigned not null comment '产品id',
`count` int unsigned not null comment '购买数量',
`amount` decimal(10,2) not null comment '总价格',
`create_time` datetime not null comment '下单时间',
`modified_time` datetime default null comment '修改时间',
primary key (`id`)
)engine=InnoDB default charset=utf8mb4;

# 查看表信息
desc product_order;
# 查看创建语句
show create table product\G

# 删除表: drop
drop table product_order;

# 修改表: alter
# 添加列: add
alter table product_order add column user_id bigint(20) unsigned not null comment '用户id' after id;
# 删除列: drop
alter table product_order drop column user_id;
# 修改列定义: modify
alter table product_order modify amount decimal(10,2) not null comment '总价(元)';
# 修改列定义及列名: change
alter table product_order change count quantity int unsigned not null comment '购买数量';
# 添加普通索引: add index
alter table product_order add index idx_product_id (`product_id`);
# 删除普通索引: drop index
alter table product_order drop index idx_product_id;
# 添加唯一索引
alter table product_order add unique (`order_serial`);
# 修改表名: alter table rename
alter table product_order rename product_orders;
# 跨库修改表名: rename table to
rename table e_mall_2.product to e_mall_0.product;
```

## DML语句
``` bash
# 插入记录: insert
insert into product_order(product_id, quantity, amount, create_time) values (3, 1, 7388.00, now());
# insert select语句
insert into product_order(product_id, quantity, amount, create_time)
  select id, 2, 2*price, now() from product where id = 1;

# 更新记录: (join) update
update product_order o join product p on o.product_id = p.id
  set o.quantity = 5, amount = 5*p.price where o.id = 1;

# 删除记录: delete
delete from product_order where id = 10;
# 删除表中所有数据: truncate, 与不带where条件的delete效果相同, 需要事务资源少, 速度快
# delete是DML语句, truncate是DDL语句
truncate table product_order;

# 查询记录
select * from product_order where quantity > 0 order by amount desc limit 1, 1;\
# 聚合查询: group by [having], having关键字对分类后的结果再进行过滤
select product_id, count(1) as order_count, sum(quantity) as total_quantity, sum(amount) as total_amount
  from product_order where quantity > 0 group by product_id having total_quantity > 2;

# 表连接查询: join
# 全连接: full join
select * from product full join product_order;
# 查询多表的全连接
select * from product, product_order;
# 子连接: in, not in, =, !=, exists, not exists
# in查询: 索引用在product_order, 与exists的区别在于索引作用的表不同
select * from product_order where product_id in (select id from product);
# exists条件查询: 当能返回结果集则为1, 不能返回结果集则为0; 索引用在product表
select * from product_order where exists (select * from product where product.id = product_order.product_id);
```

## DCL语句
``` bash
# 用户授权
grant select,insert on e_mall.* to 'test_user'@'%' identified by 'Dev!@#123' with grant option;
# 帮助使用: ? Xxx
# 查看所有的show命令
? show
```

## 数据类型
### 整数类型
|   整数类型  | 字节数  |
| :--------- | :---- |
|   TINYINT  | 	1   |
|  SMALLINT  |  	2   |
|  MEDIUMINT  | 	3    |
| INT, INTEGER | 	4 |
| BIGINT | 	8 |

|  浮点数类型  |  字节数 |
| :--------- | :---- |
|  FLOAT  | 	4 |
|  DOUBLE  | 	8 |

|  定点数类型	 |  字节数 |
| :--------- | :---- |
|  DEC(M,D)  | 	M+2 |
|  DECIMAL(M,D)  | 	M+2 |

|  位类型  | 	字节数 |
| :--------- | :---- |
|  BIT(M)  | 	1~8(M: 1~64)

### 字符串类型
|  字符串类型	  | 字节数  | 	描述 |
| :--------- | :---- | :---- |
|  CHAR(M)  | M  | 	M: 0~255, 固定长度 |
|  VARCHAR(M) | 		 | M: 0~65535, 可变长 |
|  TINYBLOB | 	 | 0~255 |
|  BLOB | 	 | 0~65535 |
|  MEDIUMBLOB	 | 	  | |
|  LONGBLOB | 	 | 	 |
|  TINYTEXT | 		 | 0~255 |
|  TEXT | 	 | 0~65535 |
|  MEDIUMTEXT	 |  | 	 |
|  LONGTEXT | 	 | 	 |
|  BINARY(M) | 	 | 	 |
|  VARBINARY	 |  | 	 |

### 日期类型
|  日期类型 | 	字节数 | 	描述 |
| :--------- | :---- | :---- | 
|  DATETIME | 	8 | 	YYYY-MM-DD HH:MM:SS |
|  DATE | 	4	 | YYYY-MM-DD |
|  TIME | 	3	 | HH:MM:SS |
|  TIMESTAMP | 	4	 | 时间戳 |