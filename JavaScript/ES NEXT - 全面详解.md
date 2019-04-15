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

### 不允许重复声明
let 、const 不允许在相同作用域内，重复声明同一个变量。

``` js
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}

function func(arg) {
  let arg; // 报错
}

function func(arg) {
  {
    let arg; // 不报错
  }
}
```

### let 和 const 的区别
const 用于声明常量，其值一旦被设定不能再被修改，否则会报错。所以 const 一旦声明变量，就必须立即初始化，不能留到以后赋值。

但是需要注意 const 声明是不允许修改绑定，也就是变量指向的那个内存地址所保存的数据不得改动，对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const 只能保证这个指针是固定的，至于它指向的数据结构是可以改变的。

``` js
const data = {
  value: 1
}

// 改变属性值，没有问题
data.value = 2;

// 添加一个属性，可以成功
data.num = 3;

// 指向另一个对象，就会报错
data = {}; // Uncaught TypeError: Assignment to constant variable.
```

## 字符串模版
ES6 中允许使用反引号 \` 来创建字符串，此种方法创建的字符串里面可以包含由美元符号加花括号包裹的变量 `${vraible}`。

``` js
let str = 'hello world'
console.log(`that message is ${str}`);

// 大括号内部可以放入任意的 JavaScript 表达式
`${x} + ${y} = ${x + y}`
`${obj.x + obj.y}`
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

解构不仅可以用于数组，还可以用于对象：

``` js
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
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

字符串也可以解构赋值：

``` js
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```

数组克隆：

``` js
// 克隆
let colors = [ "red", "green", "blue" ];
let [ ...clonedColors ] = colors;

console.log(clonedColors);      //"[red,green,blue]"
```

## symbol
symbol 作为一种新的原始数据类型，表示独一无二的值，主要是为了防止属性名冲突。

``` js
var isMoving = Symbol("isMoving");
typeof isMoving; // "symbol" 
```

> 注意：

> 1、Symbol 函数不能使用 new 调用；

> 2、Symbol都是不相等的，即使参数相同；

> 3、Symbol不能与其他类型的值计算，会报错；

> 4、Symbol可以转换为布尔值，但不能转为数值。


``` js
// 没有参数
let a1 = Symbol();
let a2 = Symbol();
a1 === a2; // false 

// 有参数
let b1 = Symbol('abc');
let b2 = Symbol('abc');
b1 === b2; // false 

// 不能计算
let c = Symbol('hello');
c + " world!";  // 报错
`${c} world!`;  // 报错

// 转换
let aa = Symbol();
Boolean(aa); // true
```

Symbol 的好处：防止同名属性，还有防止键被改写或覆盖。

``` js
let a = Symbol();
let b = {};
b[a] = 'hello'; // 不能用点运算符

// ------------------

const TYPE_AUDIO = Symbol()
const TYPE_VIDEO = Symbol()
const TYPE_IMAGE = Symbol()

function handleFileResource(resource) {
  switch(resource.type) {
    case TYPE_AUDIO:
      playAudio(resource)
      break
    case TYPE_VIDEO:
      playVideo(resource)
      break
    case TYPE_IMAGE:
      previewImage(resource)
      break
    default:
      throw new Error('Unknown type of resource')
  }
}

// -------------------

// 用于私有变量的实现
const Example = (function() {
    var _private = Symbol('private');

    class Example {
        constructor() {
          this[_private] = 'private';
        }
        getName() {
          return this[_private];
        }
    }

    return Example;
})();

var ex = new Example();

console.log(ex.getName()); // private
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

## for of
它结合了 forEach 的简洁性和中断循环的能力,for...of 循环可以使用的范围包括：

* 数组
* Set
* Map
* 类数组对象，如 arguments 对象、DOM NodeList 对象
* Generator 对象
* 字符串

``` js
for (const v of ['a', 'b', 'c']) {
  console.log(v); // a b c
}

for (const [i, v] of ['a', 'b', 'c'].entries()) {
  console.log(i, v);
}
// 0 "a"
// 1 "b"
// 2 "c"
```
