# 数组
首先数组也是对象。

## 数组方法
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

### push()
向数组的末尾添加一个或多个元素，并返回新的长度。

``` js
let  a =  [1,2,3];
let item = a.push(4);  // 4
console.log(a); // [1,2,3,4]
a.push(5,6,7);
console.log(a); // [1,2,3,4,5,6,7]
```

### unshift()
向数组的开头添加一个或更多元素，并返回新的长度。

``` js
let  a =  [1,2,3];
let item = a.unshift('a');  // 4
console.log(a); // ['a',1,2,3]
```

### reverse()
用于颠倒数组中元素的顺序。

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

// 数字排序的时候 因为转换成 Unicode 字符串
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

### indexOf() 
返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。

``` js
let a=['a',1,2,3,'b']
console.log(a.indexOf('a'));  // 0 
console.log(a.indexOf('c'));  // -1 
```

### forEach()
遍历数组中的每个元素。

``` js
array.forEach(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。


> 注意：无法中途退出循环，只能用 return 退出本次回调，进行下一次回调。

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
let arr = ['1','2','3','4'];
let result = arr.map(function (value, index, array) {
  return value + 'a'
});
console.log(result, arr); 
// ["1a","2a","3a","4a"] ["1","2","3","4"]
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

### Arrar.from() 
用于将两类对象转为真正的数组（不改变原对象，返回新的数组）。

* 参数一(必需):要转化为真正数组的对象。

* 参数二(可选): 类似数组的 map 方法，对每个元素进行处理，将处理后的值放入返回的数组。

* 参数三(可选): 用来绑定 this。

``` js
let obj = {0: 'a', 1: 'b', 2:'c', length: 3};
let arr = Array.from(obj); // ['a','b','c'];
let arr = Array.from('hello'); // ['h','e','l','l']
let arr = Array.from(new Set(['a','b'])); // ['a','b']
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
let arr = [12, 5, 8, 130, 44];
let filtered = arr.filter(n => n >= 10);
console.log(filtered); // [12, 130, 44]
```

``` js
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

### find()
用于找出第一个符合条件的数组成员，并返回该成员，如果没有符合条件的成员，则返回 undefined。

``` js
let newArray = arr.find(function(currentValue, index, arr), thisArg)
```

* currentValue 必须,数组当前元素的值；
* index 可选, 当前元素的索引值；
* arr 可选,数组对象本身；
* thisArg 可选，当执行回调函数时 this 绑定对象的值，默认值为 undefined。

``` js
let a = [1, 4, -5, 10].find((n) => n < 0); // 返回元素-5
let b = [1, 4, -5, 10,NaN].find((n) => Object.is(NaN, n));  // 返回元素NaN
```

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
}, 0);
// 6
// 将二维数组转化为一维 将数组元素展开
let flattened = [[0, 1], [2, 3], [4, 5]].reduce(
  (a, b) => a.concat(b),
  []
);
 // [0, 1, 2, 3, 4, 5]
```

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
Array.prototype.max = function() {
  return Math.max.apply({},this)
};

Array.prototype.min = function() {
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

### 合并两个 Array 并去掉重复项
``` js
Array.prototype.unique = function() {
  var a = this.concat();
  for(var i=0; i<a.length; ++i) {
    for(var j=i+1; j<a.length; ++j) {
      if(a[i] === a[j])
        a.splice(j, 1);
      }
    }
  }
  return a;
};

//Demo
var array1 = ["a","b"];
var array2 = ["b", "c"];
var array3 = array1.concat(array2).unique();
// ["a","b","c"]
```

### 取两个数组交集
``` js
/* finds the intersection of 
 * two arrays in a simple fashion.  
 *
 * PARAMS
 *  a - first array, must already be sorted
 *  b - second array, must already be sorted
 *
 * NOTES
 *
 *  Should have O(n) operations, where n is 
 *    n = MIN(a.length(), b.length())
 */
function arrayIntersection(a, b) {
  var ai=0, bi=0;
  var result = new Array();

  while( ai < a.length && bi < b.length ) {
    if(a[ai] < b[bi] ) { 
      ai++; 
    } else if (a[ai] > b[bi] ) { 
      bi++; 
    } else { /* they're equal */
      result.push(a[ai]);
      ai++;
      bi++;
    }
  }

  return result;
}

console.log(arrayIntersection([1,2,3],[2,3,4,5,6]));//[2,3]
```

### 数组扁平化
``` js
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
```

### 把 arguments 转换为 Array
``` js
var args = Array.prototype.slice.call(arguments, 0);
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
