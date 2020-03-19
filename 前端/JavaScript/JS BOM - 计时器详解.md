# 计时器
计时器分为两种，一种是 setInterval()，另一种是 setTimeout()。

### setInterval()
setInterval 函数指定某个任务每隔一段时间就执行一次，也就是无限次的定时执行。它返回一个整数，表示定时器的编号，以后可以用来取消这个定时器。

``` js
var timerId = setInterval(func, delay);
```

#### setInterval 函数还可以接受更多的参数
``` js
function f(){
  for (var i=0;i<arguments.length;i++){
    console.log(arguments[i]);
  }
}

setInterval(f, 1000, "Hello World");
```

> 注意：setInterval 指定的是“开始执行”之间的间隔，并不考虑每次任务执行本身所消耗的事件。因此实际上，两次执行之间的间隔会小于指定的时间。比如，setInterval 指定每100ms 执行一次，每次执行需要 5ms，那么第一次执行结束后95毫秒，第二次执行就会开始。如果某次执行耗时特别长，比如需要 105 毫秒，那么它结束后，下一次执行就会立即开始。

``` js
//为了确保两次执行之间有固定的间隔，可以使用 setTimeout
var i = 1;
var timer = setTimeout(function() {
  alert(i++);
  timer = setTimeout(arguments.callee, 2000);
}, 2000);
```

### setTimeout()
setTimeout 函数用来指定某个函数或某段代码，在多少毫秒之后执行。它返回一个整数，表示定时器的编号，以后可以用来取消这个定时器。

``` js
var timerId = setTimeout(func|code, delay);
```

* 参数一：func|code 是将要推迟执行的函数名或者一段代码；
* 参数二：delay 是推迟执行的毫秒数。

``` js
//如：
setTimeout('console.log(2)',1000);
```

需要注意的是，推迟执行的代码必须以字符串的形式，放入setTimeout，因为引擎内部使用eval函数，将字符串转为代码。如果推迟执行的是函数，则可以直接将函数名，放入setTimeout。一方面eval函数有安全顾虑，另一方面为了便于JavaScript引擎优化代码，setTimeout方法一般总是采用函数名的形式。

``` js
var timeId = setTimeout(function () {
  console.log(2)
},1000);

// 或者
function func() {
  console.log(2);
}
var timeId = setTimeout(func,1000);
```

#### setTimeout 传递多参数
除了前两个参数，setTimeout 还允许添加更多的参数。它们将被传入推迟执行的函数（回调函数）。

``` js
setTimeout(function(a,b){
  console.log(a+b);
},1000,1,1);
```

IE 9 及以下版本，只允许 setTimeout 有两个参数，不支持更多的参数。

#### setTimeout 中的 this
如果被 setTimeout 推迟执行的回调函数是某个对象的方法，那么该方法中的 this 关键字将指向全局环境，而不是定义时所在的那个对象。

``` js
var x = 1;

var o = {
  x: 2,
  y: function(){
    console.log(this.x);
  }
};
setTimeout(o.y,1000); // 输出的是1，而不是2
```

解决方案是在 setTimeout 后加上 bind 。

``` js
setTimeout(o.y,1000).bind(o);
```

### clearTimeout()
setTimeout 返回一个表示计数器编号的整数值，将该整数传入 clearTimeout 函数，就可以取消计时器。

``` js
var timeId = setTimeout(func,1000);

clearTimeout(timeId);
```

## setTimeout 运行机制
setTimeout 和 setInterval 的运行机制是，将指定的代码移出本次执行，等到下一轮 Event Loop 时，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就等到再下一轮 Event Loop 时重新判断。这意味着，setTimeout 指定的代码，必须等到本次执行的所有代码都执行完，才会执行。

每一轮 Event Loop 时，都会将“任务队列”中需要执行的任务，一次执行完。setTimeout 和 setInterval 都是把任务添加到“任务队列”的尾部。因此，它们实际上要等到当前脚本的所有同步任务执行完，然后再等到本次 Event Loop 的“任务队列”的所有任务执行完，才会开始执行。由于前面的任务到底需要多少时间执行完，是不确定的，所以没有办法保证，setTimeout 和 setInterval 指定的任务，一定会按照预定时间执行。

