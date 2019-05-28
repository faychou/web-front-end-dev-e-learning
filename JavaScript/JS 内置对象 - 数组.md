# 数组
首先数组也是对象。

## 数组方法
### - 会改变原数组 -
### push()
向数组的末尾添加一个或多个元素，并返回新的长度。

``` js
let  a =  [1,2,3];
let item = a.push(4);  // 4
console.log(a); // [1,2,3,4]
a.push(5,6,7);
console.log(a); // [1,2,3,4,5,6,7]
```

### pop()
删除一个数组中的最后的一个元素，并且返回这个元素。

``` js
let  a =  [1,2,3];
let item = a.pop();  // 3
console.log(a); // [1,2]
```

### shift()
删除数组的第一个元素，并返回这个元素。

``` js
let  a =  [1,2,3];
let item = a.shift();  // 1
console.log(a); // [2,3]
```

### unshift()
向数组的开头添加一个或更多元素，并返回新的长度。

``` js
let  a =  [1,2,3];
let item = a.unshift('a');  // 4
console.log(a); // ['a',1,2,3]
```

### reverse()
用于颠倒数组中元素的顺序，返回的是颠倒后的数组。

``` js
let  a =  [1,2,3];
a.reverse();  
console.log(a); // [3,2,1]
```

### splice()
向数组中添加或者删除项目，然后返回被删除的项目。

``` js
array.splice(index,length,item1,.....,itemX);
```

* index：必需。整数，规定开始的位置，使用负数可从数组结尾处规定位置；
* length：必需。要删除的项目数量。如果设置为 0，则不会删除项目；
* item1, …, itemX： 可选。在删除的位置向数组添加的新项目。

``` js
// 删除
let a = [1, 2, 3, 4, 5, 6, 7];
let item = a.splice(0, 3); // [1,2,3]
console.log(a); // [4,5,6,7]

// 插入
item = a.splice(2,0,1,2);
console.log(a); // [4,5,1,2,6,7]

// 删除并添加
let b = [1, 2, 3, 4, 5, 6, 7];
let item = b.splice(1,3,'a','b'); // [2,3,4]
console.log(b); // [1,'a','b',5,6,7]
```

### sort()
对数组元素进行排序，并返回这个数组。

默认情况下 sort() 方法按字母升序，如果不是元素不是字符串的话，会调用 toString() 方法将元素转化为字符串的 Unicode(万国码)位点，然后再比较字符。

``` js
var a = ["Banana", "Orange", "Apple", "Mango"];
a.sort(); // ["Apple","Banana","Mango","Orange"]

// 数字排序的时候 因为会转换成 Unicode 字符串，所以会出现"10"在"3"的前面
var    a = [10, 1, 3, 20,25,8];
console.log(a.sort()) // [1,10,20,25,3,8];
```

所以针对这种情况，可以使用带参数的 sort() 方法，该参数是一个比较函数，并且有两个默认参数，这两个参数是数组中两个要比较的元素，通常我们用 a 和 b 接收两个将要比较的元素：

* 若比较函数返回值 < 0，那么 a 将排到 b 的前面;
* 若比较函数返回值 = 0，那么 a 和 b 相对位置不变；
* 若比较函数返回值 > 0，那么 b 排在 a 将的前面；

``` js
var array =  [10, 1, 3, 4,20,4,25,8];  
array.sort(function(a,b) {
  // 升序 a - b < 0   a将排到b的前面，按照a的大小来排序的 
  return a-b;
});
console.log(array); // [1,3,4,4,8,10,20,25];

array.sort(function(a,b) {
  // 降序 b - a > 0 被减数 b 在减数 a 的前面
  return b-a;
});
console.log(array); // [25,20,10,8,4,4,3,1];
```

#### 案例1：数组多条件排序
``` js
var array = [{id:10,age:2},{id:5,age:4},{id:6,age:10},{id:9,age:6},{id:2,age:8},{id:10,age:9}];
    array.sort(function(a,b){
        if(a.id === b.id){// 如果id的值相等，按照age的值降序
            return b.age - a.age
        }else{ // 如果id的值不相等，按照id的值升序
            return a.id - b.id
        }
    })
 // [{"id":2,"age":8},{"id":5,"age":4},{"id":6,"age":10},{"id":9,"age":6},{"id":10,"age":9},{"id":10,"age":2}] 
```

扩展：

