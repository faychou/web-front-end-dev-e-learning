# 面向对象三大特征
面向对象拥有三大特征，封装、继承、多态。JavaScript 作为面向对象的语言，也实现了这三大特征。

## 封装


## 继承
继承是一种让一个对象可以获得另一个对象所有的属性和方法的机制。

### 类式继承
``` js
//声明父类
function SupperClass(value){
  this.value = value;
  this.fn = function(){
    console.log(this.value);
  }
}
SupperClass.prototype.otherValue = 'other value';

// 声明子类，并使得子类继承自 SupperClass
function SubClass(value){
  this.subValue = value;
}
SubClass.prototype = new SupperClass("I'm supper value");

// 使用
var Instance = new SubClass("I'm sub value");
console.log(Instance.value);
console.log(Instance.otherValue);
console.log(Instance.subValue);
Instance.fn();
```
但是这种方式存在着一些问题：

* 子类继承自父类的实例，而实例化父类的过程在声明阶段，因此在实际使用过程中无法根据实际情况向父类穿参。

* 子类的 Instance.constructor = SupperClass，因为 SubClass 并没有 constructor 属性，所以最终会从 SupperClass.prototype 处继承得到该属性。

### 构造函数继承
``` js
function SupperClass(value1){
  this.xx = value1;
}
function SubClass(value1,value2){
  SupperClass.call(this,value1);
  this.xx = value2;
}

// 使用
var Instance = new SubClass('value1','value2');
```

虽然构造函数继承解决了类式继承的不能实时向父类传参的问题，但是由于其没有一条完整的原型链，因此 子类不能继承父类的原型属性与原型方法，并非真正的继承。

### 组合继承
``` js
// 声明父类
function SupperClass(value) {
  this.value = value;
  this.fn = function() {
    console.log(this.value);
  }
}
SupperClass.prototype.otherValue = 'other value';

// 声明子类，并使得子类继承自SupperClass
function SubClass(value1,value2) {
  SupperClass.call(this,value1)
  this.subValue = value2;
}
SubClass.prototype = new SupperClass("I'm supper value");

// 使用
var Instance = new SubClass("I'm supper value","I'm sub value");
```

组合式继承集合了以上两种继承方式的优点，从而实现了继承所有属性并能动态传参的功能。但是这种方式仍然不能补齐子类的家庭成员关系，因为 SubClass.prototype 仍然是父类的实例。

### 原型继承
``` js
// Vehicle - 超类
function Vehicle (name) {
    this.name = name;
}
// 超类的方法
Vehicle.prototype.start = function () {
    return "engine of " + this.name + " starting...";
}

// Car - 子类
function Car (name) {
    Vehicle.call(this, name); //  调用超类的构造函数
}
// 子类扩展超类
Car.prototype = Object.create(Vehicle.prototype);
// 子类的方法
Car.prototype.run = function () {
    console.log("Hello " + this.start());
}

// 子类的实例
var c1 = new Car("Fiesta");
var c2 = new Car("Baleno");

// 访问 内部访问了超类方法 的子类方法
c1.run();   // "Hello engine of Fiesta starting..."
c2.run();   // "Hello engine of Baleno starting..."
```

改造：

``` js
// 包含初始化方法的基础对象
var Vehicle = {
    init: function (name) {
        this.name = name;
    },
    start: function () {
        return "engine of " + this.name + "starting...";
    }
}

// 在子对象和基础对象之间创建的委托链接
var Car = Object.create(Vehicle);

// 子对象的方法
Car.run = function () {
    console.log("Hello " + this.start());
};

// 具有委托链接的实例对象指向子对象
var c1 = Object.create(Car);
c1.init('Fiesta');

var c2 = Object.create(Car);
c2.init('Baleno');

c1.run();   // "Hello engine of Fiesta starting..."
c2.run();   // "Hello engine of Baleno starting..."
```

### 寄生式继承--原型式继承的二次封装
``` js
var supperObj = {
  key1: 'value',
  func: function() {
    console.log(this.key1);
  }
}

function inheritPrototype(obj,value) {
  // var subObj = Factory(obj);
  var subObj = Object.create(obj);
  subObj.name = value;
  subObj.say = function() {
    console.log(this.name);
  }
  return subObj;
}

var Instance = inheritPrototype(supperObj,'sub');
Instance.func();
Instance.say();
```

### 将类式继承与寄生式继承结合
``` js
function inheritPrototype(sub,sup) {
  var obj = Object.create(sup.prototype);
  sub.prototype = obj;
  obj.constructor = sub;
  Object.defineProperty(obj,'constructor',{enumerable: false});
  // 将 constructor 属性变为不可遍历，避免多继承时出现问题
}

// 声明父类
function SupperClass(value1) {
  this.supperValue = value1;
  this.func1 = function() {
    console.log(1);
  }
}
SupperClass.prototype.func2 = function() {
  console.log(this.supperValue);
}

// 声明子类
function SubClass(value2) {
  this.subValue = value2;
  this.func3 = function() {
    console.log(this.subValue);
  }
}

inheritPrototype(SubClass,SupperClass);
var Instance = new SubClass('sub');
console.log(Instance.supperValue);  //undefined
console.log(Instance.subValue); //sub
Instance.func1();   //Error
Instance.func2();   //undefined
Instance.func3();   //sub
```

也有一个致命的缺陷----由于obj对象不是SupperClass的实例，所以在实例化子类的时候父类构造函数从未被调用过，因此 子类只能继承到父类原型属性与方法，无法继承到父类自有方法。

### 寄生组合继承
``` js
function SubClass(value1,value2) {
  SupperClass.call(this,value1);
  this.subValue = value2;
  this.func3 = function() {
    console.log(this.subValue);
  }
}

var Instance = new SubClass('sup','sub');
```

寄生组合继承就是将经过改良之后的寄生继承与构造函数继承方式组合，从而弥补寄生继承无法继承父类自有属性与方法的缺陷。

## 多态
