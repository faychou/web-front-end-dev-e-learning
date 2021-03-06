# 函数式编程
函数式编程是一个比较抽象的概念，与面向对象编程一样，是一种编程风格，这种编程风格就是试图将传递函数作为参数（即将作为回调函数）和返回一个函数，但没有函数副作用（函数副作用即会改变程序的状态）。



在函数式编程中，只能用表达式，不能用语句，因为数学里面没有语句。

因为变量只能被赋值一次，不能修改变量的值，所以不存在副作用，也不能修改状态。

函数之间运行全靠参数传递，而且这个参数是不会被修改的。

## 纯函数
对于一个函数来说，使用相同的输入，永远会得到相同的输出，不依赖外部环境的状态，同时也不对外部的环境造成影响（即没有副作用）。

``` js
function getSum (n1, n2) {
    return n1 + n2;
}

console.log(getSum(1, 2));
console.log(getSum(1, 2));
console.log(getSum(1, 2));
// 相同的输入始终会得到相同的输出
```

作用：

* 可缓存，因为纯函数对相同的输入始终会有相同的输出，所以可以把纯函数的结果进行缓存
* 可测试，因为纯函数始终有输入和输出，而单元测试就是在断言函数的结果

``` js
// 缓存函数的实现
function memorize(fn) {
  const cache = Object.create(null); // 存储缓存数据的对象
  return function (...args) {
    const _args = JSON.stringify(args);
    return cache[_args] || (cache[_args] = fn.apply(fn, args));
  };
};

let complexCalc = (a, b) => {
  // 执行复杂的计算
};

let memoCalc = memorize(complexCalc);
memoCalc(666, 888);
memoCalc(666, 888); // 从缓存中获取
```



## 函数的柯里化
柯里化（英语：Currying），又译为卡瑞化或加里化，是把接受多个参数的函数变换成接受一个单一参数的函数，并且返回接受余下参数的新函数。

也就是说，传递给函数一个参数来调用它，让它不断的返回一个函数去处理剩下的参数，直到所有参数都用完为止。

``` js
// 一般写法
function add(x,y) {
  return x + y;
}

// 改造为函数柯里化 - ES5 写法
var add = function(x){
    return function(y){
        return x + y
    }
}

// 改造为函数柯里化 - ES6 写法
var add = x => (y => x + y);

// 使用
var add2 = add(2);
var add200 = add(200);

// or
add(2)(200);
```

函数柯里化是一种“预加载”函数的方法，通过传递较少的参数，得到一个已经记住了这些参数的新函数，某种意义上讲，这是一种对参数的“缓存”，是一种非常高效的编写函数的方法。

作用：避免频繁调用具有相同参数的函数。

``` js
// 商店针对不同人群，同一商品折扣不一样
function discount(discount) {
  return (price) => {
    return price * discount;
  }
}

const p1 = discount(0.1);
p1(100);

const p2 = discount(0.2);
p2(100);
```

## 高阶函数
高阶函数是一个接收函数作为参数或将函数作为输出返回的函数。如，`Array.prototype.map`，`Array.prototype.filter` 和 `Array.prototype.reduce` 是 JS 中内置的一些高阶函数。

## 函数组合

可以把两个及两个以上的纯函数结合到一起形成一个新函数的过程，这种灵活的组合可以让我们像拼积木一样来组合函数式的代码。

``` js
// 两个函数的组合
var compose = function(f, g) {
    return function(x) {
        return f(g(x));
    };
};

// ES6
var compose = (f, g) => (x => f(g(x)));

var add1 = x => x + 1;
var mul5 = x => x * 5;

compose(mul5, add1)(2);
// =>15 
```

例子，实现一个函数去除字符串前后空格并转为小写并分割为数组：

``` js
function lowerCase(input) {
  return input && typeof input === "string" ? input.toLowerCase() : input;
}

function trim(input) {
  return typeof input === "string" ? input.trim() : input;
}

function split(input, delimiter = ",") {
  return typeof input === "string" ? input.split(delimiter) : input;
}

const trimLowerCaseAndSplit = compose(trim, lowerCase, split);
trimLowerCaseAndSplit(" a,B,C "); // ["a", "b", "c"]

// 组合函数实现
function compose(...funcs) {
  return function (x) {
    return funcs.reduce(function (arg, fn) {
      return fn(arg);
    }, x);
  };
}
```

