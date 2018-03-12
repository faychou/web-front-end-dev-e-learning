# JS 高级 - 面向对象
编程领域中一般有两种开发模式：面向过程(POP) 与 面向对象（OOP）。

面向对象编程是目前主流的编程模式，其核心思想是将真实世界中各种复杂的关系，抽象为一个个对象，然后由对象之间的分工与合作，完成对真实世界的模拟。

面向对象的两大概念：

* 类：对象的类型模板
* 实例：根据类创建的对象

从上面可以看到，在传统的编程语言中，面向对象都有一个类的概念，通过类可以创建任意多个具有相同属性和方法的对象。但是在 ES6 出现之前， JavaScript 是没有类的概念，而是使用构造函数（constructor）来作为对象的模板。而要理解原型，首先从创建对象开始：

## 创建对象

### 创建 Object 实例
``` js
var person = new Object();person.firstname = "Fay";person.lastname = "Chou";person.age = 18;person.eyeColor = "black";```

由此可见 属性是可以动态添加，修改的。

### 对象字面量
``` js
var person = {  firstname:"Fay",  lastname:"Chou",  age:18,  eyeColor:"black"};```

### 工厂模式
不难发现的一点是，通过上面的方法定义的对象，缺少了复用性。也就是说，我们每次都要写 firstname、lastname、age 等等，才能创建一个新的对象实例，这肯定是不合理的。为了能够得到更好的封装效果，我们可以通过一个 function 来统一地构建一个对象的实例。

``` js
function createPerson(firstname,lastname,age) {  var person = new Object();  person.firstname = firstname;  person.lastname = lastname;  person.age = age;  return person;};
var newPerson = createPerson("fay","chou",18);  //实例化alert(newPerson.age);  //调用```

而这种方法就是工厂模式，它就是一个函数，然后放入参数，返回对象，实现流水线的工作。

但是工厂模式存在一个问题，就是不能解决对象识别。

``` js
function createPerson1(firstname,lastname,age) {  var person = new Object();  person.firstname = firstname;  person.lastname = lastname;  person.age = age;  return person;};

function createPerson2(firstname,lastname,age) {  var person = new Object();  person.firstname = firstname;  person.lastname = lastname;  person.age = age;  return person;};

var newPerson1 = createPerson1("fay","chou",18);
var newPerson2 = createPerson2("meimei","han",17);alert(newPerson1 instanceof Object);  //true
alert(newPerson2 instanceof Object);  //true
```

### 构造函数
上面的 createPerson 并不是真正的构造函数，JavaScript 中提供了真正的构造函数，它的语法和定义一个 function 其实是一样的：

``` js
function Person(firstname,lastname,age) {  this.firstname = firstname;
  this.lastname = lastname;  this.age = age;};
var person = new Person("fay","chou",18);  //实例化alert(person.age);
```

从示例中可以知道构造函数就是一个普通函数，但是内部使用了 this。通过 new 运算符生成实例，并且 this 变量会绑定在实例对象上。

> 构造函数注意：

* 函数名第一个字母必须大写；
* 必须使用 new 运算符来实例化构造函数。

同时构造函数也能解决对象识别问题：

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
来看看 new 关键字做了哪些事？

* 创建一个空对象，用 this 变量引用该对象并继承该函数的原型；
* 属性和方法加入到 this 的引用对象中；
* 新创建的对象由 this 所引用，并且最后隐式的返回 this。

> 注意：构造函数内部有 return 语句的时候，如果 return 后面跟着一个对象，new 命令返回 return 指定的对象；否则不管 return 语句直接返回 this。

``` js
var Per = function (name) {
  this.name = name;
  return {age:18}
}
var p1 = new Per('tom');
p1.name // undefined
p1.age // 18
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
实例化两个对象，虽然 sayHi 输出的内容是一样的，但是 sayHi 方法是不一样的，就是说每实例化一个对象就生成一个新的 sayHi 方法。而所有的 sayHi 方法都是同样的行为，这完全是可以共享的。

而通过原型我们就可以实现这样的共享。

``` js
function Person(firstname,lastname,age) {  this.firstname = firstname;
  this.lastname = lastname;  this.age = age;}
 Person.prototype.eyeColor = "black";Person.prototype.showFullName = function () {  alert(this.firstname + " " + this.lastname);};
 var newPerson = new Person("fay","chou",18);alert(newPerson.eyeColor);```

由此可以看出 Person 函数中有一个 prototype 属性，并且在 prototype 上定义的成员，可以在每个实例中引用，并且是共用的。

## JavaScript 原型
Js 所有的函数在创建的时候都自动添加了 prototype 属性，它记录着一些属性和方法。这个属性引用了一个对象，即原型对象，也简称原型。通俗点讲，原型对象就是内存中为其他对象提供共享属性和方法的对象。

所以 JavaScript 也是一种基于原型的面向对象语言，即每一个对象都有一个原型，对象从原型中继承属性和方法。

### why we use prototype? -> 便于方法的重用。

与构造函数相比，使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。
运行时没找到函数的方法时，对象会首先找它的类的 prototype 方法。``` js
function People(name) {
  this.name = name;
}

