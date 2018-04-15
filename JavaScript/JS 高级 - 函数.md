# 函数高级
## 递归函数

## IIFE

## 闭包
### 应用一
``` js
var lis = document.getElementsByTagName("li");

for(var i = 0 ; i < lis.length; i++) {
  lis[i].onclick = function() {
    console.log(i);
  };
}
```

当点击 li 标签时，我们会发现每次输出的 i 值是一样的，因为在用户点击的 li 的时候,这个 for 循环早已结束,i 与 for 形成了一个闭包，因为 onclick 函数长期有效，导致 for 循环并没有被回收，但 for 循环已结束，所以 i 一直是 lis的长度值。

解决方案：

``` js
var lis = document.getElementsByTagName("li");

for(var i = 0 ; i < lis.length; i++) {
  lis[i].onclick = (function(i) {
    return function() {
      console.log(i);
    };
  })(i);
}
```

通过一个直接执行的函数把参数传给匿名函数。

## 柯里化
参数够了就执行，参数不够就返回一个函数，之前的参数存起来，直到够了为止。

``` js
function curry(func) {
  var l = func.length;
  return function curried() {
    var args = [].slice.call(arguments);
    if(args.length < l) {
      return function() {
        var argsInner = [].slice.call(arguments)
        return curried.apply(this, args.concat(argsInner))
      }
    } else {
      return func.apply(this, args)
    }
  }
}

var f = function(a,b,c) {
  return console.log([a,b,c])
}
var curried = curry(f);
curried(1)(2)(3)
```
