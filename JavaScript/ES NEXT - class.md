# class
ES6 提供了更接近传统语言类的写法，引入了 Class 这个概念，作为对象的模板。

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
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

上面的 constructor 方法，这就是构造方法，而 this 关键字则代表实例对象。类的所有方法都定义在类的 prototype 属性上面。

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
2. 这里的 showName 实际上就是放在原型上的方法，可以通过 `console.dir(Person)` 查看。

### 类的实例化
必须使用 new 命令实例化对象，否则将会报错。

``` js
class Person {
  // ...
}

// 正确
var person = new Person();
```

### Class 的静态方法、属性
所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上 static 关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

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

如果静态方法包含this关键字，这个this指的是类，而不是实例。

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

## 继承
Class 可以通过extends关键字实现继承。

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