### setTimeout(func,0)
``` js
setTimeout(function () {
  func1();
}, 0);

func2();
```

运行以上代码，很明显会发现 func2 比 func1 先执行，因为 setTimeout 必须要等到当前脚本的同步任务和“任务队列”中已有的事件，全部处理完以后，才会执行 setTimeout 指定的任务。也就是说，setTimeout(f,0) 的作用是让现有的任务（脚本的同步任务和“任务队列”中已有的事件）一结束就立刻执行，即尽可能早地执行指定的任务。

所以 0 毫秒实际上达不到的。根据 HTML5 标准，setTimeOut 推迟执行的时间，最少是 4 毫秒。如果小于这个值，会被自动增加到 4。这是为了防止多个 setTimeout(f,0) 语句连续执行，造成性能问题。

另一方面，浏览器内部使用 32 位带符号的整数，来储存推迟执行的时间。这意味着 setTimeout 最多只能推迟执行 2147483647毫秒（24.8天），超过这个时间会发生溢出，导致回调函数将在当前任务队列结束后立即执行，即等同于 setTimeout(func,0) 的效果。

## setTimeout 应用
### 调整事件的发生顺序
用户在输入框输入文本，keypress 事件会在浏览器接收文本之前触发。如果想让输入的字母立即转化为大写字母，就可以使用这种方法：

``` js
document.getElementsByTagName('input[type=text]')[0].onkeypress = function() {
  var self = this;
  setTimeout(function() {
    self.value = self.value.toUpperCase();
  }, 0);
}
```

由于 keypress 事件会在浏览器接收文本之前触发，所以浏览器此时还没接收到文本， self.value 取不到最新输入的那个字符，只有用 setTimeout 改写。

### 分割耗时操作
众所周知 javascript 是单线程的，特点就是容易出现阻塞。如果一段程序处理时间很长，很容易导致整个页面 hold 住。什么交互都处理不了。setTimeout 一个很关键的用法就是分片，如果一段程序过大，我们可以拆分成若干细小的块。由于 setTimeout(func,0) 实际上意味着，将任务放到浏览器最早可得的空闲时段执行，所以那些计算量大、耗时长的任务，常常会被放到几个小部分，分别放到 setTimeout(func,0) 里面执行(分片塞入队列)，这样即使在复杂程序没有处理完时，我们操作页面，也是能得到即时响应的。

``` js
var div = document.getElementsByTagName('div')[0];

// 写法一
for(var i=0xA00000;i<0xFFFFFF;i++) {
  div.style.backgroundColor = '#'+i.toString(16);
}

// 写法二
var timer;
var i=0x100000;

function func() {
  timer = setTimeout(func, 0);
  div.style.backgroundColor = '#'+i.toString(16);
  if (i++ == 0xFFFFFF) clearInterval(timer);
}

timer = setTimeout(func, 0);
```

都是改变一个网页元素的背景色，写法一会造成浏览器“堵塞”，而写法二就不会，这就是 setTimeout(f,0) 的好处。即：可利用 setTimeout 实现一种伪多线程的概念。

### 防抖动
用来返回一个新函数，只有当两次触发之间的时间间隔大于事先设定的值，这个新函数才会运行实际的任务，以避免函数频繁执行，如滚动监听函数。

``` js
function debounce(fn, delay){
  var timer = null; // 声明计时器
  return function(){
    var context = this;
    var args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function(){
      fn.apply(context, args);
    }, delay);
  };
}

// 用法示例
var todoChanges = debounce(batchLog, 1000);
Object.observe(models.todo, todoChanges);
```

## requestAnimationFrame
专门为实现高性能的帧动画而设计的 API，但是不能指定延迟时间，而是根据浏览器的刷新频率而定（帧）。涉及到制作动画避免使用 setTimeout 和 setInterval，建议使用 requestAnimationFrame(RAF)，或者直接采用CSS来写。

``` js
var requestId = window.requestAnimationFrame(func);
```

### 优势:
* requestAnimationFrame 会把每一帧中的所有 DOM 操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒 60 帧。
* 在隐藏或不可见的元素中，requestAnimationFrame 将不会进行重绘或回流，这当然就意味着更少的的 cpu 、gpu 和内存使用量。

