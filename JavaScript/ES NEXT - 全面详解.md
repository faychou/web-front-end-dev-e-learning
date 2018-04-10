# ES NEXT 详解
之所以称之为 ES NEXT 而不是 ES6 ，主要是现在每年都会发布一个 JavaScript 版本，所以就干脆把 ES5 之后的版本都称之为 ES NEXT 。

## 字符串模版
ES6 中允许使用反引号 \` 来创建字符串，此种方法创建的字符串里面可以包含由美元符号加花括号包裹的变量 `${vraible}`。

``` js
let str = 'hello world'
console.log(`that message is ${str}`);
```

## 展开符
可以将一个数组转为用逗号分隔的参数序列，也可以将对象进行展开。

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

数组的分割：

``` js
var [a, ...b] = [0, 1, 2];
console.log(b) // [1, 2]
```

对对象属性的展开符的使用，而不必再使用 `Object.assign` 进行对象的扩展：

``` js
let warriors = {Steph: 95, Klay: 82, Draymond: 79};
let newWarriors = {
    ...warriors,
    Kevin: 97
}
```

使用 `...` 代替 arguments 类数组：

``` js
// low
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
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

很容易的实现数组和对象的拷贝：

``` js
const obj = { ...oldObj }
const arr = [ ...oldArr ]
```

## 解构
利用数组解构来实现值的互换。

``` js
let a = 'world', b = 'hello'
[a, b] = [b, a]
console.log(a) // -> hello
console.log(b) // -> world
```

自动解析到对应接收该值的变量中：

``` js
const arr = [1, 2, 3, 4];

const [first, second] = arr;
console.log(first, second);   // 1,2 
```

函数的参数如果是对象的成员，优先使用解构赋值：

``` js
// low
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;
}

// good
function getFullName({ firstName, lastName }) {
}
```

如果函数返回多个值，优先使用对象的解构赋值，而不是数组的解构赋值。这样便于以后添加返回值，以及更改返回值的顺序。

``` js
// low
function processInput(input) {
  return [left, right, top, bottom];
}

// good
function processInput(input) {
  return { left, right, top, bottom };
}

const { left, right } = processInput(input);
```

## 数组扩展
### reduce
``` js
// 计算数组的总和
const sum = (arr) => arr.reduce((a, b) => (a + b), 0)
sum([1, 2, 3, 4]) // 10
```

### 类数组转化
用 Array.from 方法，将类似数组的对象转为数组：

``` js
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
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

## Proxy
Proxy 对象就是可以让你去对 JavaScript 中的一切合法对象的基本操作进行自定义，然后用你自定义的操作去覆盖其对象的基本操作。

``` js
let p = new Proxy(target, handler);
```

* target：要代理的对象，可以是 JavaScript 中的任何合法对象(数组, 对象, 函数等)；

* handler：自定义操作方法的一个集合；

* p：被代理后的新对象，它拥有 target 的一切属性和方法，只不过其行为和结果是在 handler 中自定义的。

``` js
let obj = {
  a: 1,
  b: 2,
}

const p = new Proxy(obj, {
  get(target, key, value) {
    if (key === 'c') {
      return '我是自定义的一个结果'
    } else {
      return target[key]
    }
  },

  set(target, key, value) {
    if (value === 4) {
      target[key] = '我是自定义的一个结果'
    } else {
      target[key] = value
    }
  }
})

console.log(obj.a) // 1
console.log(obj.c) // undefined
console.log(p.a) // 1
console.log(p.c) // 我是自定义的一个结果

obj.name = 'Fay';
console.log(obj.name); // Fay
obj.age = 4;
console.log(obj.age); // 4

p.name = 'Fay';
console.log(p.name); // Fay
p.age = 4;
console.log(p.age); // 我是自定义的一个结果
```

在上面代码中，第二个参数 handler 对象是由 get 和 set 两个函数方法组成的，这两个方法会在一个对象被 get 和 set 时被调用执行，以代替原生对象上的操作。实际上 handler 是 ES6 所新设计的一个对象，它的作用就是用来自定义代理对象的各种可代理操作。它本身一共有 13 种方法，每种方法都可以代理一种操作。

``` js
// 读取代理对象的原型时触发，如执行 Object.getPrototypeOf(proxy)
handler.getPrototypeOf()

// 设置代理对象的原型时触发，如执行 Object.setPrototypeOf(proxy, null)
handler.setPrototypeOf()

// 判断一个代理对象是否是可扩展时触发，如执行 Object.isExtensible(proxy)
handler.isExtensible()

// 让一个代理对象不可扩展时触发，如执行 Object.preventExtensions(proxy)
handler.preventExtensions()

// 获取代理对象某个属性描述时触发，如执行 Object.getOwnPropertyDescriptor(proxy, "foo")
handler.getOwnPropertyDescriptor()

// 定义代理对象某个属性描述时触发，如执行 Object.defineProperty(proxy, "foo", {})
handler.defineProperty()

// 判断代理对象是否拥有某个属性时触发，如执行 "foo" in proxy
handler.has()

// 读取代理对象的某个属性时触发，如执行 proxy.foo
handler.get()

// 给代理对象的某个属性赋值时触发，如执行 proxy.foo = 1
handler.set()

// 删除代理对象的某个属性时触发，如执行 delete proxy.foo
handler.deleteProperty()

// 获取代理对象的所有属性键时触发，如执行 Object.getOwnPropertyNames(proxy)
handler.ownKeys()

// 调用一个目标对象为函数的代理对象时触发，如执行 proxy()
handler.apply()

// 给一个目标对象为构造函数的代理对象构造实例时触发，如执行 new proxy()
handler.construct()
```

### Proxy 作用
* 拦截和监视外部对对象的访问；

* 降低函数或类的复杂度；

* 在复杂操作前对操作进行校验或对所需资源进行管理。

如，可以监听对象身上发生了什么事情，并在这些事情发生后执行一些相应的操作。一下子让我们对一个对象有了很强的追踪能力，同时在数据绑定方面也很有用处。

``` js
// 定义被监听的目标对象
let engineer = { name: 'Fay', age: 50 }

// 定义处理程序
let interceptor = {
  set(receiver, property, value) {
    console.log(property, 'is changed to', value)
    receiver[property] = value
  }
}

// 创建代理以进行侦听
engineer = new Proxy(engineer, interceptor)

// 做一些改动来触发代理
engineer.age = 18; // 控制台输出：age is changed to 18
```
