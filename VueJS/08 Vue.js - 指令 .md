# Vue 指令
指令（Directives）是带有 `v-` 前缀的特殊属性。它们作用于 HTML 元素，当其表达式的值改变时相应地将某些行为应用到 DOM 上，Vue.js 的指令是以 `v-` 开头的。

## 内置指令
### v-if
v-if 是条件渲染指令，它根据表达式的真假来删除和插入元素。

``` html
<div id="app">
  <p v-if="seen">现在你看到我了</p>
</div>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      seen: true
    }
  })
</script>
```

> v-if 是“真正的”条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

### v-else-if
2.1.0版本新增的指令，充当 v-if 的“else-if 块”。可以链式地使用多次，必须紧跟在 v-if 或者 v-else-if 元素之后。

``` html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

### v-else
v-else 元素必须跟在 v-if 或 v-else-if 元素的后面，并且不需要表达式，否则它不能被识别。

``` html
<div id="app">
  <h1 v-if="age >= 25">Age: {{ age }}</h1>
  <h1 v-else>Name: {{ name }}</h1>
</div>
```

v-if 和 v-else 只能控制一个标签的渲染。如果想要控制一部分标签的渲染，需要用 `<template>` 包裹，v-if 作用在 template 标签上。

``` html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### v-show
v-show也是条件渲染指令，和v-if指令不同的是，使用v-show指令的元素始终会被渲染到HTML，它只是简单的切换元素的 CSS 属性 display。

``` html
<h1 v-show="ok">Hello!</h1>
```

> 注意， v-show 不支持 `<template>` 语法，也不支持 v-else。

### v-for
v-for 指令基于一个数组渲染一个列表，它和 JavaScript 的遍历语法相似。

``` html
<div id="app">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      todos: [
        { text: '学习 JavaScript' },
       { text: '学习 Vue' }
      ]
    }
  })
</script>
```

v-for 还支持一个可选的第二个参数为当前项的索引。

``` html
<li v-for="(todo,index) in todos"></li>
```

如果想循环渲染一部分标签，要用 `<template>` 包裹，v-for 作用在 template 标签上。

``` html
<template v-for="item in list">
  <p>{{item.content}}</p>
  <img :src="item.img" alt="">
  <p class="divider"></p>
</template>
```

对象迭代：

``` html
<table>
  <tr>
    <td v-for="(value,key,index) in memberDetail">{{key}}</td>
  </tr>
  <tr>
    <td v-for="(value,key,index) in memberDetail">{{value}}</td>
  </tr>
</table>
```

* value：代表属性值
* key：代表属性,
* index：代表每个dom节点的下标索引

传递迭代数据到组件里，我们要用 props ：

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index">
</my-component>
```

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。建议尽可能使用 v-for 来提供 key 。

``` html
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```

### v-cloak
这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。

``` html
<style>
  [v-cloak] {
    display: none;
  }
</style>

<div v-cloak>
  {{ message }}
</div>
```

## 自定义指令
Vue 里你还可以自定义指令，它们和内置指令一样，以 `v-` 开头。

#### 创建自定义指令
``` js
new Vue({
  directives: {
    focus: {
      inserted(el) {
        el.focus()
      },
    },
  },
})
```

#### 调用指令
``` html
<input v-focus>
```

#### 钩子函数
在directives里面就是所有的自定义指令，focus就是指令的名称，focus里面会加入多个方法函数，这些函数就是钩子函数，每一个钩子函数都会在不同的时刻执行，从而实现你想要的功能。

* bind: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。
* inserted: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。
* update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新（详细的钩子函数参数见下）。
* componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。
* unbind: 只调用一次， 指令与元素解绑时调用。

#### 钩子函数的参数
钩子函数被赋予了以下参数：

* el: 指令所绑定的元素，可以用来直接操作 DOM 。
* binding: 一个对象，包含以下属性：
 * name: 指令名，不包括 v- 前缀。
 * value: 指令的绑定值， 例如： v-my-directive="1 + 1", value 的值是 2。
 * oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
 * expression: 绑定值的字符串形式。 例如 v-my-directive="1 + 1" ，expression 的值是 "1 + 1"。
 * arg: 传给指令的参数。例如 v-my-directive:foo， arg 的值是 "foo"。
 * modifiers: 一个包含修饰符的对象。 例如： v-my-directive.foo.bar, 修饰符对象 modifiers 的值是 { foo: true, bar: true }。
* vnode: Vue 编译生成的虚拟节点，查阅 VNode API 了解更多详情。
* oldVnode: 上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

除了 el 之外，其它参数都应该是只读的，尽量不要修改他们。如果需要在钩子之间共享数据，建议通过元素的 dataset 来进行。