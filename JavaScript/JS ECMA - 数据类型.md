# JS ECMAScript - 数据类型
JavaScript 是一种弱类型语言。这意味着你不用提前声明变量的类型，在程序运行过程中，类型会被自动确定。这也意味着你可以使用同一个变量保存不同类型的数据，最新的 ECMAScript 标准定义了 7 种数据类型:

### 基本类型：
存放在栈内存中的数据段，因为是按值存储，可以直接访问和修改。

* String
* Number
* Boolean
* Null
* Undefined
* Symbol (ECMAScript 6 新定义)

### 引用类型：
存放在堆内存中的对象。在栈内存中变量保存的是一个指针，指向对应在堆内存中的地址。当访问引用类型的时候，要先从栈中取出该对象的地址指针，然后再从堆内存中取得所需的数据。

* Object

对象类型涵盖了很多引用类型，包括（Array、Function）。

### 可变性
基本类型：不可变类型，无法添加属性；即使添加属性，解析器无法再下一步读取它；

``` js
var cat = "cat"; //基本数据类型
cat.color = "black";
cat.color // undefined
```

对象：可变类型，支持添加和删除属性。

### 值比较
* 基本类型：按值比较；
* 对象：按引用比较。

``` js
// 基本类型
var cat = "tom";
var dog = "tom";
cat === dog // true

//对象
var cat = {name:"tom"};
var dog = {name:"tom"};
cat === dog //false

//修改对象引用同一个地址
var cat = {name:"tom"}
var dog = cat;
cat.name = "Haba"

dog === cat // true
```

``` js
var a = [1, 2, 3, 4, 5]
var b = a;
var c = a[0]

alert(b) // 1,2,3,4,5
alert(c) // 1

// 改变数值
b[4] = 6
c = 7
alert(a[4]) // 6
alert(a[0]) // 1
```

a 赋值给 b，在栈内存中其实是将 a 的地址复制一份给 b，此时 a 和 b 访问的都是同一个地址的对象，所以修改 b[4] 会直接修改堆内存中的对象的值，a 和 b 都同时访问该值，a 和 b 就产生了关联。c 是通过直接赋值堆内存中的对象的值（一个基本类型），这就是按值传递了，所以 c 不会和 a、b 建立联系。

### 数据类型检测
#### typeof
typeof 只有在基本类型的检测上面才好使，在引用类型（Function 除外）里面他返回的都是 object，另 typeof null === "object"，why?

不同的对象在底层都表示为二进制，在 JavaScript 中二进制前三位都为 0 的话会被判断为 object 类型，null 的二进制表示是全 0，自然前三位也是 0，所以执行 typeof 时会返回 object 。

``` js
console.log(typeof 'k') //string
console.log(typeof 123) // number
console.log(typeof true) //boolean
console.log(typeof undefined) //undefined
console.log(typeof function a(){} ) //function
console.log(typeof null) //object
console.log(typeof []) //object
console.log(typeof {}) //object
console.log(typeof new Date()) //object
console.log(typeof new RegExp()) //object
```

#### instanceof
instanceof 不仅检验构造这个对象的构造器，还检验原型链上的构造器，所以所有非基本类型 & undefined & null, instanceof Object 都是 true。主要用来判断自定义对象。

#### 对象检测
检测一个对象的类型，强烈推荐使用 `Object.prototype.toString` 方法，这是唯一一个可依赖的方式。

``` js
Object.prototype.toString.call([])    // "[object Array]"
Object.prototype.toString.call({})    // "[object Object]"
Object.prototype.toString.call(2)    // "[object Number]"
```