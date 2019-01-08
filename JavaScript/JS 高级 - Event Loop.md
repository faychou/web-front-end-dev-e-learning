# Event Loop
简单点讲 event loop 就是对 JS 代码执行顺序的一个规定。

## 同步代码
按顺序执行。

## 非阻塞代码
一般分为两种任务，macroTasks（宏任务） 和 microTasks（微任务）。

* 宏任务包括：setTimeout, setInterval, setImmediate, I/O, UI rendering

* 微任务包括： 原生 Promise(有些实现的 Promise 将 then 方法放到了宏任务中), Object.observe(已废弃), MutationObserver， MessageChannel。
