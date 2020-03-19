# 索引
创建了一个名称为 idx_score，使用列 score 的索引。索引名称是任意的，索引如果有多列，可以在括号里依次写上：

``` sql
ALTER TABLE students ADD INDEX idx_score (score);

# 多列索引
ALTER TABLE students ADD INDEX idx_name_score (name, score);
```

### 唯一索引
``` sql
ALTER TABLE students ADD UNIQUE INDEX uni_name (name);
```

通过UNIQUE关键字我们就添加了一个唯一索引。

### 全文索引
``` sql
ALTER TABLE students ADD FULLTEXT(name);
```

### 主键索引
``` sql
ALTER TABLE students ADD PRIMARY KEY(id);
```

