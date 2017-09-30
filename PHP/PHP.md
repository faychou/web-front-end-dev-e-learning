# PHP
## 环境搭建
### 集成环境 phpstudy

## 语法
### 基本
``` php
<?php
phpinfo();
?>
```

### 输出
``` php
<?php
echo 2323;
?>
```

### 变量
### 规则
* 变量以$开头
* 对大小写敏感
* 变量名只能包含字母数字下划线

### print_r()
``` php
<?php
$a = 'a';
$b = array(1,2);
print_r($a);
print_r($b);
?>
```

### var_dump()
主要用于调试