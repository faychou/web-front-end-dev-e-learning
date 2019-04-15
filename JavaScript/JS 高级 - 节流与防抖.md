# 节流与防抖
函数节流和函数防抖都是对大量频繁调用代码的一种优化。

## 节流（throttle）
不管你触发了多少次函数，在规定的时间内都只会执行一次。简单地说，就是限制函数在一定时间内调用的次数。

有两种主流实现方式：

* 使用时间戳
* 设置定时器

``` js
// 时间戳实现
function throttle(fn, wait) {
  var args;
  // 前一次执行的时间戳
  var previous = 0;
  return function() {
    // 将时间转为时间戳
    var now = +new Date();
    args = arguments;
    // 时间间隔大于延迟时间才执行
    if (now - previous > wait) {
      fn.apply(this, args);
      previous = now;
    }
  };
}
```

``` js
// 定时器实现
function throttle(fn, wait) {
  var timer, context, args;
  return function() {
    context = this;
    args = arguments;
    // 如果定时器存在，则不执行
    if (!timer) {
      timer = setTimeout(function() {
        // 执行后释放定时器变量
        timer = null;
        fn.apply(context, args);
      }, wait);
    }
  };
}
```

将时间戳与定时器结合，可以实现兼并立刻执行和停止触发后依然执行一次的效果。

``` js
function throttle(fn, wait) {
  var timer, context, args;
  var previous = 0;
  // 延时执行函数
  var later = function() {
    previous = +new Date();
    // 执行后释放定时器变量
    timer = null;
    fn.apply(context, args);
    if (!timeout) context = args = null;
  };
  var throttled = function() {
    var now = +new Date();
    // 距离下次执行 fn 的时间
    // 如果人为修改系统时间，可能出现 now 小于 previous 情况
    // 则剩余时间可能超过时间周期 wait
    var remaining = wait - (now - previous);
    context = this;
    args = arguments;
    // 没有剩余时间 || 修改系统时间导致时间异常，则会立即执行回调函数fn
    // 初次调用时，previous为0，除非wait大于当前时间的时间戳，否则剩余时间一定小于0
    if (remaining <= 0 || remaining > wait) {
      // 如果存在延时执行定时器，将其取消掉
      if (timer) {
        clearTimeout(timer);
        timer = null;
      }
      previous = now;
      fn.apply(context, args);
      if (!timeout) context = args = null;
    } else if (!timer) {
      // 设置延时执行
      timer = setTimeout(later, remaining);
    }
  };
  return throttled;
}
```

优化完善：增加第三个参数，

``` js
// underscore 的 throttle 源码
function throttle(func, wait, options) {
  var context, args, result;
  var timeout = null;
  // 上次执行时间点
  var previous = 0;
  if (!options) options = {};
  // 延迟执行函数
  var later = function() {
    // 若设定了开始边界不执行选项，上次执行时间始终为0
    previous = options.leading === false ? 0 : new Date().getTime();
    timeout = null;
    // func 可能会修改 timeout 变量
    result = func.apply(context, args);
    // 定时器变量引用为空，表示最后一次执行，则要清除闭包引用的变量
    if (!timeout) context = args = null;
  };
  var throttled = function() {
    var now = new Date().getTime();
    // 首次执行时，如果设定了开始边界不执行选项，将上次执行时间设定为当前时间。
    if (!previous && options.leading === false) previous = now;
    // 延迟执行时间间隔
    var remaining = wait - (now - previous);
    context = this;
    args = arguments;
    // 延迟时间间隔remaining小于等于0，表示上次执行至此所间隔时间已经超过一个时间窗口
    // remaining 大于时间窗口 wait，表示客户端系统时间被调整过
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }
      previous = now;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    } else if (!timeout && options.trailing !== false) {
      timeout = setTimeout(later, remaining);
    }
    // 返回回调函数执行后的返回值
    return result;
  };
  throttled.cancel = function() {
    clearTimeout(timeout);
    previous = 0;
    timeout = context = args = null;
  };
  return throttled;
}
```

> 注意：leading: false 和 trailing: false 不能同时设置。

## 防抖（debounce）
无论你触发了多少次函数，都只会执行最后一次函数。应用场景: 如 size 事件、表单提交等。

``` js
// 简易版本
function debounce(func, delay) {
  var timer;
  return function() {
    // 清除上一次调用时设置的定时器
    clearTimeout(timer);
    // 重新设置计时器
    timer = setTimeout(func, delay);
  };
}
```

简易版本问题一：debounce 函数在定时器中调用回调函数 func，所以 func 执行的时候 this 是指向全局对象的，这时就需要使用 apply 进行显式绑定：

``` js
function debounce(func, delay) {
  var timeout;
  return function(e) {
    clearTimeout(timeout);
    // 保存调用时的this
    var context = this;
    timeout = setTimeout(function() {
      // 修正 this 的指向
      func.apply(context);
    },delay)
  };
}
```

简易版本问题二：JavaScript 的事件处理函数中会提供事件对象 event，在闭包中调用时需要将这个事件对象传入：

``` js
// 优化版
function debounce(func, delay) {
  var timer;
  return function() {
    // 保存调用时的 this
    var context = this;
    // 保存参数
    var args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function() {
      // 修正 this，并传入参数
      func.apply(context, args);
    }, delay);
  };
}
```

完善：增加第三个参数，来决定是先执行函数，然后再等待，还是先等待，然后再执行函数。

``` js
// 终极版：underscore 的 debounce
function debounce(fn, delay, immediate) {
  var timer, result;
  var debounced = function() {
    var context = this;
    var args = arguments;
    // 停止定时器
    if (timer) clearTimeout(timer);
    // 回调函数执行的时机
    if (immediate) {
      // 是否已经执行过
      // 执行过，则 timer 指向定时器对象，callNow 为 false
      // 未执行，则 timer 为 null，callNow 为 true
      var callNow = !timer;
      // 设置延时
      timer = setTimeout(function() {
        timer = null;
      }, delay);
      if (callNow) result = fn.apply(context, args);
    } else {
      // 停止调用后delay时间才执行回调函数
      timer = setTimeout(function() {
        fn.apply(context, args);
      }, delay);
    }
    // 回调函数可能有返回值，此处返回回调函数的返回值
    return result;
  };

  // 增加一个可以取消 debounce 操作，实现再次触发
  debounced.cancel = function() {
    clearTimeout(timer);
    timer = null;
  };

  return debounced;
}
```

### 应用：输入框
搜索引擎会对你输入的文字进行预判，并在下方推荐相关的结果。所以我们就在监听用户输入的事件那里做函数防抖处理，在 n 秒后发送输入框的联想的 ajax 请求，获取返回数据，展示在页面中。

``` js
function debounce(func, wait, leading, trailing) {
  var timer, lastCall = 0, flag = true
  return function() {
    var context = this
    var args = arguments
    var now = Date.now()
    if (now - lastCall < wait) {
      flag = false
      lastCall = now
    } else {
      flag = true
    }
    if (leading && flag) {
      lastCall = now
      return func.apply(context, args)
    }
    if (trailing) {
      clearTimeout(timer)
      timer = setTimeout(function() {
        flag = true
        func.apply(context, args)
      }, wait)
    }
  }
}
```

函数防抖一般用在 resize, touchmove, mousemove, scroll 等事件中。throttle 会强制函数以固定的速率执行，因此这个方法比较适合应用于动画相关的场景。
