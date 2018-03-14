# this
因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。而上下文取决于函数调用时的各种条件，this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式，所以 this 的值并不是固定的指向。

## this 绑定规则
### 1、默认绑定
纯粹的函数调用，函数内 this 指向全局对象（window / global）。

``` js
function foo() {
  console.log(this);
}

foo();  // window
```

> 注意：只有函数运行在非严格模式下，默认绑定才能绑定到全局对象。如果在严格模式下，就会绑定到 undefined。

### 2、隐式绑定

当函数引用有上下文对象时，隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象。

``` js
function foo() {
  console.log(this.a);
}

var obj = {
    a: 1,
    foo: foo
};

obj.foo(); // 1
```

### 3、显示绑定
``` js
function foo() {
  console.log(this.a);
}

var obj = {
  a:1
};

foo.call( obj ); // 1
```

### 4、作为构造函数调用
函数内的 this 指向通过构造函数生成的对象实例。

## this 绑定的优先级

　　(1) 函数是否在new中调用(new绑定)?如果是的话this绑定的是新创建的对象。

　　　　var bar = new foo()

　　(2) 函数是否通过call、apply(显式绑定)或者硬绑定调用?如果是的话，this绑定的是 指定的对象。

　　　　var bar = foo.call(obj2)

　　(3) 函数是否在某个上下文对象中调用(隐式绑定)?如果是的话，this 绑定的是那个上 下文对象。

　　　　var bar = obj1.foo()

　　(4).如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到 全局对象。

　　　　var bar = foo()

## 修改 this 指向
JavaScript 中通过 call 、 apply 或者 bind 用来代替另一个对象调用一个方法，将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。简单的说就是改变函数执行的上下文。

### call
``` js
call(obj,arg1,arg2,arg3);
```

* 参数一：对象，可以是null；
* 参数二：可选。

参数以逗号分开进行传值，可以是任何类型。

### apply
``` js
apply(obj,[arg1,arg2,arg3]);
```

* 参数一：对象；
* 参数二：数组或者 arguments 对象。

#### apply 与 call 区别：
* 二者的作用完全一样，只是接受参数的方式不太一样。

JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数是明确知道数量时，用 call，而不确定的时候，用 apply，然后把参数 push 进数组传递进去。

当参数数量不确定时，函数内部也可以通过 arguments 这个数组来便利所有的参数。

### bind
