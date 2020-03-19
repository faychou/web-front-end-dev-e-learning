# Map
由于传统的 JavaScript 对象只能用字符串当做键，给开发带来很大限制，ES6 增加 Map 数据结构，使得各种类型的值(包括对象)都可以作为键。

``` js
let a = new Map();
let b = {name: 'leo' };
a.set(b,'my name'); // 添加值
a.get(b);           // 获取值
a.size;      // 获取总数
a.has(b);    // 查询是否存在
a.delete(b); // 删除一个值
a.clear();   // 清空所有成员 无返回
```

如果对同一个键多次赋值，后面的值将覆盖前面的值。

``` js
let a = new Map();
a.set(1,'aaa').set(1,'bbb');
a.get(1); // 'bbb'
```

同样的值的两个实例，在 Map 结构中被视为两个键。

``` js
let a = new Map();
let a1 = ['aaa'];
let a2 = ['aaa'];
a.set(a1,111).set(a2,222);
a.get(a1); // 111
a.get(a2); // 222
```

应用：

``` js
// 使用 Map 来找到对应颜色的水果
const fruitColor = new Map()
  .set('red', ['apple', 'strawberry'])
  .set('yellow', ['banana', 'pineapple'])
  .set('purple', ['grape', 'plum']);

function test(color) {
  return fruitColor.get(color) || [];
}

// 将Map结构转成数组结构
let a = new Map([
    ['name','leo'],
    ['age',18]
])

let a1 = [...a.keys()];   // a1 => ["name", "age"]
let a2 = [...a.values()]; // a2 =>  ["leo", 18]
let a3 = [...a.entries()];// a3 => [['name','leo'], ['age',18]]

```

# Set
Set 数据结构类似数组，但所有成员的值唯一。使用 add 方法来添加新成员。

``` js
let a = new Set();
[1,2,2,1,3,4,5,4,5].forEach(x=>a.add(x));
for(let k of a){
    console.log(k)
};
// 1 2 3 4 5
```
一些简单应用：

``` js
let a = new Set([1,2,3,3,4]);
[...a]; // [1,2,3,4]
a.size; // 4

// 数组去重
[...new Set([1,2,3,4,4,4])];// [1,2,3,4]
```

> 注意：向 Set 中添加值的时候，不会类型转换，即 5 和 '5' 是不同的。

## 属性
### Set.prototype.constructor
构造函数，默认就是 Set 函数。

### Set.prototype.size
返回 Set 实例的成员总数。

## 方法
### add(value)
添加某个值，返回 Set 结构本身。

### delete(value)
删除某个值，返回一个布尔值，表示删除是否成功。

### has(value)
返回一个布尔值，表示该值是否为Set的成员。

### clear()
清除所有成员，没有返回值。

``` js
let a = new Set();
a.add(1).add(2); // a => Set(2) {1, 2}
a.has(2);        // true
a.has(3);        // false
a.delete(2);     // true  a => Set(1) {1}
a.clear();       // a => Set(0) {}
```
