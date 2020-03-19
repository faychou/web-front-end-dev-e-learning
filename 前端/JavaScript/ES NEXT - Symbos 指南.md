# Symbos 指南

有且只有 `strings` 和 `symbols` 才能作为 Object 的 keys。 

``` js
const symbol1 = Symbol();
const symbol2 = Symbol();

symbol1 === symbol2; // false

const obj = {};
obj[symbol1] = 'Hello';
obj[symbol2] = 'World';

obj[symbol1]; // 'Hello'
obj[symbol2]; // 'World'
```

`Symbol()` 接受一个字符串参数，这个字符串主要起到描述说明的作用，通常都是为了 debugging - 字符串会在调用 `symbols` 的 `toString()` 方法时展示。然而，即使两个`symbol`的描述字符串相等，那两个 `symbols` 也是不相等的。

``` js
const symbol1 = Symbol('my symbol');
const symbol2 = Symbol('my symbol');

symbol1 === symbol2; // false
console.log(symbol1); // 'Symbol(my symbol)'
```

有一种全局注册 `symbol` 的方法，在创建 `symbol` 的时候使用 `Symbol.for(String)` 创建，这个 `symbol` 会被注册到全局，并且按传入的字符串做区分。换句话说就是，如果你通过 `Symbol.for(String)` 创建了两个 `symbol`，并且传入的字符串是相等的，那么这两个 `symbol` 就是相等的。

 ``` js
const symbol1 = Symbol.for('test');
const symbol2 = Symbol.for('test');

symbol1 === symbol2; // true
console.log(symbol1); // 'Symbol(test)'
 ```

一般来说，除非有特殊情况才会使用这种方式去创建 `symbol`，否则不要使用，因为有可能会遇到命名冲突。 



由于两个 `symbols` 是不相等的，所以使用 `symbols` 来定义一些私有属性是非常方便的。（以前定义一些内部变量或者方法都会加上 _ 来表示私有）。`Symbols` 属性无法被  `Object,keys()` 遍历出来，因此，除非是显式导出了一个 `symbols`，或者使用 `Object.getOwnPropertySymbols()`函数，否则其他任何代码都无法访问该属性。

 ``` js
function getObj() {
  const symbol = Symbol('test');
  const obj = {};
  obj[symbol] = 'test';
  return obj;
}

const obj = getObj();

Object.keys(obj); // []

// Unless you explicitly have a reference to the symbol, you can't access the
// symbol property.
obj[Symbol('test')]; // undefined

// You can still get a reference to the symbol using `getOwnPropertySymbols()`
const [symbol] = Object.getOwnPropertySymbols(obj);
obj[symbol]; // 'test'
 ```



 

 

 