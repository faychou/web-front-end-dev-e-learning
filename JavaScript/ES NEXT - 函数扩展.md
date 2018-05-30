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
```

### 箭头函数
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