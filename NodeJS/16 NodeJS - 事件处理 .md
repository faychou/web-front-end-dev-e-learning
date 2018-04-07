# 事件处理
events 是 Node.js 最重要的模块，所有能触发事件的对象都是 EventEmitter 类的实例。 这些对象开放了一个 eventEmitter.on() 函数，允许将一个或多个函数绑定到会被对象触发的命名事件上。

``` js
const EventEmitter = require('events');

const eventEmitter = new EventEmitter();

// eventEmitter.on() 方法用于注册监听器
eventEmitter.on('event', () => {
  console.log('触发了一个事件！');
});

// eventEmitter.emit() 方法用于触发事件
eventEmitter.emit('event');
```

``` js
addListener(event,listener) // 对指定的事件绑定事件处理函数

on(event,listener) // 对指定的事件绑定事件处理函数(addListener的别名),可以对同一事件绑定不同事件处理函数

once(event,listener) // 对指定事件只执行一次绑定的事件处理函数

removeListener(event,listener) // 对指定事件解除事件处理函数

removeAllListener([event]) // 对指定事件解除所有事件处理函数

listeners(event) // 获取指定事件的所有事件处理函数

emit(event,[args],[..]) // 手工触发指定事件

setMaxListeners(n) // 指定事件处理函数的最大数量,默认对同一事件最大绑定10个事件处理函数.

EventEmitter.listenCount(emitter,event) // 获取某个对象的指定事件的事件处理函数数量
```