``` js
var array = [{name:'Koro1'},{name:'Koro1'},{name:'OB'},{name:'Koro1'},{name:'OB'},{name:'OB'}];
array.sort(function(a,b){
    if(a.name === 'Koro1'){// 如果name是'Koro1' 返回-1 ，-1<0 a排在b的前面
        return -1
    }else{ // 如果不是的话，a排在b的后面
      return 1
    }
})
// [{"name":"Koro1"},{"name":"Koro1"},{"name":"Koro1"},{"name":"OB"},{"name":"OB"},{"name":"OB"}] 
```

### fill() 
``` js
array.fill(value, start, end);
```

用于将一个固定值替换数组的元素，返回新的被替换的数组。

* value	必需。填充的值。
* start	可选。开始填充位置。
* end	可选。停止填充位置(不包含) (默认为 array.length)

``` js
var values = [0, 1, 5, 10, 15];
values.fill(2);
console.log(values);  // [ 2, 2, 2, 2, 2 ]

values = [0, 1, 5, 10, 15];
values.fill(2,3,4);
console.log(values);  // [ 0, 1, 5, 2, 15 ]
```

### copyWithin() 
``` js
array.copyWithin(target, start, end)
```

选择数组的某个下标，从该位置开始复制数组元素，默认从0开始复制。也可以指定要复制的元素范围，返回新复制的数组。

``` js
var fruits = [1,2,3,4,5,6];
fruits.copyWithin(1);  // [ 1, 1, 2, 3, 4, 5 ]   从下标为1的元素开始，复制数组

fruits.copyWithin(3, 0, 3);  // [ 1, 2, 3, 1, 2, 3 ]  从下标为3的元素开始，复制数组坐标为0到2的数组
```

### - 不会改变原属组 -
### cancat()
用于合并两个或多个数组，返回一个新数组。

``` js
let a = [1, 2, 3];
 let b = [4, 5, 6];
 //连接两个数组
 let newVal=a.concat(b); // [1,2,3,4,5,6]
 // 连接三个数组
 let c = [7, 8, 9]
 let newVal2 = a.concat(b, c); // [1,2,3,4,5,6,7,8,9]
```

### join() 
用于把数组中的所有元素通过指定的分隔符进行分隔放入一个字符串，返回生成的字符串，不会不改变原数组。

``` js
let a = ['hello','world'];
let str = a.join(); // 'hello,world'
let str = a.join('-'); // 'hello-world'

let a= [['OBKoro1','23'],'test'];
let str1=a.join(); // OBKoro1,23,test
let b= [{name:'OBKoro1',age:'23'},'test'];
let str2 = b.join(); // [object Object],test
```

### slice()
``` js
array.slice(start, end);
```

返回一个新的数组，包含从 start 位置到 end （不包括该元素）位置的元素。如果只有一个参数，返回从该参数指定位置到当前数组末尾的所有项；如果参数为负数，规定从数组尾部开始算起的位置。也就是说，-1 指最后一个元素，-2 指倒数第二个元素，以此类推。

``` js
var arr1 = [1,2,3,4,5,6];
var arr2 = arr1.slice(1); // [ 2, 3, 4, 5, 6 ]
var arr3 = arr1.slice(2,4); // [ 3, 4 ]
var arr4 = arr1.slice(-4,-2);  // [ 3, 4 ] 
```

### indexOf() 
返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。

``` js
let a=['a',1,2,3,'b']
console.log(a.indexOf('a'));  // 0 
console.log(a.indexOf('c'));  // -1 
```

lastIndexOf() 方法从数组末尾开始向前查找。

### forEach()
遍历数组中的每个元素。这个方法没有返回值，本质上与使用 for 循环迭代数组一样。

``` js
array.forEach(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

> 注意：无法中途退出循环，只能用 return 退出本次回调，进行下一次回调。

``` js
var items = [1, 2, 4, 7, 3];
var copy = [];

items.forEach(function(item,index){
  copy.push(item*index);
})

