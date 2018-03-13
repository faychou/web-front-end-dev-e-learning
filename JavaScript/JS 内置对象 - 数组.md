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
