# 节流与防抖
函数节流和函数防抖都是对大量频繁调用代码的一种优化。

## 节流
不管怎么触发，都是按照指定的时间间隔来执行。简单地说，就是限制函数在一定时间内调用的次数。

``` js
// 实现方案
let throttleIndentify = 0;
$dragable.addEventListener('mousemove', () => {
  if(throttleIndentify) return;
  throttleIndentify = setTimeout(() => throttleIdentify = 0, 500);
  console.log('trigger');
})

// 简易版本
var my_throttled_timeout = null,
  prev_time = null,
  my_function = function() {
    document.querySelector('.j_my_throttle_sum').innerHTML = parseInt(document.querySelector('.j_my_throttle_sum').innerHTML) + 1;
    my_throttled_timeout = null
    prev_time = new Date().getTime();

  };

window.addEventListener('mousemove', function(){
  if (my_throttled_timeout) return;
  remaining = 3000 - ( new Date().getTime() - prev_time )
  if ( remaining <= 0 ) {
    my_function()
  } else {
    my_throttled_timeout = setTimeout(function() {
      my_function()
    }, remaining)
  }   
})
```

``` js
// underscore 的 throttle 源码
_.throttle = function(func, wait, options) {
    var timeout, context, args, result;
    var previous = 0;
    if (!options) options = {};

    var later = function() {
      previous = options.leading === false ? 0 : _.now();
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };

    var throttled = function() {
      var now = _.now();
      if (!previous && options.leading === false) previous = now;
      var remaining = wait - (now - previous); // 代表还有多少时间, 可以执行下一次函数
      context = this;
      args = arguments;

      if (remaining <= 0 || remaining > wait) { //只有当没有设置options.leading = false和 非节流的情况第一次能进来这里. 
        if (timeout) { // 虽然 remaining不可能超过wait, 这里没必要clearTimeout(timeout), 因为如果setTimeout能准确执行的话, 这里timeout肯定不存在
          clearTimeout(timeout); //但是seTimeout 并不准确, 可能会延迟, 所以可能到了超过remaining的时间, 但setTimeout还没执行, 所以要移除掉
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {// 被节流的函数都要执行setimeout, 
        timeout = setTimeout(later, remaining);
      }
      return result;
    };

    throttled.cancel = function() {
      clearTimeout(timeout);
      previous = 0;
      timeout = context = args = null;
    };

    return throttled;
  };
```

## 防抖
不管你触发了多少次，都等到你最后触发后过一段你指定的时间才触发。应用场景: 拖动 window 的 size, 不断触发 resize, 当停止拖动时, 才做处理。

``` js
let debounceIdentify = 0;
window.addEventListener('resize', () => {
  debounceIdentify && clearTimeout(debounceIdentity)
  debounceIdentity = setTimeout(() => {
    console.log('trigger')
  }, 300)
});
```

``` js
// underscore 的 debounce
_.debounce = function(func, wait, immediate) {
    var timeout, result;

    var later = function(context, args) {
      timeout = null;
      if (args) result = func.apply(context, args); //只有immediate非true时 才会执行到这里
    };

    var debounced = restArgs(function(args) {
      if (timeout) clearTimeout(timeout);
      if (immediate) { //立即先执行一次 然后再进行反抖
        var callNow = !timeout;
        timeout = setTimeout(later, wait);
        if (callNow) result = func.apply(this, args);
      } else {
        timeout = _.delay(later, wait, this, args);
      }

      return result;
    });

    debounced.cancel = function() {
      clearTimeout(timeout);
      timeout = null;
    };

    return debounced;
  };
```

### 应用：输入框
搜索引擎会对你输入的文字进行预判，并在下方推荐相关的结果。所以我们就在监听用户输入的事件那里做函数防抖处理，在 n 秒后发送输入框的联想的 ajax 请求，获取返回数据，展示在页面中。

``` js
function debounce(func, wait, leading, trailing) {
  var timer, lastCall = 0, flag = true
  return function() {
    var context = this
    var args = arguments
    var now = + new Date()
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
