### 语法规则
* 在 window 系统中要区分大小写，但是在其他系统中不区分；

* MySQL 语句都以分号为结束；

* 数据库名或者表名只能包含字母、数字、下划线（_）、@、# 等；

# 数据库
## 创建数据库
``` bash
create database test;
```

这句话代表创建一个名叫 test 的数据库。

为了避免创建一个已经存在的数据库，我们可以添加以下字段：

``` bash
create database if not exists db_name;
```

还可以在创建数据库时通过 character set 为数据库指定字符编码：

``` bash
create database user character set utf8;

# or
create database user character set utf8mb4;
```

> 注意：utf-8 编码可能是 2个字节、3个字节、4个字节的字符，但是 MySQL 的 utf8 编码只支持3字节的数据，而移动端的表情数据是4个字节的字符。所以就引入了 utf8mb4 编码，该编码是 utf8 编码的超集，兼容 utf8，并且能存储4字节的表情字符。采用 utf8mb4 编码的好处是：存储与获取数据的时候，不用再考虑表情字符的编码与解码问题。utf8mb4 的最低 mysql 版本支持版本为 5.5.3+ 。

### 显示数据库
查询所有的数据库：

``` bash
show databases;
```

如果想要快速查找数据，但是只记得数据库名字的一部分，可以使用以下语法：

``` bash
show databases like 'te%';
```

代表查询 te 开头的数据库，如果关键字在中间则使用 `'%es%'` ； 如果关键字在最后，我们使用 `'%st'` 来查找。

### 切换数据库
一般情况，数据库是比较多的，如果想要操作某一个数据库，则需要切换到该数据库下：

``` bash
# db_name 是数据库的名字
use db_name;
```

也可以在登陆数据库时直接选择进入哪个数据库：

``` bash
mysql -D 所选择的数据库名 -h 主机名 -u 用户名 -p
```

### 删除数据库
当我们不需要该数据库的时候，最好的方式是删除掉，因为数据库是会占据空间的：

``` bash
drop database test;

# 或者使用  if exists 关键词，如果数据库存在则删除，不存在也不会报错
drop database if exists test;
```

## 数据表操作
### 创建数据库表
MySQL 中所有的数据操作都是针对表来操作的，所以先要创建一个数据库表：

``` bash
# 进入数据库
use test;

# 创建表
# 语法： create table 表名(列名1 数据类型 约束,列名2 数据类型 约束);
create table user(
  name nvarchar(40),
  age int(5)
);
```

括号里面是表中每一列的内容，name、age 为每列的名称, 后面跟的是数据类型，其后可以设置每一列的约束条件，如：内容是否为空、是否为主键等。

以创建一个 t_user 表为例，表中将存放 id、姓名(name)、性别(sex)、年龄(age)、联系电话(tel) 这些内容:

``` bash
create table t_user(
  u_id int not null auto_increment primary key,
  u_name char(8) not null default "jack",
  u_sex char(4) not null,
  u_age int(5) unsigned not null,
  u_tel char(13) null default "-"  
) engine=innodb auto_increment=1 default charset=utf8;
```

* not null 代表该列的值不能为空；
* auto_increment 代表该列的值自增，在每张表中仅能有一个这样的值且所在列必须为索引列；
* primary key 表示该列是表的主键, 本列的值必须唯一且不能为空, MySQL 将自动索引该列；
* unsigned 表示该类型为无符号型，也就是不能为负数,当你插入负数时就显示0；
* zerofill 自动填充，如 int(4),你插入1，显示0001，你插入6666，显示也是6666，并且会为该列自动地增加 UNSIGNED 属性；
* unique 唯一约束，用于保证数据表中字段的唯一性；
* default 表示如果插入新字段时，该列值为空时系统自动插入默认值。
* engine=innodb 使用innodb引擎，是 mysql 数据库引擎之一；
* auto_increment=1 自增键的起始序列号为1；
* charset=utf8 数据库默认编码为 utf8。

#### 通过 select 来创建表
``` bash
create table user_new select * from user_old;
```

代表从 user_old 的表中复制一份数据，添加到新的 user_new 数据库表中。

### 数据类型
MySQL 有三大类数据类型, 分别为数字、字符串、日期\时间。