People.prototype = {
  greet: function() {
    console.log('Hello, my name is ' + this.name + '!');
  }
};

var person = new People('Cassie Xu');
person.greet();

//在每个实例化后的对象都会有一个 __proto__ 属性,类会将它的 prototype 赋给实例
//实例化一个对象时，People 这个类首先会将 person 的 __proto 属性指向 People.prototype
console.log(person.__proto__ === People.prototype); // true
```
### JavaScript 原型链
每个对象都有一个内部属性 prototype，称之为 原型。原型的值可以是一个对象，也可以是 null 。如果它的值是一个对象，则这个对象也一定有自己的原型，这样就形成了一条线性的链，直到原型为 null。

原型的优点是能够以对象结构为载体，创建大量的实列，这些实列能共享原型中的成员(属性和方法)；同时也可以使用原型实现面向对象中的继承机制 。

如果跟着原型链一层层的寻找，所有对象都可以寻找到最顶层，Object.prototype, 即 Object 的构造函数的 prototype 属性，而 Object.prototype 对象指向的就是没有任何属性和方法的 null 对象。

``` js
Object.getPrototypeOf(Object.prototype)
// null
```

对象查找他的属性的过程：首先在对象本身上面找 -> 没找到再到对象的原型上找 ->还是找不到就到原型的原型上找 —>直到 Object.prototype 找不到 -> 返回 undefined。

### constructor
prototype 对象有一个 constructor 属性，默认指向 prototype 对象所在的构造函数。
由于 constructor 属性是一种原型对象与构造函数的关联关系，所以修改原型对象的时候，务必要小心。

``` js
var Person = function (name) {
  this.name = name;
}
// 尽量避免这么写，因为会把construct属性覆盖掉。
Person.prototype = {
  sayHi: function () {
    console.log('Hi');
  }
}

// 如果我们覆盖了constructor属性则要找回来
Pet.prototype.constructor = Pet;
```

### `__proto__`
访问一个对象的原型可以使用 ES5 中的 `Object.getPrototypeOf` 方法,或者 ES6 中的 `__proto__` 属性。

``` js
var foo = {   x: 10,   y: 20  //实际上foo还预留了__proto__这个属性，指向该实例的原型};

/*............ 分割线 ............*/

var a = {   x: 10,   calculate: function (z) {     return this.x + this.y + z;   } }; var b = {   y: 20,   __proto__: a }; b.calculate(30);   // 60 /*............ 分割线 ............*/function Foo(y) {   this.y = y ; } Foo.prototype.x = 10; Foo.prototype.calculate = function(z) {   return this.x + this.y + z; }; var b = new Foo(20); alert(b.calculate(30)); ```

当不指定 `__proto__` 的时候，foo 也会预留一个这样的属性， 如果有明确的指向，那么这个链表就链起来啦。b 共享 a 的属性和方法，同时又有自己的私有属性。 当定义一个 prototype 的时候，会构造一个原形对象，这个原型对象存储于构造这个 prototype 的函数的原形方法之中。

``` js
var a = {};
console.log(a.__proto__); // {}
console.log(a.__proto__.__proto__); // null
```#### `__proto__` 属性和 prototype 属性的区别
* prototype 是 function 对象中专有的属性；
* `__proto__` 是普通对象的隐式属性，在 new 的时候，会指向 prototype 所指的对象；
* 构造函数通过 prototype 属性访问原型对象；
* 浏览器实现了 `__proto__` 属性用于实例对象访问原型对象。

### 原型继承
``` js
function Animal(){
  this.super = 'animal';
}
function Cat(name){
  this.name = name;
  this.food = 'fish';
}
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
Cat.prototype.getFood = function () {
  return this.food;
}
```

