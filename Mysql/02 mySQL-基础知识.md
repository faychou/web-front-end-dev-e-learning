### 基本概念
* 数据库: 一个保存着有组织的数据的容器。

* 数据库软件: DBMS, 数据库是通过DBMS来操作的容器。

* 表: 某中特定数据类型的结构化清单

* 模式(schema): 关于数据库和表的布局以及特性的相关信息

* 列(column): 表由列组成, 列中保存着表中某部分信息, 每一列都有相应的数据类型

* 数据类型: 所容许的数据类型, 它限制该列所存储的数据结构

* 行(row/record): 表中的一行记录

* 主键(primary key): 唯一标识表中每行的这个列(这组列), 每个表都应该拥有一个主键(不能为空)

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

查看数据库中的表: 

``` bash
show tables;
```

查看表中的列: 

``` bash
show columns from tablesName;
```

其他：

``` bash
show status; #查看服务器状态信息。
show errors; #显示服务器错误信息
show warnings; #显示服务器警告信息
show grants; #显示授予用户的安全权限
show create table tableName; #查看创建表的sql语句
show create database dbName; #查看创建数据库的语句
```

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

