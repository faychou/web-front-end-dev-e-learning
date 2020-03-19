# JavaScript 设计模式

## 工厂模式
工厂模式是为了解决多个类似对象声明的问题;也就是为了解决实列化对象产生重复的问题。

``` js
function CreatePerson(name,age,sex) {
  var obj = new Object();
  obj.name = name;
  obj.age = age;
  obj.sex = sex;
  obj.sayName = function(){
    return this.name;
  }
  return obj;
}

var p1 = new CreatePerson("long",'28','男');
var p2 = new CreatePerson("jiao",'27','女');
console.log(p1.name); // long
console.log(p1.age);  // 28
console.log(p1.sex);  // 男
console.log(p1.sayName()); // long

console.log(p2.name);  // jiao
console.log(p2.age);   // 27
console.log(p2.sex);   // 女
console.log(p2.sayName()); // jiao

// 返回都是object 无法识别对象的类型 不知道他们是哪个对象的实列
console.log(typeof p1);  // object
console.log(typeof p2);  // object
console.log(p1 instanceof Object); // true
```

## 观察者模式
这种模式的实质就是你可以对程序中的某个对象的状态进行观察，并且在其发生改变时能够得到通知。也称为发布者、订阅者模式。

``` js
var Site = {};
Site.userList = []; // 订阅者列表
Site.subscribe = function( fn ) { // 增加订阅者
  this.userList.push( fn );
};

Site.publish = function() { // 发布消息
  for( var i = 0, len = this.userList.length; i < len; i++ ) {
    this.userList[i].subscribe.apply( this, arguments );
  } 
}
Site.subscribe( function( type ) {
  console.log( "网站发布了" + type + "内容" );
});
Site.publish( 'javascript' );
Site.publish( 'html5' );
```

Site.userList 就是用来保存订阅者

Site.subscribe 就是具体的订阅者，把每一个订阅者订阅的具体信息保存在 Site.userList

Site.publish 就是发布者：根据保存的 userList，一个个遍历(通知)，执行里面的业务逻辑。



``` js
var Event = function() {
  this.obj = {}
}

Event.prototype.on = function(eventType, fn) {
  if (!this.obj[eventType]) {
    this.obj[eventType] = []
  }
  this.obj[eventType].push(fn)
}

Event.prototype.emit = function() {
  var eventType = Array.prototype.shift.call(arguments)
  var arr = this.obj[eventType]
  for (let i = 0; i < arr.length; i++) {
    arr[i].apply(arr[i], arguments)
  }
}

var ev = new Event()

ev.on('click', function(a) { // 订阅函数
  console.log(a) // 1
})

ev.emit('click', 1)  // 发布函数
```

## 单例模式
一个类只能构造出唯一实例，并且可以全局访问。保证一个类只有一个实例，实现方法是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，确保一个类只有一个实例对象。

``` js
const singleton = function(name) {
  this.name = name
  this.instance = null
}

singleton.prototype.getName = function() {
  console.log(this.name)
}

singleton.getInstance = function(name) {
  if (!this.instance) { // 关键语句
    this.instance = new singleton(name)
  }
  return this.instance
}

// test
const a = singleton.getInstance('a') // 通过 getInstance 来获取实例
const b = singleton.getInstance('b')
console.log(a === b); // true
```

### 适用场景
适用于弹框的实现，全局缓存

``` js
//创建登录框
const createLoginLayer = function() {
  const div = document.createElement('div')
  div.innerHTML = '登入浮框'
  div.style.display = 'none'
  document.appendChild(div)
  return div
}

const getSingle = function(fn) {
  const result
  return function() {
    return result || result = fn.apply(this, arguments)
  }
}

const createSingleLoginLayer = getSingle(createLoginLayer)

document.getElementById('loginBtn').onclick = function() {
  createSingleLoginLayer()
}
```

又如：

``` js
var singleton = function(fn) {
  var result; 
  return function() {
    return result || ( result = fn .apply(this, arguments));
  }
}
```

## 装饰者模式	
为对象添加新功能，不改变原有的结构和功能, 优点是把类（函数）的核心职责和装饰功能区分开了。

## 适配器模式
是将一个类（对象）的接口（方法或属性）转化成客户希望的另外一个接口（方法或属性），适配器模式使得原本由于接口不兼容而不能一起工作的那些类（对象）可以一些工作。

适配器模式的作用很像一个转接口. 本来iphone的充电器是不能直接插在电脑机箱上的, 而通过一个usb转接口就可以了。

## 代理模式	
使用者无权访问目标对象, 为其他对象提供一种代理以控制对这个对象的访问, 接口不变。

运用代理模式来实现图片预加载，可以看到通过代理模式巧妙地将创建图片与预加载逻辑分离，并且在未来如果不需要预加载，只要改成请求本体代替请求代理对象就行。

``` js
const myImage = (function() {
  const imgNode = document.createElement('img')
  document.body.appendChild(imgNode)
  return {
    setSrc: function(src) {
      imgNode.src = src
    }
  }
})()

const proxyImage = (function() {
  const img = new Image()
  img.onload = function() { // http 图片加载完毕后才会执行
    myImage.setSrc(this.src)
  }
  return {
    setSrc: function(src) {
      myImage.setSrc('loading.jpg') // 本地 loading 图片
      img.src = src
    }
  }
})()

proxyImage.setSrc('http://loaded.jpg')
```

## 策略模式
根据不同参数可以命中不同的策略。

``` js
const strategy = {
  'S': function(salary) {
    return salary * 4
  },
  'A': function(salary) {
    return salary * 3
  },
  'B': function(salary) {
    return salary * 2
  }
}

const calculateBonus = function(level, salary) {
  return strategy[level](salary)
}

calculateBonus('A', 10000) // 30000
```

优点：能减少大量的 if 语句，复用性好。

## 命令模式
用于将一个请求封装成一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及执行可撤销的操作。也就是说改模式旨在将函数的调用、请求和操作封装成一个单一的对象，然后对这个对象进行一系列的处理。此外，可以通过调用实现具体函数的对象来解耦命令对象与接收对象。

