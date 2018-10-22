# Map
Map 是 ES2015 引入的新的对象类型，允许你存放键值对。

``` js
// 使用 Map 来找到对应颜色的水果
const fruitColor = new Map()
  .set('red', ['apple', 'strawberry'])
  .set('yellow', ['banana', 'pineapple'])
  .set('purple', ['grape', 'plum']);

function test(color) {
  return fruitColor.get(color) || [];
}
```
