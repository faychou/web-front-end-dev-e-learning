# JS 高级 - 面向对象
编程领域中一般有两种开发模式：面向过程(POP) 与 面向对象（OOP）。

面向过程编程采取的是”时间”换”空间”的策略，更关注的是做事的过程。

面向对象编程是目前主流的编程模式，其核心思想是将真实世界中各种复杂的关系，抽象为一个个对象，然后由对象之间的分工与合作，完成对真实世界的模拟。更关注的一件事情的参与者和他们的行为。

在传统的编程语言中，面向对象都有两大概念：

* 类：通常指现实生活中相同的对象间一般共同的特征，是对事物的抽象。也就是说类是对象的模板，通过类可以创建任意多个具有相同属性和方法的对象。

* 对象（实例）：实实在在存在的，应该具有某些明确的特征（如：姓名，年龄，身高，发色等），能执行某些动作（如：逛、吃、跑步，做瑜伽等），是根据类所创建出来的。

一个类，通常具有两大特征：属性 和 方法。

* 属性：是指一个类型具有的共同“特征”。如人有2个眼睛，汽车有4个轮子等等。值是各种基本类型，也可以是一个对象类型。

* 方法：是指一个类型具有的共同“行为”。如人能说话，狗吃骨头等。值是一个函数。

在 ES6 出现之前， JavaScript 是没有类的概念，但是 javascript 也是面向对象的语言，而这种面向对象的方式是基于原型模式的面向对象语言。如果要理解基于原型实现面向对象的思想，那么掌握以下几个概念就显得尤为重要： 对象（Object）、构造函数（constructor）、原型（prototype）、原型链（prototype chain） 。
## 理解对象
JavaScript 的所有类型，都源于一个 Object，也就是我们常说的万物皆对象。

### 内置对象
``` js
var obj = new Object();
obj.name = 'fay';
obj.age=18;
```

### 函数对象
``` js
new Function(形参参数1,参数2,... ,函数体);
```

### 对象操作
#### 创建属性
对象.属性名=属性值

对象['属性名']=属性值

``` js
var person = new Object();

// 通过明确的属性名添加
person.name ='jack';

// 通过不明确的属性名添加
var str = "age";
person[str] = 13;
```

#### 访问属性
对象.属性名

对象['属性名']

#### 修改属性
对象.属性名=新属性值

对象['属性名']=新属性值

#### 删除属性
对象.属性名 = undefine;(不标准)

delete 对象.属性名  (只能删属性)

#### 设置方法
``` js
var person = new Object();
person.run = function() {
  //...
}
```

#### 调用方法
``` js
person.run();
```
## 创建自定义对象
创建对象包括了几种经典的模式；工厂模式，构造函数模式，原型模式 等。

### 对象字面量
``` js
var person = {  firstname:"Fay",  lastname:"Chou",  age:18,  eyeColor:"black"};```

### 工厂模式
通过上面的方法定义的对象，缺少了复用性。也就是说，我们每次都要写 firstname、lastname、age 等，才能创建一个新的对象实例，这肯定是不合理的。为了能够得到更好的封装效果，我们可以通过一个 function 来统一地构建一个对象的实例。

``` js
function createPerson(firstname,lastname,age) {  var person = new Object();  person.firstname = firstname;  person.lastname = lastname;  person.age = age;  return person;};
var newPerson = createPerson("fay","chou",18);  //实例化alert(newPerson.age);  //调用```

而这种方法就是工厂模式，它就是一个函数，然后放入参数，返回对象，实现流水线的工作。但是工厂模式存在一个问题，就是不能解决对象识别。

``` js
function createPerson1(firstname,lastname,age) {  var person = new Object();  person.firstname = firstname;  person.lastname = lastname;  person.age = age;  return person;};

function createPerson2(firstname,lastname,age) {  var person = new Object();  person.firstname = firstname;  person.lastname = lastname;  person.age = age;  return person;};

var newPerson1 = createPerson1("fay","chou",18);
var newPerson2 = createPerson2("meimei","han",17);alert(newPerson1 instanceof Object);  //true
alert(newPerson2 instanceof Object);  //true
```

