# ES NEXT 详解
之所以称之为 ES NEXT 而不是 ES6 ，主要是现在每年都会发布一个 JavaScript 版本，所以就干脆把 ES5 之后的版本都称之为 ES NEXT 。

## let 和 const
### 块级作用域
let 和 const 都是块级声明的一种。

什么是块级作用域？

* 函数内部
* 块中(字符 { 和 } 之间的区域)

### 变量不会被提升
let 和 const 声明的变量不会被提升到作用域顶部，所以如果在声明之前访问这些变量，会导致报错：

``` js
if (false) {
  let value = 1;
}
console.log(value); // Uncaught ReferenceError: value is not defined
```

### let 和 const 的区别
const 用于声明常量，其值一旦被设定不能再被修改，否则会报错。但是需要注意 const 声明是不允许修改绑定，但允许修改值。

``` js
const data = {
    value: 1
}

// 没有问题
data.value = 2;
data.num = 3;

// 报错
data = {}; // Uncaught TypeError: Assignment to constant variable.
```

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
