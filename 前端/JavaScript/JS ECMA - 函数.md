# JavaScript - 函数
函数在 JavaScript 的世界里是作为第一等公民。

### 函数的返回值
``` js
function add(a, b) {
  return a + b
}
```

### 参数
``` js
//普通传参
function add(a, b) {
  reutrn a + b;
}

//省略参数，参数默认值
function sub(a, b) {
  a = a || 0;
  b = b || 0;
  return a - b;
}

// 对象参数（推荐）
var option = {
  width: 10,
  height: 10
}

function area(opt) {
  this.width = opt.width || 1;
  this.height = opt.height || 1;
  return this.width * this.height;
}
```

## arguments 对象

## 函数的分类
### 普通函数
有函数名，参数，返回值等。

``` js
function add(a, b) {
  console.log(a + b);
}
```

### 匿名函数
没有函数名，可以把函数赋值给变量和函数，或者作为回调函数使用。非常特殊的就是立即执行函数。

``` js
(function() {
  console.log(1);
})();
```