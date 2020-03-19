# 对象
``` js
var obj = {
  a:1,
  b:2
}
```

属性名永远是字符串，如果使用 string 以外的其他值作为属性名，也会被转换为一个字符串。

### 属性操作
``` js
对象.属性

//或者
对象[属性]
```

## 对象方法
### obj.hasOwnProperty(key)
``` js
var myObject = {
    a:2
};
("a" in myObject); // true
("b" in myObject); // false
myObject.hasOwnProperty( "a" ); // true
myObject.hasOwnProperty( "b" ); // false
```

in 操作符会检查属性是否在对象及其 Prototype 原型链中。相比之下， hasOwnProperty() 只会检查属性是否在对象中，不会检查 Prototype 链。

> 注意：虽然数组也是对象，但是 in 操作符在数组中检查的是数组的属性名，而不是值。

``` js
4 in [2, 4, 6] // false
```

### Object.keys(key)
获取对象所有的属性，会返回一个数组，包含所有可枚举属性。

### Object.assign(target, [...source])
遍历一个或多个源对象的所有可枚举的自有键并把它们复制 (使用 = 操作符赋值) 到目标对象，最后返回目标对象。

### 案例：复制对象
``` js
var newObj = JSON.parse( JSON.stringify(someObj));

//或者
var newObj = Object.assign({}, someObj);
```

### Object.getOwnPropertyDescriptor(obj, key)
获取属性。

``` js
Object.getOwnPropertyDescriptor(myObject, key)
var myObject = {
    a:2
};
Object.getOwnPropertyDescriptor(myObject, "a");
// {
//     value: 2,
//     writable: true,
//     enumerable: true,
//     configurable: true
// }
```

### Object.defineProperty(obj, key, {...})
Object.defineProperty() 方法直接定义一个对象的属性，或者修改对象当中一个已经存在的属性，并返回这个对象。

``` js
// Object.defineProperty(myObject, key, {...})

var myObject = {};
Object.defineProperty( myObject, "a", {
    value: 2,
    writable: true,
    configurable: true,
    enumerable: true,
});
myObject.a; // 2

//注意：即便属性是 configurable:false，我们还是可以 把 writable 的状态由 true 改为 false，但是无法由 false 改为 true。
```

## 深拷贝和浅拷贝
深拷贝一定都是按值传递，浅拷贝一定有按址传递。

### 浅拷贝
浅拷贝: 以赋值的形式拷贝引用对象，仍指向同一个地址，修改时原对象也会受到影响。

* Object.assign
* 展开运算符(...)
* concat()
* slice()

``` js
var obj1 = {
  a:1
}

function Copy(o) {
  var c = {}; // 在堆内存中新生成一个空对象，这样来杜绝关联
  for (var i in o) { 
    c[i] = o[i];
  }
  return c;
}

obj1.b = ['1','2'];
var obj2 = Copy(a);
obj2.c = 3;
alert(obj2.a);     // 1
alert(obj2.c);    // 3
alert(obj1.c);    // undefined
```

由于 `obj1.b` 是个引用类型，所以 copy 的时候 `obj1.b` 是按引用地址传递的，就会导致修改 `obj1.a` 的值时，`obj2.b` 的值也会变化。因为 obj1 和 obj2 会有关联，所以说这次拷贝是一次浅拷贝。

### 深拷贝
完全拷贝一个新对象，修改时原对象不再受到任何影响。

* JSON.parse(JSON.stringify(obj)): 性能最快
  * 具有循环引用的对象时，报错
  * 当值为函数、undefined、或symbol时，无法拷贝

* 递归进行逐一赋值


``` js
var obj1 = {
  a:1
}

// 通过递归进行深拷贝
function Copy(a,b) {
  var c = c || {};
  for (var i in a) { 
    if(typeof a[i] == 'object') {
      if(a[i].constructor == Array) {
        copy(a[i], c[i])
      } else if(a[i].constructor == Object) {
        copy(a[i], c[i])
      }
    } else {
      c[i] = a[i]
    }
  }
  return c;
}

obj1.b = ['1','1'];
var obj2 = Copy(a);
obj2.b = ['2','2'];
alert(obj2.b); // 1,1
alert(obj2.b); // 2,2
```

