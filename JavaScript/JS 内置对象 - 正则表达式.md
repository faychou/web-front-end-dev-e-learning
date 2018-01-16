# 正则表达式

## 定义
一个正则有两部分组成：正则主体和修饰符。

``` js
//RegExp构造函数
const pattern=/\d/g;

//或 字面量形式
const pattern = new RegExp('\d','g')
```

### 修饰符
* g : 所有匹配的情况，如果没有它，只匹配第一个
* i : 忽略字符的大小写
* m : 支持多行
* u : 支持 Unicode
* y : 严格模式（返回指定位置后的匹配结果）

### 转义符号
某些符号代表一些特殊含义，如果要使用这些符号就必须要使用反斜杠来转义 `\` 这些字符。

## 匹配规则
* ^ : 它定义正则运算的前边界，如果出现在集合（[]）中表示取反
* $ : 它定义了正则运算的后边界
* \b : 匹配一个字符的边界（也即是字符和空白字符的分界）
* \d : 代表数字
* \D ：非数字
* \w : 代表单词
* \W ：非单词
* \s : 代表空白符，
* \D ：非空白字符
* . : 任意字符
* + : 匹配该符号前面的表达式一次或更多次
* * ：匹配该符号前面的表达式零次或多次
* ？ : 匹配该符号前面的表达式零次或一次
* {n} ：就是匹配n次
* {n,} ：就是匹配至少n次
* {n,m} ：就是匹配n到m之间的任意次数


## 分组和捕获
在正则中，`()` 有两种含义，一是用来分组，一是用来捕获想要的值。

### 分组
圆括号结合 * ？ + {} 使用时，是把圆括号内看作一个整体。

``` js
/(ab)+/  //匹配一个或多个ab

/(ab)+|(cd)+/   //匹配一个或多个 ab或cd
```

### 捕获

## 正则方法
### regexp.test(str)
返回值为true/false。

``` js
let str = "Hello world!";
let regexp = /hello/i;
console.log(regexp.test(str)); //true
```

### regexp.exec(str)
返回匹配到的字符，没有匹配返回 null。

## String可以使用正则的方法
### str.search(str | regexp)
匹配结果，返回第一个匹配结果的首字符位置；否则，返回 `-1`。

``` js
let str = "Hello world!";
regexp = /o/i;
str.search(regexp); // 4
```

> 注；search 只能返回第一次匹配的结果，而不能返回其他匹配结果。

### str.match(str | regexp)
和 regexp.exec() 返回结果一样，如果匹配到返回匹配的字符，否则返回 null。

### str.split(substr | regexp, limit)
将给定的字符串按单词为单位进行分割，返回一个由单词组成的数组。

``` js
let str = 'Hello world, my   name  is lzb.'
let regexp = /\s+/i
str.split(regexp); // ["Hello", "world,", "my", "name", "is", "lzb."]
str.split(regexp, 3) //  ["Hello", "world,", "my"]
```

第二参数限制返回结果数组的长度。

### str.replace(str | regexp, str | func)

## 可视化的正则编辑器
[Regulex](https://jex.im/regulex/#!flags=&re=%5E(a%7Cb)*%3F%24)