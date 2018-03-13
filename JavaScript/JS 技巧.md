# JS 技巧
### JavaScript 事件监听兼容写法
包括 IE 下 this 指向问题。

``` js
function addEventHandler(oTarget, sEventType, fnHandler) {
  if(oTarget.addEventListener) {
    oTarget.addEventListener(sEventType,fnHandler,false);
  } else if(oTarget.attachEvent) {
    oTarget.attachEvent("on"+sEventType,function() {
      return fnHandler.call(oTarget,window.event);
    });
  } else{
    oTarget["on"+sEventType]=fnHandler;
  }
}
```

### 使用!!操作符转换布尔值
有时候我们需要对一个变量查检其是否存在或者检查值是否有一个有效值，如果存在就返回 true 值。为了做这样的验证，我们可以使用 `!!` 操作符来实现是非常的方便与简单。对于变量可以使用 `!!variable` 做检测，只要变量的值为:0、null、" "、undefined 或者 NaN 都将返回的是 false，反之返回的是 true。

``` js
console.log(!!10); //true
console.log(!!0); //false
console.log(!!"abc"); //true
console.log(!!""); //false

// 简单地说就是把右侧的值转为 bool 值
```

### 添加和删除 class
``` js
//添加 Class
document.getElementById("MyElement").className += " MyClass";

//删除 Class
document.getElementById("MyElement").className = document.getElementById("MyElement").className.replace(/(?:^|\s)MyClass(?!\S)/,'');
```

### 检查一个字符串是否为空最简单的方法
``` js
if (strValue) {
  //do something
}
```

### jQuery 中判断一个元素是否存在
``` js
if ($(selector).length)
```

### 克隆对象
``` js
function clone(obj) {
  // Handle the 3 simple types, and null or undefined
  if (null == obj || "object" != typeof obj) return obj;

  // Handle Date
  if (obj instanceof Date) {
    var copy = new Date();
    copy.setTime(obj.getTime());
    return copy;
  }

  // Handle Array
  if (obj instanceof Array) {
    var copy = [];
    for (var i = 0, var len = obj.length; i < len; ++i) {
      copy[i] = clone(obj[i]);
    }
    return copy;
  }

  // Handle Object
  if (obj instanceof Object) {
    var copy = {};
      for (var attr in obj) {
        if (obj.hasOwnProperty(attr)) copy[attr] = clone(obj[attr]);
        }
    return copy;
  }

  throw new Error("Unable to copy obj! Its type isn't supported.");
}
```

### JavaScript中浮点数的相等判断不能用 ==
``` js
console.log(0.1+0.2 == 0.3);//false
console.log(Math.abs(0.1+0.2 - 0.3) < 0.000001);//true
```

### 获取 URL 中的参数
``` js
function getQueryString() {
  var result = {}, queryString = location.search.substring(1),
    re = /([^&=]+)=([^&]*)/g, m;

  while (m = re.exec(queryString)) {
    result[decodeURIComponent(m[1])] = decodeURIComponent(m[2]);
  }

  return result;
}

// demo
var myParam = getQueryString()["myParam"];
```

### 根据日计算年龄
``` js
function getAge(dateString) {
  var today = new Date();
  var birthDate = new Date(dateString);
  var age = today.getFullYear() - birthDate.getFullYear();
  var m = today.getMonth() - birthDate.getMonth();

  if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
    age--;
  }
  return age;
}
```

### 判断当前页面是否被放入了 iframe 中
``` js
if(self==top){
  //not in iframe
}else{
  //in iframe
}
```
