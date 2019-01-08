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

## 惰性载入函数
惰性载入函数是指在执行函数前需要做出判断，但是每次执行还要进行 if 判断，这就造成了不必要的浪费。所以解决方法是只判断一次，后续所有函数直接执行不再判断。实现方式有以下两种：

### 在声明函数时就指定适当的函数
``` js
var foo = (function foo() {
  if(a != b) {
    return function() {
      console.log('aaa');
    }
  } else {
    return function() {
      console.log('bbb');
    }
  }
})();
```

### 在函数被调用时再处理函数
``` js
function foo() {
  if(a != b) {
    foo = function() {
      console.log('aaa');
    };
  } else {
    foo = function() {
      console.log('bbb');
    }
  };
  return foo();
}
```

比如说，在写 js 的事件监听时，每次都要判断当前浏览器，然后再根据不同的浏览器选择不同的添加事件方式，这就浪费了性能。因为当打开浏览器后，浏览器就已经确定了。

## 柯里化
参数够了就执行，参数不够就返回一个函数，之前的参数存起来，直到够了为止。

柯里化就是，把原来的函数 adder(3,5,6,7,8,9) ==> adder(3)(5)(6)(7)(8)(9);

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
