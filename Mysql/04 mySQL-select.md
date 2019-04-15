# select
基本语法：

``` sql
select selection_list  # 选择列名称
  from table_list  # 表名称 
  where primary_constraint  # 满足什么条件 
  group by grouping_columns # 怎样对结果分组 
  having secondary_constraint  # 满足的第二条件 
  order by sorting_columns # 怎样对结果排序 
  limit count;  # 结果限定
```

select 5种查询条件子句：

* where 条件查询

* group by 分组

* having 筛选

* order by 排序

* limit 限制结果条数

> 注意：所有使用的关键词必须精确地以上面的顺序给出。例如，`having` 必须跟在 `group by` 之后和 `order by` 之前。

## 普通查询
``` sql
# 查询 user 表中所有列的数据
select * from user;

# 查询 user 表里所有 name 列
select name from user;

# 查询 user 表里所有 name 列和 age 列(多个列名之间使用逗号隔开)
select name,age from user;

# 表达式求值而不查询任何表
select (2+3*4.5)/2.5;

# 如果检索数据时候不想要出现重复的结果，可以使用 distinct 关键字
select distinct name from user;

# 给查询的列取别名：SELECT 列1 别名1, 列2 别名2 FROM ...
SELECT id, tel telphone, name FROM students;
```

## 条件查询
使用 where 关键字来对查询的结果进行筛选：

|  条件  |   说明  |
| :----- | :---- |
| <	                  | 小于        |
| >	                  | 大于        |
| !=或<>              | 	 不等于  |
| <=	               | 小于等于    |
| >=	               | 大于等于    |
| in	               | 在某集合内  |
| between....and..... | 在某范围内  |
| not或!	            | 逻辑非     |
| or 或 `||`	         | 逻辑或     |
| and 或 &&	         | 逻辑与     |
| like                | 模糊查询,`%` 通配任意字符;`_` 通配单个字符 |

``` sql
# 筛选出当前表中性别为女的信息
select * from user where sex ="女";

# 查询年龄在 21 岁以上的所有信息
select * from user where age > 21;

# 查询第4个分类或第5个分类的商品
select goods_id,cat_id,goods_name from goods where cat_id in(4,5);

# 查询价格在200和300之间的商品
select goods_name,price from goods where price between 200 and 300;

# 查询价格在 300-500 之间的商品，或者 800-1000 之间的商品
select goods_name,price from goods where price between 300 and 500 or price between 800 and 1000;

# 取出不属于第4,5个分类的商品
select goods_id,cat_id,goods_name from goods where cat_id not in(4,5);

# 查询 id 小于 5 且年龄大于 20 的所有信息
select * from user where id<5 and age>20;

# 查询名字中带有 "王" 字的所有信息
select * from user where name like "%王%";

# 查询名字中 "王" 字开头的所有信息
select * from user where name like "王%";

# 查询名字是 "王XX" 的所有信息
select * from user where name like "王__";
```

要组合三个或者更多的条件，就需要用小括号()表示如何进行条件运算：

``` sql
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

如果不加括号，条件运算按照 NOT、AND、OR 的优先级进行，即 NOT 优先级最高，其次是 AND，最后是 OR。

### 多表查询
SELECT查询不但可以从一张表查询数据，还可以从多张表同时查询数据。

``` sql
SELECT * FROM students, classes;
```

这种一次查询两个表的数据，查询的结果也是一个二维表，它是students表和classes表的“乘积”，即students表的每一行与classes表的每一行都两两拼在一起返回。结果集的列数是students表和classes表的列数之和，行数是students表和classes表的行数之积。又称笛卡尔查询

设置别名:

``` sql
select t1.name as t1name,t1.age as t1age,t2.name as t2name,t2.age as t2age
 from test01_01 as t1,test01_03 as t2 where t1.name=t2.name;
```

从 t1 和 t2 中取出两张表 name 相同的值。as 表示为这个表取别名。

## 分组查询
group by 从句根据所给的列名返回分组的查询结果，可用于查询具有相同值的列。

``` sql
# 按 name 分组，并且统计数量
select name,count(*) from user group by name;
```

### 聚合函数
* max : 计算某一列的最大值；

* min : 计算某一列的最小值；

* sum : 计算某一列的合计值，该列必须为数值类型；

* avg : 计算某一列的平均值，该列必须为数值类型；

* count : 求总行数。

``` sql
# 查出最贵的商品的价格
select max(shop_price) from goods;

# 查出最大（最新）的商品
select max(goods_id) from goods;

# 查出最便宜的商品价格
select min(shop_price) from goods;

# 查出最旧(最小)的商品
select min(goods_id) from goods;

# 查询所有商品的库存总量
select sum(goods_number) from goods; 

# 查询所有商品的平均价格
select avg(shop_price) from goods;

# 查询所有手机的平均价格
select avg(shop_price) from goods where goods_type = "mobile";

# 查询一共有多少种商品。
select count(*) from goods;

# 查询男生有多少人数
SELECT COUNT(*) FROM students WHERE gender = 'M';

# 按 cat_id 分组并返回该组价格最贵的，注意返回的 cat_id 必须是分组名，因为只有这个是一样的
select cat_id,max(shop_price) from goods group by cat_id;

# 按 cat_id 分组并返回该组价格最低的
select cat_id,min(shop_price) from goods group by cat_id;

# 按 cat_id 分组并返回该组平均价格
select cat_id,avg(shop_price) from goods group by cat_id; 

# 按 cat_id 分组并返回该组价格总和
select cat_id,sum(goods_number) from goods group by cat_id;

# 按 cat_id 分组并统计每个 cat_id 分组下的数量
select cat_id,count(*) from goods group by cat_id;
```

给聚合函数设置别名：

``` sql
SELECT COUNT(*) num FROM students;
```

> 注意：COUNT(*)和COUNT(id)实际上是一样的效果。

## 筛选查询
having 与 where 异同点:

* having 与 where 类似，可筛选数据，where 后的表达式怎么写，having 就怎么写。
* where 针对表中的列发挥作用，查询数据。
* having 针对查询结果中的列发挥作用，筛选数据。
* where 对表起作用，having 是对结果进行筛选。

``` sql
# 查询挂科两门及两门以上不及格同学的平均分
select name,sum(score < 60) as gk,avg(score) as pj from student group by name having gk >= 2;
```

## 排序查询
使用 order by 子句对查询返回的结果按一列或多列排序。

``` sql
# 按照 age 升序输出
select * from user order by age;

# 按照 age 降序输出
select * from user order by age desc;

# 在相同的 name 时候才 age 字段进行排序
select * from user order by name desc, age;
```

* asc 表示升序，为默认值;
* desc 为降序。

## 限制查询
使用 limit 关键字限制查询的数量。limit 后面可以跟两个值, 第一个为起始位置, 第二个是要查询的长度。

``` sql
# 限制查询5条记录
select * from user limit 5;

# 查询第5条后向后的5条记录
select * from user limit 5, 5;

# 另一种写法，注意 SQL 记录集的索引从0开始。
select * from user limit 5 OFFSET 5;
```
