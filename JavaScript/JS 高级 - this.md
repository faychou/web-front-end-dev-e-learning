# this
因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。所以 this 的值并不是固定的指向。

## this 四种场景

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