console.log(items);  // [ 1, 2, 4, 7, 3 ]
console.log(copy);  // [ 0, 2, 8, 21, 12 ]
```

### map()
遍历数组中的每个元素并进行处理，然后返回新的数组。

``` js
let newArray = arr.map(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

``` js
// 批量修改数组的值
let arr = ['1','2','3','4'];
let result = arr.map(function (value, index, array) {
  return value + 'a'
});
console.log(result, arr); 
// ["1a","2a","3a","4a"] ["1","2","3","4"]

// 批量修改数组中每个对象的属性值 
var arr = [{
  name: "lily",
  age: 10
}, {
  name: "lucy",
  age: 20
}, {
  name: "jack",
  age: 30
}];

arr = arr.map(function(item, index, arr) {
    item.age++;
    return item;
})
console.log(arr); // [{"name":"lily","age":11},{"name":"lucy","age":21},{"name":"jack","age":31}]
```

### every()
用于检测数组所有元素是否都符合函数定义的条件。如果数组中检测到有一个元素不满足，则整个表达式返回 false，且剩余的元素不会再进行检测。如果所有元素都满足条件，则返回 true。

``` js
array.every(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

``` js
[12, 5, 8, 130, 44].every(x => x >= 10); // false
[12, 54, 18, 130, 44].every(x => x >= 10); // true

function allCheckedHandle() {
  // 条件：（简短形式）所有的选框都必须选中
  const isAllChecked = checkboxes.every(f => f.checked === true);

  console.log(isAllChecked);
}
```

### some()
检查数组中是否有满足条件的元素。如果有一个元素满足条件，则表达式返回 true, 剩余的元素不会再执行检测。如果没有满足条件的元素，则返回 false。

``` js
array.some(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

``` js
function anyCheckedHandle() {
  // 条件：（简短形式）至少有一个选框没选中
  const isAnyChecked = checkboxes.some(f => f.checked === true);

  console.log(isAnyChecked);
}
```

### Array.of()
返回由所有参数值组成的数组，如果没有参数，就返回一个空数组。详见下面的代码：

``` js
var a = new Array(); // [] 
var a = new Array(3); // [undefined,undefined,undefined]
var a = new Array(3,11,8); // [ 3,11,8 ]

let a = Array.of(3, 11, 8); // [3,11,8]
let a = Array.of(3); // [3]
```

主要用来解决上述构造器因参数个数不同，导致的行为有差异的问题。

### from() 
``` js
Array.from(object, mapFunction, thisValue);
```

用于将两类对象转为真正的数组（不改变原对象，返回新的数组）。

* 参数一(必需):要转化为真正数组的对象。

* 参数二(可选): 类似数组的 map 方法，对每个元素进行处理，将处理后的值放入返回的数组。

* 参数三(可选): 用来绑定 this。

``` js
let obj = {0: 'a', 1: 'b', 2:'c', length: 3};
let arr = Array.from(obj); // ['a','b','c'];

let arr = Array.from('hello'); // ['h','e','l','l']

// 还可以结合 new Set() 进行数组的去重
let arr = Array.from(new Set(['a','b','a'])); // ['a','b']
```

### filter()
为数组中的每个元素调用一次 callback 函数，利用 callback 返回 true 当作"过滤条件",返回新的数组。

``` js
let newArray = arr.filter(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

``` js
// demo
let arr = [12, 5, 8, 130, 44];
let filtered = arr.filter(n => n >= 10);
console.log(filtered); // [12, 130, 44]

// 使用 Array filter 来找到对应颜色的水果
const fruits = [
  { name: 'apple', color: 'red' }, 
  { name: 'strawberry', color: 'red' }, 
  { name: 'banana', color: 'yellow' }, 
  { name: 'pineapple', color: 'yellow' }, 
  { name: 'grape', color: 'purple' }, 
  { name: 'plum', color: 'purple' }
];

function test(color) {
  return fruits.filter(f => f.color == color);
}
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
//方法1
function uniqueArr(arr) {
  var newArr = [];
  for(var i = 0; i < arr.length; i++) {
    if(newArr.indexOf(arr[i]) === -1) {
      newArr.push(arr[i]);
    }
  }
  return newArr;
}

// 方法2
function uniqueArr(arr) {
  var newArr = [];
  for (var i = 0; i < arr.length; i++) {
    for (var j = i+1; j < arr.length; j++) {
      if(arr[i] === arr[j]) {
        ++i;
      }
    }
    newArr.push(arr[i]);
  }
  return newArr;
}

// 方法3：es5简化版
Array.prototype. uniqueArr = function() {
  return this.filter((v, i) => this.indexOf(v) === i)
}
// es6简化版
Array.prototype. uniqueArr = function() {
  return Array.from(new Set(this));
}
// 或
Array.prototype. uniqueArr = function() {
  return [...new Set(this)];
}

// 方法4：对象存放，哈希算法(映射)判断（最优）
Array.prototype. uniqueArr = function() {
  // n为hash表，r为临时数组
  var n = {}, r = [];
  for (var i = 0; i < this.length; i++) {
    // 如果hash表中没有当前项
    if (!n[this[i]]) {
      // 存入hash表
      n[this[i]] = true;
      // 把当前数组的当前项push到临时数组里面
      r.push(this[i]); 
    }
  }
  return r;
}
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

### find()
用于找出第一个符合条件的数组成员，找到后停止搜索并返回该成员，如果没有符合条件的成员，则返回 undefined。

``` js
let newArray = arr.find(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

``` js
let a = [1, 4, -5, 10].find((n) => n < 0); // 返回元素-5
let b = [1, 4, -5, 10,NaN].find((n) => Object.is(NaN, n));  // 返回元素 NaN
```

filter() 和 find() 方法的区别：

* filter() 方法是对数组的每一项都进行检查，最后返回结果为 true 的数组；而 find() 方法当找到符合的元素时，立刻返回该元素，之后的元素不再进行检查；

* filter() 方法如果没有找到符合的元素返回空的数组；而 find() 方法没有找到符合的元素则返回 undefined。

### reduce()
为数组提供累加器，合并为一个值。

``` js
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
```

* total 必须,初始值, 或者上一次调用回调返回的值；
* currentValue 必须,数组当前元素的值；
* currentIndex 可选,当前元素的索引值
* arr 可选,数组对象本身；
* initialValue 可选，指定第一次回调的第一个参数。
  * 如果 initialValue 在调用 reduce 时被提供，那么第一个 total 将等于 initialValue，此时 currentValue 等于数组中的第一个值；
  
  * 如果 initialValue 未被提供，那么 total 等于数组中的第一个值，currentValue 等于数组中的第二个值。此时如果数组为空，那么将抛出 TypeError。
  
  * 如果数组仅有一个元素，并且没有提供 initialValue，或提供了 initialValue 但数组为空，那么回调不会被执行，数组的唯一值将被返回。

``` js
// 数组求和 
let sum = [0, 1, 2, 3].reduce(function (a, b) {
  return a + b;
}, 0); // 6

// 求该同学的总成绩
var result = [
    {
        subject: 'math',
        score: 88
    },
    {
        subject: 'chinese',
        score: 95
    },
    {
        subject: 'english',
        score: 80
    }
];
var sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, 0);

// 将二维数组转化为一维 将数组元素展开
let flattened = [[0, 1], [2, 3], [4, 5]].reduce(
  (a, b) => a.concat(b),
  []
); // [0, 1, 2, 3, 4, 5]
 
// 判断一串字符串中每个字母出现的次数
var arrString = 'abcdaabc';

arrString.split('').reduce(function(res, cur) {
  res[cur] ? res[cur] ++ : res[cur] = 1
  return res;
}, {})；  // 通过第二参数设置叠加结果的类型初始值
```

reduceRight() 则从数组的最后一项开始，向前遍历到第一项。

### Array.includes
``` js
// 条件语句
function test(fruit) {
  if (fruit == 'apple' || fruit == 'strawberry') {
    console.log('red');
  }
}

// 改造
function test(fruit) {
  // 把条件提取到数组中
  const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries'];

  if (redFruits.includes(fruit)) {
    console.log('red');
  }
}
```

## 数组应用

### 求数组最大值与最小值
``` js
var a = [1,2,3,5,6];
alert(Math.max.apply(null, a)); //最大值
alert(Math.min.apply(null, a)); //最小值
```

多维数组：

``` js
var a = [1,2,3,[5,6],[1,4,8]];
var ta = a.join(",").split(","); //转化为一维数组
alert(Math.max.apply(null,ta)); //最大值
alert(Math.min.apply(null,ta)); //最小值
```

扩展为数组的原型方法：

``` js
Array.prototype.max = Array.prototype.max || function() {
  return Math.max.apply({},this)
};

Array.prototype.min = Array.prototype.min || function() {
  return Math.min.apply({},this)
};

[1, 2, 3].max() // => 3
[1, 2, 3].min() // => 1
```

### 从数组中随机选取元素
从数组中随机取出一个元素：

``` js
var items = ['1','2','4','5','6'];
var item = items[Math.floor(Math.random()*items.length)];
```

从数组中随机取出几个元素：

``` js
function getRandomArrayElements(arr, count) {
  var shuffled = arr.slice(0), 
  i = arr.length, 
  min = i - count, 
  temp, 
  index;
  
  while (i-- > min) {
    index = Math.floor((i + 1) * Math.random());
    temp = shuffled[index];
    shuffled[index] = shuffled[i];
    shuffled[i] = temp;
  }
  return shuffled.slice(min);
}

var items = ['1','2','4','5','6'];
console.log( getRandomArrayElements(items, 3));
```

### 判断一个对象是否为数组
不能用instanceof 和 constructor来判断。

``` js
function isArray(obj){
  return Object.prototype.toString.call(obj) == "[object Array]";
}
```

### 伪数组转换为标准数据
``` js
// 方法一
Array.prototype.slice.call(arguments);

// 方法二
Array.from(arguments);
```

### 数组去重复
``` js
// 方法一：双重遍历
Array.prototype.unique = Array.prototype.unique || function(arr) {
  for(var i = 0; i < this.length; ++i) {
    for(var j = i + 1; j < this.length; ++j) {
      if(this[i] === this[j]) {
        this.splice(j, 1);
      }
    }
  }
  return this;
};

// 方法二：相邻元素去重
function unique(arr) {
  let Arr = arr.sort();
  let b = [];
  for(let i = 0; i < Arr.length; i++) {
    if(Arr[i] !== Arr[i+1]) {
      b.push(Arr[i]);
    }
  }
  return b;
}

// 方法三：存在唯一性
function unique(arr) {
  let b = [];
  let hash = {};
  for(let i = 0; i < arr.length; i++) {
    if(!hash[JSON.stringify(arr[i])]) {
      hash[JSON.stringify(arr[i])] = true;
      b.push(arr[i]);
    }
  }
  return b;
}

// 方法四：
function unique(arr) {
  let b = [];
  for(let i = 0; i < arr.length; i++) {
    if(b.indexOf(arr[i]) == -1) {
      b.push(arr[i]);
    }
  }
  return b;
}

// 方法五：
function unique(arr) {
  let b=[];
  arr.forEach(res => {
    if(b.indexOf(res) == -1) {
      b.push(res);
    }
  });
  return b
}

// 方法六：
function unique(arr) {
  let b = new Set(arr);
  let c = Array.from(b);
  return c;
}
```

### 取两个数组交集
``` js
function getArrIntersection(arr1, arr2) {
  let newArr = [];
  for (let i = 0; i < arr2.length; i++) {
    for (let j = 0; j < arr1.length; j++) {
      if(arr1[j] === arr2[i]){
        newArr.push(arr1[j]);
      }
    }
  }
  return newArr;
}
```

### 取出两个数组的不同元素
``` js
function getArrDiff(arr1, arr2) {
  return arr1.concat(arr2).filter(function(v, i, arr) {
    return arr.indexOf(v) === arr.lastIndexOf(v);
  });
}
```

### 数组扁平化
``` js
// 使用展开运算符扁平二维数组
[].concat(...arr);

function flattenDepth(array, depth=1) {
  let result = [];
  array.forEach (item => {
    let d = depth;
    if(Array.isArray(item) && d > 0){
      result.push(...(flattenDepth(item, --d)))
    } else {
      result.push(item);
    }
  })
  return result;
}
console.log(flattenDepth([1,[2,[3,[4]],5]]))
console.log(flattenDepth([1,[2,[3,[4]],5]],2))
console.log(flattenDepth([1,[2,[3,[4]],5]],3))

// or
function flattenArray(arr) {
  const flattened = [].concat(...arr);
  return flattened.some(item => Array.isArray(item)) ? 
    flattenArray(flattened) : flattened;
}
```

### 条件语句的优化
``` js
// bad
function test(color) {
  switch (color) {
    case 'red':
      return ['apple', 'strawberry'];
    case 'yellow':
      return ['banana', 'pineapple'];
    case 'purple':
      return ['grape', 'plum'];
    default:
      return [];
  }
}

// good
const fruitColor = {
  red: ['apple', 'strawberry'],
  yellow: ['banana', 'pineapple'],
  purple: ['grape', 'plum']
};

function test(color) {
  return fruitColor[color] || [];
}

// better
const fruitColor = new Map()
  .set('red', ['apple', 'strawberry'])
  .set('yellow', ['banana', 'pineapple'])
  .set('purple', ['grape', 'plum']);

function test(color) {
  return fruitColor.get(color) || [];
}
```

### 日期格式化
``` js
Date.prototype.toString=function(format,loc) {
  var time={};
  time.Year=this.getFullYear();
  time.TYear=(""+time.Year).substr(2);
  time.Month=this.getMonth()+1;
  time.TMonth=time.Month<10?"0"+time.Month:time.Month;
  time.Day=this.getDate();
  time.TDay=time.Day<10?"0"+time.Day:time.Day;
  time.Hour=this.getHours();
  time.THour=time.Hour<10?"0"+time.Hour:time.Hour;
  time.hour=time.Hour<13?time.Hour:time.Hour-12;
  time.Thour=time.hour<10?"0"+time.hour:time.hour;
  time.Minute=this.getMinutes();
  time.TMinute=time.Minute<10?"0"+time.Minute:time.Minute;
  time.Second=this.getSeconds();
  time.TSecond=time.Second<10?"0"+time.Second:time.Second;
  time.Millisecond=this.getMilliseconds();
  time.Week=this.getDay();

  var MMMArrEn=["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];
  var MMMArr=["一月","二月","三月","四月","五月","六月","七月","八月","九月","十月","十一月","十二月"];
  var WeekArrEn=["Sun","Mon","Tue","Web","Thu","Fri","Sat"];
  var WeekArr=["星期日","星期一","星期二","星期三","星期四","星期五","星期六"];
  var oNumber = time.Millisecond / 1000;

  if(format! = undefined && format.replace(/\s/g,"").length > 0) {
    if(loc != undefined && loc == "en") {
      MMMArr = MMMArrEn.slice(0);
      WeekArr = WeekArrEn.slice(0);
    }
    format = format
      .replace(/yyyy/ig,time.Year)
      .replace(/yyy/ig,time.Year)
      .replace(/yy/ig,time.TYear)
      .replace(/y/ig,time.TYear)
      .replace(/MMM/g,MMMArr[time.Month-1])
      .replace(/MM/g,time.TMonth)
      .replace(/M/g,time.Month)
      .replace(/dd/ig,time.TDay)
      .replace(/d/ig,time.Day)
      .replace(/HH/g,time.THour)
      .replace(/H/g,time.Hour)
      .replace(/hh/g,time.Thour)
      .replace(/h/g,time.hour)
      .replace(/mm/g,time.TMinute)
      .replace(/m/g,time.Minute)
      .replace(/ss/ig,time.TSecond)
      .replace(/s/ig,time.Second)
      .replace(/fff/ig,time.Millisecond)
      .replace(/ff/ig,oNumber.toFixed(2)*100)
      .replace(/f/ig,oNumber.toFixed(1)*10)
      .replace(/EEE/g,WeekArr[time.Week]);
    } else {
      format=time.Year+"-"+time.Month+"-"+time.Day+" "+time.Hour+":"+time.Minute+":"+time.Second;
  }
  return format;
}

var d=new Date();
console.log(d.toString());    //2012-7-27 9:26:52
console.log(d.toString(""));    //2012-7-27 9:26:52
console.log(d.toString("yyyy-MM-dd HH:mm:ss"));    //2012-07-27 09:26:52
console.log(d.toString("yyyy年MM月dd日 HH:mm:ss"));    //2012年07月27日 09:26:52
console.log(d.toString("yyyy-MM-dd HH:mm:ss fff"));    //2012-07-27 09:26:52 237
console.log(d.toString("yyyy年 MMM dd EEE"));    //2012年 七月 27 星期五
console.log(d.toString("yyyy MMM dd EEE","en"));    //2012 Jul 27 Fri
```

### JavaScript 判断浏览器类型及主版本
``` js
function getBrowserInfo() {
  var Sys = {};
  var ua = navigator.userAgent.toLowerCase();

  if (window.ActiveXObject) {
    Sys.b="ie";
    Sys.v =parseInt(ua.match(/msie ([\d.]+)/)[1]);
  } else if (document.getBoxObjectFor) {
    Sys.b="firefox";
    Sys.v =parseInt(ua.match(/firefox\/([\d.]+)/)[1]);
  } else if (window.MessageEvent && !document.getBoxObjectFor) {
    Sys.b="chrome";
    ys.v == parseInt(ua.match(/chrome\/([\d.]+)/)[1]);
  } else if (window.opera){
    Sys.b="opera";
    Sys.v == parseInt(ua.match(/opera.([\d.]+)/)[1]);
  } else if (window.openDatabase){
    Sys.b="safari";
    Sys.v == parseInt(ua.match(/version\/([\d.]+)/)[1]);
  }
  return Sys;
}
```
