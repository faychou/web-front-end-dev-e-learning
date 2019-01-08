# this
因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。而上下文取决于函数调用时的各种条件，this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式，所以 this 的值并不是固定的指向。

总之一句话：JavaScript 的 this 总是指向一个对象，而具体指向哪个对象是在运行时基于函数的执行环境动态绑定的，而非函数被声明时的环境。

## this 绑定
基本可以分成以下四种情况：

### 1、作为普通函数调用
纯粹的函数调用，函数内 this 指向全局对象（window / global）。

``` js
function foo() {
  console.log(this);
}

foo();  // window
```

> 注意：只有函数运行在非严格模式下，默认绑定才能绑定到全局对象。如果在严格模式下，就会绑定到 undefined。

### 2、作为对象的方法调用
当函数引用有上下文对象时，会把函数调用中的 this 绑定到这个上下文对象。

``` js
function getName() {
  console.log(this.name);
}

var obj = {
  name: 'fay',
  getName: getName
};

obj.getName(); // fay
```

注意以下的区别：

``` js
window.name = 'globalName';
var obj = { 
  name: 'fay',
  getName: function() { 
    console.log(this.name);
  } 
};
var getName = obj.getName; 
console.log( getName() ); // globalName
```

### 3、作为构造函数调用
当用 new 运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的 this 就指向返回的这个对象。

``` js
function Person() { 
  this.name = 'fay';
};

var obj = new Person();
console.log( obj.name );    // fay
```

如果构造器显式地返回了一个 object 类型的对象，那么此次运算结果最终会返回这个对象，而不是我们之前期待的 this：

``` js
function Person() {
  this.name = 'fay';
  return { // 显式地返回一个对象
    name: 'jack' 
  }
};
var obj = new Person();
console.log( obj.name ); // jack
```

### 4、显示绑定
Function.prototype.call 或Function.prototype.apply 可以动态地 改变传入函数的 this。

``` js
function getName() {
  console.log(this.name);
}

var obj = {
  name: 'fay'
};

getName(); // undefined
getName.call( obj ); // fay
```

## this 绑定的优先级
1、函数是否通过 new 调用?如果是的话 this 绑定的是新创建的对象。

``` js
var bar = new foo();
```

2、函数是否通过 call、apply 调用?如果是的话，this 绑定的是指定的对象。

``` js
var bar = foo.call(obj2);
```

3、函数是否在某个上下文对象中调用?如果是的话，this 绑定的是那个上下文对象。

``` js
var bar = obj1.foo();
```

4、如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到 全局对象。

``` js
var bar = foo();
```

## 修改 this 指向
JavaScript 中通过 call 、 apply 或者 bind 用来代替另一个对象调用一个方法，将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。简单的说就是改变函数执行的上下文。

### call
``` js
// 语法
call(obj,arg1,arg2,arg3);

// 例子：
function func( a, b, c ) {
  alert ( [ a, b, c ] ); 
};
func.call( null, 1, 2, 3 ); // 输出 [ 1, 2, 3 ]
```

* 参数一：对象，可以是 null；
* 参数二：可选，传递给函数的参数，多个参数以逗号分开，可以是任何类型。

### apply
``` js
// 语法
apply(obj,[arg1,arg2,arg3]);

// 例子
function func( a, b, c ) {
  alert ( [ a, b, c ] ); 
};
func.apply( null, [ 1, 2, 3 ] ); // 输出 [ 1, 2, 3 ]
```

* 参数一：对象；
* 参数二：数组或者 arguments 对象。

> apply 与 call 区别：二者的作用完全一样，只是在于传入参数形式的不同。

JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数是明确知道数量时，用 call，而不确定的时候，用 apply，然后把参数 push 进数组传递进去。

当参数数量不确定时，函数内部也可以通过 arguments 对象来遍历所有的参数。

### bind
