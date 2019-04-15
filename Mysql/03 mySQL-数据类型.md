# 数据类型
MySQL 有三大类数据类型, 分别为数字、字符串、日期\时间。

## 数字类型
### 整数
|   整数类型  | 大小(字节)  | 最小值 | 最大值 |
| :--------- | :---- | :---- | :---- |
|   TINYINT  | 	1   | -128 | 127 |
|  SMALLINT  |  	2   | -32768 | 32767 |
|  MEDIUMINT  | 	3    | -8388608 | 8388607 |
| INT, INTEGER | 	4 | -2147483648 | 2147483647 |
| BIGINT | 	8 |  -9223372036854775808  | 9223372036854775807 |

### 浮点数
|  浮点数类型  |  大小 |  精度  |
| :--------- | :---- | :---- |
|  FLOAT(M,D)  | 	4 | 7为小数 |
|  DOUBLE(M,D)  | 	8 | 15为小数 |
|  DECIMAL(M,D)  | 17 | 30为小数 |

> DECIMAL(M,D) 中，M 指数字的总位数，包括整数位个数和小数位个数，D 指小数位的个数，所以 M 肯定是大于 D 的。

|  位类型  | 	字节数 |
| :--------- | :---- |
|  BIT(M)  | 	1~8(M: 1~64)

## 字符串类型
### 字符串
|  字符串类型	  | 字节数  | 	描述 |
| :--------- | :---- | :---- |
|  CHAR(M)  | 固定 M字节  | 	M: 0~255, 固定长度 |
|  VARCHAR(M) | 最大 M字节 | M: 0~65535, 可变长 |
|  BINARY(M) |  M	 | 允许长度0-M个字节的定长字节符串 |
|  VARBINARY(M)	 | 允许长度0-M个字节的定长字节符串 | 值的长度+1个字节 |

> CHAR 和 VARCHAR：

1. char(n) 若存入字符数小于 n，则以空格补于其后，查询之时再将空格去掉。所以 char 类型存储的字符串末尾不能有空格，varchar 不限于此。 

2. char(n) 固定长度，char(4) 不管是存入几个字符，都将占用4个字节，varchar 是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，所以 varchar(4),存入3个字符将占用4个字节。 

3. char 类型的字符串检索速度要比 varchar 类型的快。

### 文本
|  字符串类型	  | 字节数  | 	描述 |
| :--------- | :---- | :---- |
|  TINYTEXT |   | 0~255,可变长度 |
|  TEXT |   	 | 0~65535,可变长度 |
|  MEDIUMTEXT	 |   | 可变长度，最多2的24次方-1个字符 |
|  LONGTEXT | 	 | 可变长度，最多2的32次方-1个字符	 |

> varchar 和 text 的区别： 

1. varchar可指定n，text不能指定，内部存储varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，text是实际字符数+2个字节。 

2. text类型不能有默认值。 

3. varchar可直接创建索引，text创建索引要指定前多少个字符。varchar查询速度快于text,在都创建索引的情况下，text的索引似乎不起作用。

### 二进制(可用来存储图片、音乐等)
|  字符串类型	  | 分类 | 字节数  | 	描述 |
| :--------- | :---- |:---- | :---- |
|  TINYBLOB | 二进制(图片、音乐等) | 0~255 |  |
|  BLOB | 二进制 | | 0~65535 |
|  MEDIUMBLOB	 |二进制 | 	  | |
|  LONGBLOB | 二进制 |	 | 	 |

## 日期类型
|  日期类型 | 	字节数 | 	描述 |
| :--------- | :---- | :---- | 
|  DATETIME | 	8 | 	YYYY-MM-DD HH:MM:SS |
|  DATE | 	4	 | YYYY-MM-DD |
|  TIME | 	3	 | HH:MM:SS |
|  TIMESTAMP | 	4	 | 时间戳 |
|  YEAR | 	1	 | 年份 |

``` sql
-- 设置一个具体的默认时间
TIMESTAMP DEFAULT 'yyyy-mm-dd hh:mm:ss'
```

在创建时间字段的时候可以设置成自动添加时间：

DEFAULT CURRENT_TIMESTAMP：表示当插入数据的时候，该字段默认值为当前时间

ON UPDATE CURRENT_TIMESTAMP：表示每次更新这条数据的时候，该字段都会更新成当前时间

``` sql
CREATE TABLE user (
  username varchar(255) DEFAULT '' COMMENT '内容',
  create_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  update_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
``
