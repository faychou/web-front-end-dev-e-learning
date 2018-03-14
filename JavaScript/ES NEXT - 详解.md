# ES NEXT详解
之所以称之为 ES NEXT 而不是 ES6 ，主要是现在每年都会发布一个 JavaScript 版本，所以就干脆把 ES5 之后的版本都称之为 ES NEXT 。

## 展开符
使用展开符来扩充一个数组：

``` js
let values = [2, 3, 4];
let verbose = [1, ...values, 5];

//数组拼接
const one = ['a', 'b', 'c']
const two = ['d', 'e', 'f']
const three = ['g', 'h', 'i']
const result = [...one, ...two, ...three]
```

对对象属性的展开符的使用，而不必再使用Object.assign进行对象的扩展：

``` js
let warriors = {Steph: 95, Klay: 82, Draymond: 79};
let newWarriors = {
    ...warriors,
    Kevin: 97
}
```

案例1、使用`Math.max`方法对数组求最大值时，我们可以利用 apply 接受一个数组作为函数参数的特性：

``` js
var array = [33, 2, 9];
var maxValue = Math.max.apply(null, array)
```

而使用展开符：

``` js
const maxValue = (arr) => Math.max(...arr)
maxValue([33, 22, 9]) // 33
```

很容易的实现数组和对象的 拷贝：

``` js
const obj = { ...oldObj }
const arr = [ ...oldArr ]
```

## 数组扩展
### 解构
利用数组解构来实现值的互换。

``` js
let a = 'world', b = 'hello'
[a, b] = [b, a]
console.log(a) // -> hello
console.log(b) // -> world
```

### reduce
``` js
// 计算数组的总和
const sum = (arr) => arr.reduce((a, b) => (a + b), 0)
sum([1, 2, 3, 4]) // 10
```

## 对象扩展
使得函数声明和函数的调用更加可读。

``` js
// 一般写法
const getState = (id, force, verbose) => {
  //...do something
}
// 当调用函数时， 对这里的参数不明所以
getStuffNotBad(150, true, true)

// 优化
const getState = ({id, force, verbose}) => {
  //...do something
}
// 完美
getStuffAwesome({ id: 150, force: true, verbose: true })
```

## 函数扩展
### 箭头函数
如果无需返回值，则函数体用花括号围起来：

``` js
// 这是一个非匿名函数
const alertUser = (message) => {
    alert(message)
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

## Async Functions
基于 Promises 的实现也有缺点，所以更先进的方式，就是使用 Async 。

``` js
async handleCommentSubmit(comment) {
  try {
    let res = await fetch(url, {
      method: 'POST',
      body: JSON.stringify(comment)
    });
    newComments = await res.json();
  }
  
  catch (ex) {
    console.error(ex);
    newComments = comments;
  }

  this.setState({comments: newComments});
}
```

## class
constructor 方法是类的默认方法，通过 new 命令生成对象实例时，自动调用该方法。

``` js
class Person {
  constructor(name,age) {
    this.name = name
    this.age = age
  }
  showName() {
    console.log('姓名：' + this.name)
  }
}

const person1 = new Person('faychou',18)
person1.showName
```

> 注意：

1. constructor 只做初始化；
2. 这里的 showName 实际上就是放在原型上的方法，可以通过 `console.dir(Person)` 查看。