#### 整数类型
|   整数类型  | 大小  |
| :--------- | :---- |
|   TINYINT  | 	1   |
|  SMALLINT  |  	2   |
|  MEDIUMINT  | 	3    |
| INT, INTEGER | 	4 |
| BIGINT | 	8 |

|  浮点数类型  |  大小 |  精度  |
| :--------- | :---- | :---- |
|  FLOAT  | 	4 | 7为小数 |
|  DOUBLE  | 	8 | 15为小数 |
|  DECIMAL(M,D)  | 17 | 30为小数 |

> DECIMAL(M,D) 中，M 指数字的总位数，包括整数位个数和小数位个数，D 指小数位的个数，所以 M 肯定是大于 D 的。

|  位类型  | 	字节数 |
| :--------- | :---- |
|  BIT(M)  | 	1~8(M: 1~64)

#### 字符串类型
|  字符串类型	  | 分类 | 字节数  | 	描述 |
| :--------- | :---- |:---- | :---- |
|  CHAR(M)  | 字符串 | 固定 M字节  | 	M: 0~255, 固定长度 |
|  VARCHAR(M) | 字符串 |	 最大 M字节 | M: 0~65535, 可变长 |
|  TINYBLOB | 二进制(图片、音乐等) | 0~255 |  |
|  BLOB | 二进制 | | 0~65535 |
|  MEDIUMBLOB	 |二进制 | 	  | |
|  LONGBLOB | 二进制 |	 | 	 |
|  TINYTEXT | 文本 |  | 0~255 |
|  TEXT |  文本(如存储文章详情) | 	 | 0~65535 |
|  MEDIUMTEXT	 | 文本 |  | 	 |
|  LONGTEXT | 长文本 | 	 | 	 |
|  BINARY(M) | 	 | 	 |
|  VARBINARY	 |  | 	 |

#### 日期类型
|  日期类型 | 	字节数 | 	描述 |
| :--------- | :---- | :---- | 
|  DATETIME | 	8 | 	YYYY-MM-DD HH:MM:SS |
|  DATE | 	4	 | YYYY-MM-DD |
|  TIME | 	3	 | HH:MM:SS |
|  TIMESTAMP | 	4	 | 时间戳 |

### 显示数据库表信息
查看某个数据库下的所有数据库表，或者根据like模糊查看某个数据库表：

``` bash
show tables from  test;
show tables from  test like "%s%";

# 也可以省略 from ，直接写
show tables;
```

查看某个数据库表中的列属性,like后面跟的是列的名称的某个关键字：

``` bash
show columns from user;
show columns from user from test like "%n%";
```

查看某个数据库表中的索引：

``` bash
show index from user from test;
```

查看数据库中全部表的状态或者某个表的状态，信息提供的更多：

``` bash
show table status from test;
show table status from test like "%s%";
```

另外一种查看数据库表信息的方式：

``` bash
desc user;
describe user;
```

### 修改表结构
#### 增加列
``` bash
# 在 user 表最后增加一列 address 
alter table user add address char(60);

# 在名为 age 的列后插入列 birthday
alter table students add birthday date after age;
```

#### 删除列
``` bash
alter table user drop address;
```

#### 修改列
``` bash
# 将 address 更名为 addr
alter table user change address addr int;

# 将 name 列的数据类型改为 char(16)
alter table user change name name char(16) not null;
```

#### 表更名
``` bash
alter table user rename users;
```

### 删除数据库表
``` bash
# 删除一个表
drop table user;

# 避免不存在的表导致错误
drop table if exists user;

# 删除多个表
drop table user,labels;
```

> 注意：`drop table` 删除一个或多个数据库表，所有表中的数据和表定义均被删除。

## 添加数据
``` bash
insert into 表名 values(值1,值2,...);
# or
insert 表名 (字段1,字段2,...) values(值1,值2,...);
```

使用 into 关键字，此时 values 里面的数据必须一一对应表中所有的字段，也就是说,表中有几个字段,那么 value s里面就必须有几个值；

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

## 查询数据
``` bash
select selection_list  # 选择列名称
from table_list  # 表名称 
where primary_constraint  # 满足什么条件 
group by grouping_columns # 怎样对结果分组 
having secondary_constraint  # 满足的第二条件 
order by sorting_columns # 怎样对结果排序 
limit count  # 结果限定
```