### 构造函数
上面的工厂模式并不是真正的构造函数，JavaScript 中提供了真正的构造函数，它的语法和定义一个 function 其实是一样的。构造函数是用来初始化对象的，像原生的构造函数 Object, Array 一样，可以创建自定义的构造函数，从而自定义对象的属性与方法。上面的 createPerson 并不是真正的构造函数，构造函数的语法和定义一个 function 其实是一样的：

``` js
function Person(firstname,lastname,age) {  this.firstname = firstname;
  this.lastname = lastname;  this.age = age;};
var person = new Person("fay","chou",18);  //实例化alert(person.age);
```

从示例中可以知道构造函数就是一个普通函数，但是内部使用了 this。通过 new 运算符生成实例，并且 this 变量会绑定在实例对象上。

> 构造函数注意：

* 函数名第一个字母必须大写；
* 必须使用 new 运算符来实例化构造函数。

与工厂模式相比，构造函数没有显示创建 Object 实例，而且可以解决对象识别问题：

``` js
function Person1(firstname,lastname,age) {  this.firstname = firstname;
  this.lastname = lastname;  this.age = age;};

function Person2(firstname,lastname,age) {  this.firstname = firstname;
  this.lastname = lastname;  this.age = age;};
var person1 = new Person1("fay","chou",18);
var person2 = new Person2("meimei","han",17);
alert(person1 instanceof Object);  //true
alert(person2 instanceof Object);  //true
alert(person1 instanceof Person1);  //true
alert(person2 instanceof Person2);  //true
alert(person1 instanceof Person2);  //false
```

通过构造函数创建的对象，都有一个 constructor 属性，指向他们的构造函数；如：``` jsalert(person.constructor == Person);  //true
```

#### new
我们知道构造函数并没有显示创建 Object 实例，那对象是如何生成的勒，来看看 new 关键字做了哪些事？

* 创建一个空对象；
* 将构造函数作用域赋予新对象（因此 this 指向新对象）
* 执行构造函数中的代码（为新对象添加属性）；
* 返回新对象。

> 注意：构造函数内部有 return 语句的时候，如果 return 后面跟着一个对象，new 命令返回 return 指定的对象；否则不管 return 语句直接返回 this。

``` js
var Person = function (name) {
  this.name = name;
  return {age:18}
}
var p1 = new Person('tom');
p1.name // undefined
p1.age // 18
```

#### 作用域安全的构造函数
作用域安全的构造函数在进行更改前，首先确认 this 对象是正确类型的实例，如果不是，就创建新的对象并且返回。

``` js
function Person(name, age) {
  if(this instanceof Person) { // 检查 this 对象是不是 Person 的实例
    this.name = name;
    this.age = age;     
  } else {
    return new Person(name, age);
  }
}

let p1 = new Person("jack", 20);
p1.name // jack

let p2 = Person("jack", 20);
p2.name // jack
```

### 原型
上面通过构造函数创建实例，实例对象的属性和方法都在构造函数内部实现。这样构造函数就会有一个缺点。

``` js
function Person(name) {  this.name = name;  this.sayHi = function() {
    conssole.log('Hi');
  };};
var p1 = new Person('tom');
var p2 = new Person('jery');

p1.sayHi === p2.sayHi // false
```

从上面可以看出，虽然 sayHi 输出的内容是一样的，但是 sayHi 方法是不一样的，就是说每实例化一个对象就生成一个新的 sayHi 方法。而所有的 sayHi 方法其实都是同样的行为，这就会造成内存空间的浪费，因为 sayHi 方法完全是可以共享的。

而通过原型我们就可以实现这样的共享。

``` js
function Person(firstname,lastname,age) {  this.firstname = firstname;
  this.lastname = lastname;  this.age = age;}
 Person.prototype.eyeColor = "black";Person.prototype.showFullName = function () {  alert(this.firstname + " " + this.lastname);};
 var newPerson = new Person("fay","chou",18);alert(newPerson.eyeColor);```

由此可以看出 Person 函数中有一个 prototype 属性，并且在 prototype 上定义的成员，可以在每个实例中引用，并且是共用的。

