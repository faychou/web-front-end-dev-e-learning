# Vue 事件
### v-on
v-on 指令用于给监听 DOM 事件，来触发一些 JavaScript 代码。

``` html
<div id="app">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">逆转消息</button>
</div>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue.js!'
    },
    methods: {
      reverseMessage: function () {
        this.message = this.message.split('').reverse().join('')
      }
    }
  })
</script>
```

传参：

``` html
<button v-on:click="say('hi')">Say hi</button>
```

v-on 指令可以缩写为 @ 符号：

``` html
<!--完整语法-->
<button v-on:click="greet">Greet</button>
<!--缩写语法-->
<button @click="greet">Greet</button>
```

### 事件修饰符
在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在 methods 中轻松实现这点，但更好的方式是：methods 只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

``` html
<!-- 阻止事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 阻止默认行为 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联  -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件侦听器时使用时间捕获模式 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>
```

### 按键修饰符
Vue 允许为 v-on 添加监测常见的键值。

``` html
<!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
<input v-on:keyup.13="submit">
<!-- 同上 -->
<input v-on:keyup.enter="submit">
<!-- 缩写语法 -->
<input @keyup.enter="submit">

<!--全部的按键别名：-->

    enter
    tab
    delete (捕获 “删除” 和 “退格” 键)
    esc
    space
    up
    down
    left
    right
    ctrl
    alt
    shift
    meta（在Mac系统键盘上，meta对应命令键 (⌘)。在Windows系统键盘meta对应windows徽标键(⊞)）
```

在 `v-on:click` 的时候想既传递参数又想传递事件对象，那么你需要手动传入 `$event` 参数。

``` html
<button v-on:click="warn('data', $event)">Submit</button>
```

## 事件对象
javaScript 中事件对象用 event ，vue 中事件对象用 `$event`。

有时也需要在内联语句处理器中访问原生 DOM 事件。可以用特殊变量 `$event` 把它传入方法：

``` html
<button v-on:click="warn('参数', $event)">Submit</button>

<script>
// ...
methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) event.preventDefault()
    alert(message)
  }
}
</script>
```

## 变异方法
Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：

* push()
* pop()
* shift()
* unshift()
* splice()
* sort()
* reverse()

## 自定义事件
* 使用 `$on(eventName)` 监听事件
* 使用 `$emit(eventName)` 触发事件

``` html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>

<script>
Vue.component('button-counter', {
  template: '<button v-on:click="increment">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    increment: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
</script>
```

### 给组件绑定原生事件
有时候，可能想在某个组件的根元素上监听一个原生事件，可以使用 `.native` 修饰 `v-on` 。

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```