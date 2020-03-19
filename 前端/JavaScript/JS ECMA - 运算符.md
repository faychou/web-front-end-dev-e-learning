# JavaScript - 运算符

## 算数运算符

## 条件运算符

## 逻辑运算符

## 三元运算符

## 短路写法
在JS函数中我们经常会使用到短路运算符，主要是逻辑与（&&） 和 逻辑或（||）。

### 逻辑与 &&
1、如果逻辑与运算符左边的值布尔转换后为 true，那么返回右边的值：

``` js
var a = 1 && 2;
console.log(a); // 2
```

2、如果逻辑与运算符左边的值布尔转换后为 true，那么返回右边的值：

``` js
var a = false && 1;
console.log(a); // false
```

3、如果逻辑与运算符左边的值布尔转换后为 false，那么返回左边的值，但是当逻辑与的左边为 null / NaN / undefined ，结果就会得到 null / NaN / undefined。

### 逻辑或 ||
1、如果逻辑或运算符左边的值布尔转换后为 false，那么返回右边的值：

``` js
var a = false || 1;
console.log(a); // 1
```


2、如果逻辑或运算符左边的值布尔转换后为 true，那么返回左边的值，如果两个操作数都是是 null/ NaN / undefined，返回 null / NaN / undefined：

``` js
var a = true || 1;
console.log(a); // 1
```
