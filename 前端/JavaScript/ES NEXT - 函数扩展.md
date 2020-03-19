# 函数扩展
### 默认参数
在定义函数的时候指定参数的默认值。

``` js
function sayHello(name) {
  var name = name || 'tom';   // 传统的指定默认参数的方式
  console.log('Hello '+name);
}

// ES6 的默认参数
function sayHello2(name = 'tom') {  // 如果没有传这个参数，才会有默认值，
  console.log(`Hello ${name}`);
}

// 可以给解构参数本身提供默认值
function setCookie(name, value, { secure, path, domain, expires } = {}) {
  // ...
}
```

### 箭头函数
ES6 允许使用“箭头”（=>）定义函数。

``` js
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};
```

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。

``` js
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

如果无需返回值，则函数体用花括号围起来：

``` js
// 这是一个非匿名函数
const alertUser = (message) => {
  alert(message)
}
```

使用可选参数：

``` js
var add = (...nums) => {
  var numArr = [].concat(nums)
  return numArr.reduce((acc, v) => acc += v)
}
```

#### 箭头函数中的 this
函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

箭头函数不使用 this 的四种绑定规则，而是根据外层作用域来决定 this。

``` js
$.ajax({
  url: url,
  data: comment,
  success: function(resJson) {
    this.setState({comments: resJson})
  }.bind(this)
})
```

在 ES6 箭头函数下，我们可以直接这样写：

``` js
$.ajax({
  url: url,
  data: comment,
  success: (resJson) => {
    this.setState({comments: resJson})
  }
});
```

> 注意：

* 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

* 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

* 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。