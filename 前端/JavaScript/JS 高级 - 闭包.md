# 闭包

闭包是作用域的体现，一种能够读取其他函数内部变量的函数。

在本质上，闭包是将函数内部和函数外部连接起来的桥梁。

### 写法

``` js
// 第一种写法：直接将内部函数暴露给全局
(function() {
  var i=0;
  window.aaa = function() {
    return ++i;
  };
})();

// 第二种写法： 将函数执行的结果返回
var aaa = (function() {
  var i = 0;
  return function() {
     return ++i;
  }
})();
```



### 作用

由于局部变量被内层函数引用，导致该变量在函数执行结束后不会销毁，会常驻内存；

一个是可以读取函数内部的变量；

另一个就是让这些变量的值始终保持在内存中，避免使用全局变量。

### 缺点

由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

### 什么情况会引起内存泄漏？
全局变量(全局变量，不会被回收)；
闭包引起的内存泄漏；
没有清理的 DOM 元素引用；
被遗忘的定时器或者回调；
子元素存在引用引起的内存泄漏。

### 应用1：给多个标签设置事件
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

### 应用2：每隔一秒输出一个递增的数字

``` js
for(let i = 1; i <= 5; i++) { 
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
```

每一次for循环的时候，settimeout都执行一次，但是里面的函数没有被执行，而是被放到了任务队列里面，等待执行，for循环了4次，就放了4次，当主线程执行完成后，才进入任务队列里面执行。

``` js
// 正解1
// let 为代码块的作用域，所以每一次 for 循环，console.log(i)中 i 的引用都会得到保持，所以 for 循环结束后，这些作用域在 setTimeout 未执行前都不会被释放。
for(let i = 1; i <= 5; i++) { 
  setTimeout(function() {
    console.log(i);
  }, 1000 * i);
}
// 正解2
for(var i = 1; i <= 5; i++) {	
  (function(i) {
    setTimeout(function() {
      console.log(i);
    }, 1000 * i);
  })(i);
}
// 正解3
function foo(i) {
  setTimeout(function() {
    console.log(i);
    foo(++i)
  }, 1000);
}
foo(1)
```



### 垃圾回收
    8.1 概念：
      是当变量进入环境时，将这个变量标记为“进入环境”。当变量离开环境时，则将其标记为“离开环境”。销毁那些带“离开环境”标记的值并回收他们所占用的内存空间。