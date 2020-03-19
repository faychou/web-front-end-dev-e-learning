# class
ES6 的 class可以看作只是一个语法糖，提供了更接近传统语言类的写法，作为对象的模板。

``` js
// ES5
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);

// ES6
class Point {
  // 构造函数，相当于之前的函数 Point
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  // 相当于挂载在原型链上的原型方法
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

// 生成对象实例
let a = new Point(1, 2)
// 调用原型方法
a.toString()	// 1,2
```

上面的 constructor 方法，这就是构造方法，而 this 关键字则代表实例对象。类的所有方法都定义在类的 prototype 属性上面。

class不存在变量提升。

### constructor
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
2. 一个类必须有 constructor() 方法，否则一个空的 constructor() 会默认添加。
3. 这里的 showName 实际上就是放在原型上的方法，可以通过 `console.dir(Person)` 查看。

### 类的实例化
必须使用 new 命令实例化对象，否则将会报错。

``` js
class Person {
  // ...
}

var person = Person(); // 报错
var person = new Person(); // 正确
```

## class 的静态方法、属性
所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上 `static` 关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

``` js
class Person {
  static run() {
    console.log('fast!');
  }
  static country = 'china';
}

Person.run() // 'fast'

var person = new Person();
person.run();
// TypeError: person.run is not a function
```

如果静态方法包含 this 关键字，这个 this 指的是类，而不是实例。

``` js
class Foo {
  static bar () {
    this.baz();
  }
  static baz () {
    console.log('hello');
  }
  baz () {
    console.log('world');
  }
}

Foo.bar() // hello
```

父类的静态方法，可以被子类继承。

``` js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

类的静态属性只要在实例属性前面加上static关键字就可以。

``` js
class P {
    static prop = 100;
    constructor(){console.log(this.prop)}; // 100
}
```

## 继承
Class 可以通过 extends 关键字实现继承。

``` js
class Father {
  constructor(props) {
    this.props = props;
  }
}

class Child extends Father {
  constructor(props) {
    super(props); // 调用父类的constructor(props)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的 toString()
  }
}
```

通过 extends 关键字，Child 继承了 Father 类的所有属性和方法。子类必须在 constructor 方法中调用 super 方法，否则新建实例时会报错。super 在这里表示父类的构造函数，用来新建父类的 this 对象。

### super
super 指代父类的构造函数，只能用在子类的构造函数之中，用在其他地方就会报错。

在你调用父类构造函数之前，你无法在构造函数中使用 this。

super 当函数调用，代表父类的构造函数，但必须执行一次。

``` js
class P {... };
class R extends P {
    constructor(){
        super();
    }
}
```

super 当对象调用，指向原型对象，在静态方法中指向父类。

``` js
class P {
    f (){ return 2 };
}
class R extends P {
    constructor (){
        super();
        console.log(super.f()); // 2
    }
}
let a = new R()
```

## getter 和 setter
使用 get 和 set 关键词对属性设置取值函数和存值函数，拦截属性的存取行为。

``` js
class P {
    constructor (){ ... }
    get f (){
        return 'getter';
    }
    set f (val) {
        console.log('setter: ' + val);
    }
}

let a = new P();
a.f = 100;   // setter : 100
a.f;          // getter
```

## 实现 class 私有变量
虽然 class 本身没有提供私有变量的功能，但是可以通过通过一些方式来实现类似于私有变量的功能。

首先约定一种代表着私有变量的命名方式，一般是在私有变量的名称前加上一个下划线 `_`。

``` js
class Point {
  constructor(x, y) {
    // _x 是一个私有变量
    this._x = x;
    this.y = y;
  }

  showX() {
    return this._x ;
  }
}

let p = new Point(1, 2);

// _x 依然可以被使用
p._x		// 1
p.showX()	// 1
```

通过 IIFE （立即执行函数表达式） 建立一个闭包，在其中建立一个变量以及 class ，通过 class 引用变量实现私有变量。

``` js
// 利用闭包生成 IIFE，返回类 Point
const Point = (function() {
  // 定义私有变量_x
  let _x

  class Point {
    constructor (x, y) {
      // 初始化私有变量_x
      _x = x
    }

    showX () {
      return _x
    }
  }

  return Point
})();

let p = new Point(1, 2)

// 无法访问
p._x		// undefined
// 可以访问
p.showX()	// 1
```

另一种方案是利用 Symbol 的唯一性，来实现变量私有：

``` js
// 定义symbol
const _x = Symbol('x')

class Point {
  constructor (x) {
    // 利用symbol声明私有变量
    this[_x] = x;
  }
  showX () {
    return this[_x];
  }
}

let p = new Point(1, 2);

// 自行定义一个相同的 Symbol
const x = Symbol('x');
// 无法访问
a[x];  // undefined
// 可以访问
p.showX();	// 1
```

### 私有属性提案
以上两种方法都可以实现私有属性，但是毕竟不是正统的，所以有一个提案，在属性名之前加上 `#` ，用于表示私有属性。

``` js
class Point {
  #x = 0
  constructor (x) {
    #x = x
  }
  showX () {
    return this.#x
  }
}
```