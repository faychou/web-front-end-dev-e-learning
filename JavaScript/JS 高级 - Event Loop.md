# Event Loop
简单点讲 event loop 就是对 JS 代码执行顺序的一个规定。

JS 是单进程的，也就是一次只能执行一个任务。

## 同步代码
按顺序执行。

### 同步代码合并
一段同步代码修改同一个元素的属性，浏览器会直接优化到最后一个。

``` js
box.style.display = 'none'
box.style.display = 'block'
box.style.display = 'none'
```

浏览器会直接隐藏元素，相当于只运行了最后一句。

## 异步队列
### macroTasks（宏任务）
JS 增加了异步队列 (task queue) 来解决需要等待一会儿再处理的任务，例如 setTimeout，或者异步请求等。每次碰到异步操作，就把操作添加到异步队列中。等待主进程为空（即没有同步代码需要执行了），就去执行异步队列。执行完成后再回到主进程。这样就不会严重影响效率和体验。

包括：setTimeout, setInterval, setImmediate, I/O, UI rendering 等。

> Tasks 只执行一个。执行完了就进入主进程。

### microTasks（微任务）
microTasks 就是在 当次 事件循环的 结尾 立刻执行 的任务。包括原生 Promise(有些实现的 Promise 将 then 方法放到了宏任务中), MutationObserver， MessageChannel。

> 补充：因为是当次执行，因此如果既设置了 setTimeout(0) 又设置了 Promise.then()，优先执行 Microtasks。

### requestAnimationFrame
是一个特别的异步任务，只是注册的方法不加入异步队列，而是加入渲染这一边的队列中。

> 执行队列里的全部任务，但如果任务本身又新增 Animation callback 就不会当场执行了，因为那是下一个循环。
