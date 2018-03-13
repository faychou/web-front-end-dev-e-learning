# 数组

## 数组遍历

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

