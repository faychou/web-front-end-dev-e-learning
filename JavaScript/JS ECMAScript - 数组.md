# JS ECMAScript - 数组
### filter
为数组中的每个元素调用一次 callback 函数，并利用所有使得" callback 返回 true 或 等价于 true 的值 的元素"创建一个新数组。也就是说 callback 返回 true 当作"过滤条件"函数，生产新的数组。

``` js
//语法
var new_array = arr.filter(callback[, thisArg]);

//例子
let arr = [12, 5, 8, 130, 44]
let filtered = arr.filter(n => n >= 10)
console.log(filtered) // [12, 130, 44]
```

#### 案例1:数组对象的键名/键值的搜索
``` js

let users = [
  { 'user': 'barney', 'age': 36, 'active': true },
  { 'user': 'fred',   'age': 40, 'active': false },
  { 'user': 'ared',   'age': 24, 'active': false },
  { 'user': 'ered',   'age': 80, 'active': false },
  { 'abc': 'ered',   'age': 80, 'active': false }
]
var filtered = users.filter(n => n.age===40 || n.age===24)
console.log(filtered)   // => [{user: "fred", age: 40, active: false}，{user: "ared", age: 24, active: false}]
```

#### 案例2:数组去重
``` js
let arr = [12, 5, 8, 8, 130, 44,130]
let filtered = arr.filter((item, idx, arr) => {
  return arr.indexOf(item) === idx;
})
console.log(filtered)
```

#### 案例3:数组中的空字符串删掉
``` js
let arr = ['A', '', 'B', null, undefined, 'C', '  ']
let filtered = arr.filter((item, idx, arr) => {
  return item && item.trim()
})
console.log(filtered) // ["A", "B", "C"]
```

#### 案例4:有条件筛选数组并生成新的数组
``` js
//方法一：filter +map
let arr = [{
  gender: 'man',
  name: 'john'
}, {
  gender: 'woman',
  name: 'mark'
}, {
  gender: 'man',
  name: 'jerry'
}]

// 筛选出性别为男性的名字集合
let newArr = arr.filter(n => n.gender === 'man').map(item => {
  return {
    name: item.name
  }
})
console.log(newArr)   //[{name: 'john'}, {name: 'jerry'}]

//方法二：for + continue
let forArr = []
  for (let i = 0; i < arr.length; i++) {
    if (arr[i].gender === 'woman') continue
    let temp = {
      name: arr[i].name
    }
    forArr.push(temp)
  }
console.log(forArr) // [{name: 'john'}, {name: 'jerry'}]
```
