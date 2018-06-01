# MySQL
MySQL 是一个真正的多用户、多线程 SQL 数据库服务器，是一个为关系型数据库。

MySQL 具有运行速度很快、容易使用、支持查询语言、多个用户可同时连接客户机和服务器、可以运行在各种版本的 UNIX 以及非 UNIX 的系统上等优点。

## 环境搭建
### Linux 系统配置

### MacOS 系统配置

### Windows 系统配置
#### 配置：
1. 直接官网下载 mysql.zip [下载地址](https://dev.mysql.com/downloads/mysql/)；

2. 将下载的 mysql.zip 解压至需要安装的位置, 如: C:\Program Files;

3. 在安装文件夹下找到 my-small.ini 配置文件, 将其重命名为 my.ini , 打开进行编辑, 在 `[client]` 与 `[mysqld]` 下均添加一行: `default-character-set = gbk`；

4. 打开 Windows 环境变量设置, 新建变量名 `MYSQL_HOME` , 变量值为 `MySQL` 安装目录路径, 如： `C:\Program Files\mysql-win64`；

5. 在 环境变量 的 Path 变量中添加 `;%MYSQL_HOME%\bin;`；

6. 安装 MySQL 服务, 打开 Windows 命令提示符, 执行命令: `mysqld --install MySQL --defaults-file="my.ini"` 提示 "Service successfully installed." 则表示安装成功。

### MySQL服务的启动、停止与卸载
``` bash
# 启动
net start MySQL

# 停止
net stop MySQL

# 卸载
sc delete MySQL
```

### 登录 MySQL
当 MySQL 服务已经运行时, 我们可以通过以下命令登录到 MySQL 数据库中, 首先打开终端, 输入以下命名:

``` bash
mysql -h 主机名 -u 用户名 -p
```

* -h : 该命令用于指定客户端所要登录的MySQL主机名, 登录当前机器该参数可以省略;
* -u : 所要登录的用户名;
* -p : 告诉服务器将会使用一个密码来登录, 如果所要登录的用户名密码为空, 可以忽略此选项。

然后，会出现输入密码的提示，若密码存在, 输入密码并回车登录, 不存在则直接按回车登录。登录成功后你将会看到 Welecome to the MySQL monitor... 的提示语。

最后，输入 exit 或 quit 退出登录。

### 修改用户密码
打开终端, 执行命令: 

``` bash
mysqladmin -u root -p password 新密码
```

执行后提示输入旧密码完成密码修改, 当旧密码为空时直接按回车键确认即可。

## 语法规则
在 window 系统中要区分大小写，但是在其他系统中不区分；

MySQL 语句都以分号为结束；

## 数据类型
MySQL 有三大类数据类型, 分别为数字、字符串、日期\时间。

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
|  字符串类型	  | 分类 | 字节数  | 	描述 |
| :--------- | :---- |:---- | :---- |
|  CHAR(M)  | 字符串 | M  | 	M: 0~255, 固定长度 |
|  VARCHAR(M) | 字符串 |		 | M: 0~65535, 可变长 |
|  TINYBLOB | 二进制(图片、音乐等) | 0~255 |  |
|  BLOB | 二进制 | | 0~65535 |
|  MEDIUMBLOB	 |二进制 | 	  | |
|  LONGBLOB | 二进制 |	 | 	 |
|  TINYTEXT | 文本 |  | 0~255 |
|  TEXT |  文本 | 	 | 0~65535 |
|  MEDIUMTEXT	 | 文本|  | 	 |
|  LONGTEXT |文本| 	 | 	 |
|  BINARY(M) | 	 | 	 |
|  VARBINARY	 |  | 	 |

### 日期类型
|  日期类型 | 	字节数 | 	描述 |
| :--------- | :---- | :---- | 
|  DATETIME | 	8 | 	YYYY-MM-DD HH:MM:SS |
|  DATE | 	4	 | YYYY-MM-DD |
|  TIME | 	3	 | HH:MM:SS |
|  TIMESTAMP | 	4	 | 时间戳 |


## 数据库操作
### 创建数据库
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
create database user character set utf-8;
```

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
create table user(
  name nvarchar(40),
  age int(5)
);
```

`create table` 是固定的关键字，后面紧跟要创建的表的名称，括号里面是表中每一列的内容，每一列都可以设置内容是否为空、是否为主键等，比如说 name、age 为每列的名称, 后面跟的是数据类型描述。

以创建一个 user 表为例，表中将存放 id、姓名(name)、性别(sex)、年龄(age)、联系电话(tel) 这些内容:

``` bash
create table user(
  id int not null auto_increment primary key,
  name char(8) not null default "jack",
  sex char(4) not null,
  age int(5) unsigned not null,
  tel char(13) null default "-"  
);
```

* not null 代表该列的值不能为空；
* auto_increment 代表该列的值自增，在每张表中仅能有一个这样的值且所在列必须为索引列；
* primary key 表示该列是表的主键, 本列的值必须唯一, MySQL 将自动索引该列；
* unsigned 表示该类型为无符号型；
* default 表示该列值为空时的默认值。

#### 通过 select 来创建表
``` bash
create table user_new select * from user_old;
```

代表从 user_old 的表中复制一份数据，添加到新的 user_new 数据库表中。

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
### 添加单条数据
``` bash
# 向 user 表中添加一条数据
insert into user values('faychou',18);
```

* into 是可选；
* values 必须包含表中每列的值，并且按表中列的存放次序给出。

如果设置了主键并且自动增长，添加的时候需要把对应的主键值设置为 null 或者数字 “0”：

``` bash
insert user values(null,'jack',23);
```

有时我们只需要插入部分数据, 或者不按照列的顺序进行插入, 可以使用这样的形式进行插入:

``` bash
insert user (name, sex, age) values("lily", "女", 21);
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