> 注意：所有使用的关键词必须精确地以上面的顺序给出。例如，`having` 必须跟在 `group by` 之后和 `order by` 之前。

### 普通查询
``` bash
# 查询 user 表中所有列的数据
select * from user;

# 查询 user 表里所有数据的 name 列和 age 列
select name,age from user;

# 表达式求值而不查询任何表
select (2+3*4.5)/2.5;
```

### 条件查询
使用 where 关键字来对查询的结果进行筛选：

``` bash
# 筛选出当前表中性别为女的信息
select * from user where sex ="女";

# 查询年龄在 21 岁以上的所有信息
select * from user where age > 21;

# 查询名字中带有 "王" 字的所有信息
select * from user where name like "%王%";

# 查询 id 小于 5 且年龄大于 20 的所有信息
select * from user where id<5 and age>20;
```

### 查询排序
使用 order by 子句对查询返回的结果按一列或多列排序。

``` bash
# 按照 age 升序输出
select * from user order by age asc;
```

* asc 表示升序，为默认值;
* desc 为降序。

### 分组和行计数
group by 从句根据所给的列名返回分组的查询结果，可用于查询具有相同值的列。

``` bash
# 按 name 分组，并且统计数量
select name,count(*) from user group by name;
```

### 多表查询
``` bash
select t1.name as t1name,t1.age as t1age,t2.name as t2name,t2.age as t2age
 from test01_01 as t1,test01_03 as t2 where t1.name=t2.name;
```

从 t1 和 t2 中取出两张表 name 相同的值。as 表示为这个表取别名。

## 更新数据
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

## 删除数据
``` bash
# 将 user 中名为 jack 的数据删除
delete from user where name= 'jack';
```

where 子句指定哪些记录应该删除。它是可选的，如果不指定的话，将会删除所有的记录。

``` bash
delete from students;
```

## 数据库连接池
数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个。这项技术能明显提高对数据库操作的性能。

## 外键
如果一张表中有一个非主键的字段指向了别一张表中的主键，就将该字段叫做外键。一张表中可以有多个外键。

两个概念：外键所在的表称为子表，外键所指向的表称为父表。

### 外键的约束模式：
* RESTRICT：严格模式(默认), 父表不能删除或更新一个被子表引用的记录。

* CASCADE：级联模式, 父表操作后，子表关联的数据也跟着一起操作。

* SET NULL：置空模式,前提外键字段允许为 NULL,  父表操作后，子表对应的字段被置空。

* SET DEFAULT：设默认值。

* NO ACTION：无动作，如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作。

### 使用外键的前提：
1. 表储存引擎必须是 innodb，否则创建的外键无约束效果。

2. 外键的列类型必须与父表的主键类型完全一致。

3. 外键的名字不能重复。

4. 已经存在数据的字段被设为外键时，必须保证字段中的数据与父表的主键数据对应起来。

### 添加外键
1、在创建时增加：

语法：

```
[CONSTRAINT 外键名] foreign key(外键字段名)　references 父表名(主键字段名) [ON DELETE {RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT}] [ON UPDATE {RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT}];
```

CONSTRAINT 如果不设置的话，会自动生成。

如：

``` bash
# 为 class 列添加名为 fk 的外键，且此外键的参照为 tb_tab2 表的 id 列
create table bt_tab1(
  id int primary key auto_increment,
  class int,
  constraint `fk` foreign key(class)　references tb_tab2(id);
)charset utf8;

# 增加外键约束，父表的数据就可以删除更新了
create table bt_tab1(
  id int primary key auto_increment,
  class int,
  foreign key(class)　references tb_tab2(id) on delete cascade on update cascade;
)charset utf8;
```

2、在创建好的表中增加：

```
ALTER TABLE 子表名 ADD FOREIGN KEY (外键字段名) REFERENCES 父表名 (主键字段名)

ALTER TABLE 子表名 ADD CONSTRAINT ID_FK FOREIGN KEY (外键字段名) REFERENCES 父表名 (主键字段名)
```

### 删除外键
```
alter table 子表名 drop foreign key 外键字段名;
```
