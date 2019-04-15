# 外键
如果一张表中有一个非主键的字段指向了别一张表中的主键，就将该字段叫做外键。一张表中可以有多个外键。

两个概念：外键所在的表称为子表（从表），外键所指向的表称为父表（主表）。

### 使用外键的前提：
1. 表储存引擎必须是 innodb，否则创建的外键无约束效果。

2. 外键的列类型必须与父表的主键类型完全一致。

3. 外键的名字不能重复。

4. 已经存在数据的字段被设为外键时，必须保证字段中的数据与父表的主键数据对应起来。

## 添加外键
语法：

``` sql
[CONSTRAINT 外键名] foreign key(外键字段名)　references 父表名(主键字段名) 
[ON DELETE {RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT}] 
[ON UPDATE {RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT}];
```

### 1、在创建时增加：

``` sql
-- 第一步：创建主表
CREATE TABLE grade (
  g_id int NOT NULL AUTO_INCREMENT,
  g_name varchar(10) DEFAULT NULL,
  PRIMARY KEY (g_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 第二步，建立从表并设置外键
-- 设置外键别名为 sgid
-- 外键 gradeNo 指向 表 grade 的 g_id 字段
CREATE TABLE students (
  s_id int NOT NULL AUTO_INCREMENT,
  s_name varchar(10) NOT NULL,
  s_age int DEFAULT NULL,
  s_sex int DEFAULT '0' COMMENT '0-男,1-女',
  gradeNo int DEFAULT NULL,
  PRIMARY KEY (s_id),
  FOREIGN KEY (gradeNo) REFERENCES grade (g_id)
  -- CONSTRAINT sgid FOREIGN KEY (gradeNo) REFERENCES grade (g_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

> 注意：CONSTRAINT 如果不设置的话，会自动生成。

### 2、在已创建的表中增加外键：

``` sql
ALTER TABLE 子表名 ADD FOREIGN KEY (外键字段名) REFERENCES 父表名 (主键字段名)

ALTER TABLE 子表名 ADD CONSTRAINT 外键名 FOREIGN KEY (外键字段名) REFERENCES 父表名 (主键字段名)
```

> 注意：在删除主表数据的时候，必须先删除子表中对应的数据，然后才可以删除主表的数据。

## 外键的约束模式：
* RESTRICT：严格模式(默认), 父表不能删除或更新一个被子表引用的记录。

* CASCADE：级联模式, 父表操作后，子表关联的数据也跟着一起操作。

* SET NULL：置空模式,前提外键字段允许为 NULL,  父表操作后，子表对应的字段被置空。

* SET DEFAULT：表示设置为默认值(restrict)。

* NO ACTION：无动作，如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作。

``` sql
# 增加外键约束，父表的数据就可以删除更新了
create table bt_tab1(
  id int primary key auto_increment,
  class int,
  foreign key(class)　references tb_tab2(id) on delete cascade on update cascade;
)charset utf8;
```

### 删除外键
``` sql
alter table 子表名 drop foreign key 外键字段名;
```