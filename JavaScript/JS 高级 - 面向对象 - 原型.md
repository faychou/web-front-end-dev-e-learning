# 原型
JS 所有的函数在创建的时候都会自动为其添加一个 prototype 属性，它记录着一些属性和方法。这个属性引用了一个对象，即原型对象，也简称原型。是 JavaScript 面向对象开发中最重要的一个概念。通俗点讲，原型对象就是内存中为其他对象提供共享属性和方法的对象。

所以 JavaScript 是一种基于原型的面向对象语言，即每一个对象都有一个原型，对象从原型中继承属性和方法。

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

### constructor
每个 prototype 对象都有一个 constructor 属性，这个属性始终指向 prototype 对象所在的构造函数。

``` js
function Person() {

}
console.log(Person === Person.prototype.constructor); // true
```

由于 constructor 属性有原型对象与构造函数的关联关系，所以修改原型对象的时候，务必要小心。

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
每个 JavaScript 对象(除了 null )都具有一个 `__proto__` 属性，它是对构造函数的 prototype 对象的引用。

``` js
function Person() {

}
var person = new Person();
console.log(person.__proto__ === Person.prototype); // true
```

``` js
function Foo() {};
var foo = new Foo();
console.log(foo.__proto__ === Foo.prototype); // ture
```

由于 `Foo.prototype` 是 Object 预创建的一个对象，是 Object 创建的一个实例，所以 `Foo.prototype.__proto_` 是 `Object.prototype` 的引用。

``` js
function Foo(){};
console.log(Foo.prototype.__proto__ === Object.prototype); // true
```

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

### 函数（function）对象的原型
在 javascript 中，函数是一种特殊的对象，所有的函数都是构造函数 Function 的实例。

``` js
function Foo() {};
console.log(Foo.__proto__ === Object.prototype); //false
console.log(Foo.__proto__ === Function.prototype); // true
console.log(Foo.prototype.__proto__ === Object.prototype);//true
```

### Object.create() 
会创建一个对象并把这个对象的 Prototype 关联到指定的对象。

``` js
var obj = {
  a:1
};

// 创建一个关联到 obj 的对象
var newObject = Object.create( obj );
newObject.a; // 2
```

`Object.create(null)` 会 创 建 一 个 拥 有 空( 或 者 说 null) Prototype 链接的对象，这个对象无法进行委托。由于这个对象没有原型链，所以 instanceof 操作符(之前解释过)无法进行判断，因此总是会返回 false。 这些特殊的空 Prototype 对象通常被称作“字典”，它们完全不会受到原 型链的干扰，因此非常适合用来存储数据。

#### 行为委托
``` js
var anotherObject = {
    cool: function() {
        console.log( "cool!" );
    }
};

var myObject = Object.create( anotherObject );

myObject.doCool = function() {
    this.cool(); // 内部委托!
};

myObject.doCool(); // "cool!"
```

调用的 myObject.doCool() 是实际存在于 myObject 中的，这可以让我们的 API 设 计更加清晰。从内部来说，我们的实现遵循的是委托设计模式，通过 Prototype 委托到 anotherObject.cool()。换句话说，内部委托比起直接委托可以让 API 接口设计更加清晰。

### JavaScript 原型链
每个对象都有一个内部属性 prototype，称之为 原型。原型的值可以是一个对象，也可以是 null 。如果它的值是一个对象，则这个对象也一定有自己的原型，这样就形成了一条线性的链，直到原型为 null。

原型的优点是能够以对象结构为载体，创建大量的实列，这些实列能共享原型中的成员(属性和方法)；同时也可以使用原型实现面向对象中的继承机制 。

如果跟着原型链一层层的寻找，所有对象都可以寻找到最顶层，Object.prototype, 即 Object 的构造函数的 prototype 属性，而 Object.prototype 对象指向的就是没有任何属性和方法的 null 对象。

``` js
console.log(Object.prototype.__proto__ === null) // true
```

### 属性优先级
对象查找属性的过程：首先在对象本身上面找 -> 没找到再到对象的原型上找 ->还是找不到就到原型的原型上找 —>一直找到最顶层 Object.prototype 为止，如果找不到就返回 undefined。