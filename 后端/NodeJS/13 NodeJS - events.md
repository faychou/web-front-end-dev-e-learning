## 内置模块-events

`events` 模块是 Node 实现事件驱动的核心，在 Node 中大部分的模块的实现都继承了 Events 类。

`events` 模块只提供了一个对象： `events.EventEmitter`。`EventEmitter` 的核心就是事件触发与事件监听器功能的封装，`EventEmitter` 本质上是一个观察者模式的实现。所有能触发事件的对象都是 `EventEmitter` 类的实例。 这些对象有一个 `eventEmitter.on()` 函数，用于将一个或多个函数绑定到命名事件上。使用 `eventEmitter.emit()` 触发事件，当 `EventEmitter` 对象触发一个事件时，所有绑定在该事件上的函数都会被同步地调用。 注意在在注册事件前，触发该事件，则该事件不会被触发 !

``` js
const EventEmitter = require('events')

class Application extends EventEmitter {
  constructor() {
    super()
  }
}
const app = new Application()
//  监听 hello 事件
app.on('hello', data => {
  console.log(data) // hello nodeJs
})
//  触发 hello 事件
app.emit('hello', 'hello nodeJs')
```

当监听器函数被调用时， `this` 关键词会被指向监听器所绑定的 `EventEmitter` 实例。也可以使用 `ES6` 的箭头函数作为监听器,但 `this` 关键词不会指向 `EventEmitter` 实例。

``` js
const EventEmitter = require('events')

class Person extends EventEmitter {
  constructor() {
    super()
  }
}
const mrNull = new Person()
//  监听play事件
mrNull.on('play1', function(data) {
  console.log(this)
  // Person {
  //   _events:
  //   [Object: null prototype] { play: [[Function], [Function]] },
  //   _eventsCount: 1,
  //     _maxListeners: undefined
  // }
})
//  监听play事件
mrNull.on('play2', data => {
  console.log(this) // {}
})
//  触发play事件
mrNull.emit('play2', 'hello nodeJs') // 注意，以注册的顺序同步地调用所有监听器。
console.log(`hello`)
// {}
// hello
```

使用 `eventEmitter.once()` 可以注册最多可调用一次的监听器。 当事件被触发时，监听器会被注销，然后再调用。

``` js
const EventEmitter = require('events')

class Person extends EventEmitter {
  constructor() {
    super()
  }
}
const mrNull = new Person()
mrNull.once('play', () => {
  console.log('play !')
})

mrNull.emit('play')
mrNull.emit('play')

// play ! 只输出一次
```

移除事件监听器：

``` js
const EventEmitter = require('events')

class Person extends EventEmitter {
  constructor() {
    super()
  }
}
const mrNull = new Person()

function play() {
  console.log('play !')
}
mrNull.on('play', play)

mrNull.emit('play')

// mrNull.off("play", play); v10.0.0版本新增,emitter.removeListener() 的别名。
//  or
mrNull.removeListener('play', play)

mrNull.emit('play') // play !  移除后不再触发
```

