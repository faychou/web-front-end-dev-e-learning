# select
基本语法：

``` bash
select selection_list  # 选择列名称
from table_list  # 表名称 
where primary_constraint  # 满足什么条件 
group by grouping_columns # 怎样对结果分组 
having secondary_constraint  # 满足的第二条件 
order by sorting_columns # 怎样对结果排序 
limit count  # 结果限定
```

select 5种子句：

* where 条件查询

* group by 分组

* having 筛选

* order by 排序

* limit 限制结果条数

> 注意：所有使用的关键词必须精确地以上面的顺序给出。例如，`having` 必须跟在 `group by` 之后和 `order by` 之前。

## 普通查询
``` bash
# 查询 user 表中所有列的数据
select * from user;

# 查询 user 表里所有数据的 name 列和 age 列
select name,age from user;

# 表达式求值而不查询任何表
select (2+3*4.5)/2.5;
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

### 多表查询
``` bash
select t1.name as t1name,t1.age as t1age,t2.name as t2name,t2.age as t2age
 from test01_01 as t1,test01_03 as t2 where t1.name=t2.name;
```

从 t1 和 t2 中取出两张表 name 相同的值。as 表示为这个表取别名。

## 分组查询
group by 从句根据所给的列名返回分组的查询结果，可用于查询具有相同值的列。

``` bash
# 按 name 分组，并且统计数量
select name,count(*) from user group by name;
```

### 聚合函数
* max : 求最大

* min : 求最小

* sum : 求总和

* avg : 求平均

* count : 求总行数

``` sql
# 查出最贵的商品的价格
elect max(shop_price) from goods;

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

# 查询一共有多少种商品。
select count(*) from goods;

# 按 cat_id 分组并返回该组价格最贵的
select cat_id,max(shop_price) from goods group by cat_id;

# 按 cat_id 分组并返回该组价格最低的
select cat_id,min(shop_price) from goods group by cat_id;

# 按 cat_id 分组并返回该组平均价格
select cat_id,avg(shop_price) from goods group by cat_id; 

# 按 cat_id 分组并返回该组价格总和
select cat_id,sum(goods_number) from goods group by cat_id;

# 按 cat_id 分组并统计数量
select cat_id,count(*) from goods group by cat_id;
```

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

``` bash
# 按照 age 升序输出
select * from user order by age asc;
```

* asc 表示升序，为默认值;
* desc 为降序。

## 限制查询
