# 异步
需求：每隔 1s 输出一个数字。

``` js
//原始代码
for (var i = 1; i <= 5; i++) {
  setTimeout( function timer() {
    console.log( i );
  }, i * 1000 );
}
// 6, 6, 6, 6, 6
```

当定时器运行时，即使每个迭代中执行的是 setTimeout(.., 0)，所有的回调函数依然是在循环结束后才会被执行，因此会每次输出一个 6 出来。

``` js
// 改进
for (var i = 1; i <= 5; i++) {
  (function() {
    var j = i;
    setTimeout( function timer() {
      console.log( j );
    }, j * 1000 );
  })();
}

//或者
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log( j );
    }, j * 1000 );
  })(i);
}
```

迭代内使用 IIFE 会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确值的变量供我们访问。