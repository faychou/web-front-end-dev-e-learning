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

## 发布者,订阅者模式
``` js
var Site = {};
Site.userList = [];
Site.subscribe = function( fn ) {
  this.userList.push( fn );
}
Site.publish = function() {
  for( var i = 0, len = this.userList.length; i < len; i++ ) {
    this.userList[i].apply( this, arguments );
  } 
}
Site.subscribe( function( type ) {
  console.log( "网站发布了" + type + "内容" );
});
Site.subscribe( function( type ) {
  console.log( "网站发布了" + type + "内容" );
});
Site.publish( 'javascript' );
Site.publish( 'html5' );
```

Site.userList就是用来保存订阅者

Site.subscribe就是具体的订阅者，把每一个订阅者订阅的具体信息保存在Site.userList

Site.publish就是发布者：根据保存的userList，一个个遍历(通知)，执行里面的业务逻辑.