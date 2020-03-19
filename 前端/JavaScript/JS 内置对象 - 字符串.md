# 字符串

## 方法
### split()
使用一个指定的分隔符把一个字符串分割存储到数组。

``` js
str=”jpg|bmp|gif|ico|png”;
arr=str.split(”|”);
//arr是一个包含字符值”jpg”、”bmp”、”gif”、”ico”和”png”的数组
```

### join()
使用您选择的分隔符将一个数组合并为一个字符串。

``` js
var myList=[”jpg”,”bmp”,”gif”,”ico”,”png”]
var portableList=myList.join(”|”);
//结果是jpg|bmp|gif|ico|png
```

### substring()
字符串截取。

### indexOf()
返回字符串中匹配子串的第一个字符的下标。

``` js
var myString=”JavaScript”;
var w=myString.indexOf(”v”);   //2
var x=myString.indexOf(”S”);    //4
var y=myString.indexOf(”Script”);    // 4
var z=myString.indexOf(”key”);    // -1(不存在)
```

## 应用
### 统计一个字符串中某段子串出现的次数
``` js
var temp = "This is a string.";
var count = temp.match(/is/g).length;
```
