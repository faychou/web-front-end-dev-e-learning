# Vue.js 全家桶系列
Vue.js 是当下很火的一套构建用户界面的渐进式框架，它是以数据驱动和组件化的思想构建的。根据官方的解释的理解，vue 不属于 MVC 的范畴，因为它只有 MV，而且它的 M 是内置于框架内核中，对于开发者而言，可能基本上都是在处理 V 的东西。相比于 Angular.js，Vue.js 提供了更加简洁、更易于理解的 API，使得我们能够快速地上手并使用 Vue.js。

## 一、兼容性
Vue.js 不支持 IE8 及其以下版本。

## 二、起步
引入Vue.js：

``` html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
```

hello world 事例：

``` html
<div id="app">
  {{ message }}
</div>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
</script>
```

## 三、Vue 实例
所有的vue程序都需要实例化之后使用，实例主要有两种，一个是Vue实例，一个是组件实例。
### 构造器
Vue实例是作用于某一个HTML元素上的，这个元素可以是HTML的body元素，也可以是指定了id的某个元素。每个vue.js应用的起步都是通过构造函数Vue创建一个Vue的根实例:

``` js
var vm = new Vue({
  // 选项
})
```

实例化Vue时需要传入选项对象，例如数据（data）、模板（template）、挂载元素（el）、方法（method）、生命周期钩子等选项。

``` js
var vm = new Vue({
  data:{
    message:'hello vue'
 }
})

const app = new Vue({
    render: h => h(AppContainer),
}).$mount('#app')


// new Vue({
//     el:'#app',
//     render: h => h(App)
// })
```

### 组件构造器
可以扩展 Vue 构造器，从而用预定义选项创建可复用的组件构造器。所谓组件构造器，就是创建一个组件的原型类。

``` js
var MyComponent = Vue.extend({
  // 扩展选项
})

// 所有的 `MyComponent` 实例都将以预定义的扩展选项被创建
var myComponentInstance = new MyComponent()
```

尽管可以命令式地创建扩展实例，不过在多数情况下建议将组件构造器注册为一个自定义元素，然后声明式地用在模板中。

### 属性与方法
数据都要绑定要data属性，方法都要绑定到methods方法。

每个 Vue 实例都会代理其 data 对象里所有的属性：

``` js
var data = { a: 1 }
var vm = new Vue({ 
data: data//这里的data是被代理的属性
})
vm.a === data.a // -> true

// 设置属性也会影响到原始数据
vm.a = 2
data.a // -> 2

// ... 反之亦然
data.a = 3
vm.a // -> 3
```

除了 data 属性， Vue 实例暴露了一些有用的实例属性与方法。这些属性与方法都有前缀 `$`，以便与代理的 data 属性区分。

``` js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // -> true
vm.$el === document.getElementById('example') // -> true
```

> 注意只有这些被代理的属性是响应的。如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。也就是说，你得在 new 之前，就把所有的 data 都传进去。实例创建之后，其实可以使用 `$set` 来加入属性，也可以实现响应功能。

在实例里面可以自己传进去一些方法进行调用，和 `this.$data` 一样，vue也有一些以`$`开头的方法，比如`app.$watch`等，这些都是 vue 内置的方法。

``` js
var app = new Vue({
  methods: {
    myMethod() {},
    otherMethod() {
      // 这里就可以使用this.myMethod()了
    },
  },
})
app.otherMethod() // 可以在外面调用

// $watch 是一个实例方法
vm.$watch('a', function (newVal, oldVal) {
  // 这个回调将在 `vm.a`  改变后调用
}
```


### 实例生命周期
每个 Vue 实例在被创建之前都要经过一系列的初始化过程。在这个过程中，实例也会调用一些 生命周期钩子 ，这就给我们提供了执行自定义逻辑的机会。

与 react 中监听 props 和 state 属性变化不同的是，在vue中是只监听data属性的变化。

![生命周期](https://cn.vuejs.org/images/lifecycle.png)

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  beforeCreate:function(){}, //组件实例化之前
  created: function () { //在实例创建后调用这个函数
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  },
  beforeMount:function(){}, //组件写入dom结构之前
  mounted:function(){ //组件写入dom结构了
    console.log(this.$children);
    console.log(this.$refs);
  },
  beforeUpdate:function(){}, //组件更新前
  updated:function(){}, //组件更新比如修改了文案
  beforeDestroy:function(){}, //组件销毁之前
  destroyed:function(){} //组件已经销毁
})
// -> "a is: 1"
```

## 四、数据绑定
Vue.js 使用了基于 HTML 的模版语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。所有 Vue.js 的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。

在底层的实现上， Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。

### Mustache（双大括号）
数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值，Mustache 将会被替代为对应数据的值 ,而且一直会监听该值，一但改变会跟着改变。

``` html
<p>hello {{world}}</p>
<p>{{`hello ${world}`}}</p>
<p v-text="`hello ${world}`"></p>
```

在为标签的属性赋值的时候不能Mustache语法，我们要用v-bind指令。

``` html
// 错误的写法
<div id="{{id}}"></div>
// 正确的写法
<div v-bind:id="id"></div>
<div v-bind:id="'list-' + id"></div>
```

### 纯 HTML
双大括号会将数据解释为纯文本，而非 HTML 。为了输出真正的 HTML ，你需要使用 v-html 指令：

``` html
<div v-html="rawHtml"></div>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      rawHtml : `<span style='color : red;'>显示红色字就解析成功了</span>`
    }
  })
</script>
```

被插入的内容都会被当做 HTML —— 数据绑定会被忽略。注意，你不能使用 v-html 来复合局部模板，因为 Vue 不是基于字符串的模板引擎。组件更适合担任 UI 重用与复合的基本单元。

你的站点上动态渲染的任意 HTML 可能会非常危险，因为它很容易导致 XSS 攻击。请只对可信内容使用 HTML 插值，绝不要对用户提供的内容插值。

### 文本
``` html
<span v-text="msg"></span>

<!-- 和下面的一样 -->
<span>{{msg}}</span>
```

### 属性
Mustache 不能直接在 HTML 属性中使用，而是使用下面的方法来绑定数据：

#### v-bind
v-bind 指令可以在其名称后面带一个参数，中间放一个冒号隔开，这个参数通常是 HTML 元素的属性。

``` html
<a v-bind:href="url">链接</a>

<script>
  var app = new Vue({
    el: '#app',
    data: {
      url:'http://www.baidu.com'
    }
  })
</script>
```

v-bind指令可以缩写为一个冒号：

``` html
<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>

<!-- 更多案例 -->
<!-- 绑定一个属性 -->
<img v-bind:src="imageSrc">
<!-- 缩写 -->
<img :src="imageSrc">
<!-- with inline string concatenation -->
<img :src="'/path/to/images/' + fileName">
<!-- class 绑定 -->
<div :class="{ red: isRed }"></div>
<div :class="[classA, classB]"></div>
<div :class="[classA, { classB: isB, classC: isC }]">
<!-- style 绑定 -->
<div :style="{ fontSize: size + 'px' }"></div>
<div :style="[styleObjectA, styleObjectB]"></div>
<!-- 绑定一个有属性的对象 -->
<div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>
<!-- 通过 prop 修饰符绑定 DOM 属性 -->
<div v-bind:text-content.prop="text"></div>
<!-- prop 绑定. “prop” 必须在 my-component 中声明。 -->
<my-component :prop="someThing"></my-component>
<!-- XLink -->
<svg><a :xlink:special="foo"></a></svg>
```

####  v-once
执行一次性地插值，当数据改变时，插值处的内容不会更新。但请留心这会影响到该节点上所有的数据绑定。

``` html
<span v-once>This will never change: {{ msg }}</span>
```

### javascript表达式
迄今为止，在我们的模板中，我们一直都只绑定简单的属性键值。但实际上，对于所有的数据绑定， Vue.js 都提供了完全的 JavaScript 表达式支持。

``` js
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

### 双向绑定
在Vue.js中可以使用v-model指令在表单元素上创建双向数据绑定。

``` html
<div id="app">
    <p>{{ message }}</p>
    <input type="text" v-model="message"/>
</div>
```

打开控制台输入以下内容回车，我们会发现，视图也会立即更新：

	app.message='haha'
	
#### 修饰符
``` html
<!-- 在 "change" 而不是 "input" 事件中更新 -->
<input v-model.lazy="msg" >

<!-- 自动将用户的输入值转为 Number 类型 -->
<input v-model.number="age" type="number">

<!-- 自动过滤用户输入的首尾空格 -->
<input v-model.trim="msg">
```

另一种实现数据双向绑定，是利用 .sync 修饰符：

``` js
// 声明一个双向绑定
<comp :foo.sync="bar"></comp>


// 上面一行代码会被会被扩展为下面这一行：
<comp :foo="bar" @update:foo="val => bar = val"></comp>

// 当子组件需要更新 foo 的值时，它会显式地触发一个更新事件：
this.$emit('update:foo', newValue)
```

## 五、计算属性（computed）
表达式只用于简单的运算，而处理模板语法中的复杂业务逻辑就需要使用计算属性。

所谓计算属性，就是跟ES5的getter一样的，用function来定义个属性，当你获取这个属性的时候，实际上是要执行这个function，执行function的过程就是计算过程，所以也就叫计算属性。所有的计算属性被放在computed里面，computed和data, methods同级。

``` html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>

<script>
  var vm = new Vue({
    el: '#example',
    data: {
      message: 'Hello'
    },
    computed: {
      reversedMessage: function () {
        return this.message.split('').reverse().join('')
      }
    }
  })
</script>
```

### computed 和 methods 的区别
1、 只要计算属性的依赖没有发生变化，当你再次调用计算属性的时候，能够立即返回上次缓存的计算值，而不需要从新执行计算属性的方法；

2、 而 methods 是方法，只要调用它，函数就会从新执行。有一定的触发条件。

使用计算属性与 methods 的区别并不明显，但在一些比较复杂或是数据量比较大的，并存在一些依赖关系的时候，计算属性的优势就会突显出来，此时使用计算属性会大大提高我们的性能。

我们为什么需要缓存？假设我们有一个性能开销比较大的的计算属性 A ，它需要遍历一个极大的数组和做大量的计算。然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用 method 替代。

## 六、watch
watch 是和 data, methods, computed 同级的一个参数，你可以在 watch 里面规定你要观察的属性，当这些属性发生变化的时候，它会执行对应的那个函数。

``` js
var app = new Vue({
  data: {
    message: '',
    msg: '',
  },
  watch: {
    message: function() { // 当app.$data.message发生变化的时候，执行这个函数
      this.msg = 'Changed!'
    },
  },
})
```

watch 用于观察和响应 Vue 实例上的数据变动,与计算属性相比，大部份情况下使用计算属性可能会更好一些，但当你想要在数据变化响应时，执行异步操作或开销较大的操作，这时使用 watch 将是更好的选择。

## 七、过滤器
Vue.js 允许你自定义过滤器，可被用作一些常见的文本格式化。过滤器可以用在两个地方：mustache 插值和 v-bind 表达式。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符指示:

``` html
<!-- in mustaches -->
{{ message | capitalize }}

<!-- 过滤器可以串联 -->
{{ message | filterA | filterB }}

<!-- in v-bind -->
<div v-bind:id="rawId | formatId"></div>

<!-- 过滤器可以接受参数,字符串 'arg1' 将传给过滤器作为第二个参数 -->
{{ message | filterA('arg1', arg2) }}
```

过滤器函数总接受表达式的值作为第一个参数。

``` js
new Vue({
  // ...
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```

Vue2.0把过滤器这块移除是有道理的，他们希望你更多使用computed，而不是过滤器。

## 八、指令
指令（Directives）是带有 v- 前缀的特殊属性。它们作用于HTML元素，当其表达式的值改变时相应地将某些行为应用到 DOM 上，Vue.js 的指令是以v-开头的。

### v-if
v-if是条件渲染指令，它根据表达式的真假来删除和插入元素。

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

### 自定义指令
vue里你还可以自定义指令，它们和内置指令一样，以v-开头。

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

## 九、事件
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

有时也需要在内联语句处理器中访问原生 DOM 事件。可以用特殊变量 `$event` 把它传入方法：

``` html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>

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

v-on 指令可以缩写为 @ 符号：

``` html
<!--完整语法-->
<button v-on:click="greet">Greet</button>
<!--缩写语法-->
<button @click="greet">Greet</button>
```

### 事件修饰符
在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。尽管我们可以在 methods 中轻松实现这点，但更好的方式是：methods 只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

``` html
<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
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

在v-on:click的时候想既传递参数又想传递事件对象，那么你需要手动传入`$event`参数。

``` html
<button v-on:click="warn('data', $event)">Submit</button>
```

### 变异方法
Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：

* push()
* pop()
* shift()
* unshift()
* splice()
* sort()
* reverse()

## 十、 表单
``` html
<input v-model="message">

<textarea v-model="message" placeholder="add multiple lines"></textarea>

<!-- 当选中时，`picked` 为字符串 "a" -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` 为 true 或 false -->
<input type="checkbox" v-model="toggle">

<!-- 当选中时，`selected` 为字符串 "abc" -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

> 当v-bind和v-model同时出现在一个input上时，这个v-bind会失效。

### 复选框
``` html
<input type="checkbox" v-model="checked">
```

这里的v-model绑定了checked，但是checked这个变量应该是一个boolean值，表示这个checkbox是否被选中，是绑定到了prop属性。

另一种是给两个绑定值，如下：

``` html
<input type="checkbox" v-model="checked" :true-value="a" :false-value="b">
```

这里的a和b都是变量，选中的时候checked值等于a，即app.checked = app.a，否则app.checked = app.b。这样checked就不是boolean值了。

> 注意：checkbox单个复选框的时候，不能使用:value="xxx"绑定值，即使绑定了也没用，会被舍弃。

``` html
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>

<span>Checked names: {{ checkedNames }}</span>

<script>
  new Vue({
    el: '...',
    data: {
      checkedNames: [],
    }
  })
</script>
```

这里 `type="checkbox"` 是一定要有的，如果没有 `type="checkbox"`，会报错，type 值必须是字符串，不能通过`v-bind`进行绑定使用动态值，否则 vue 会报错。

`data.checkedNames`一定要是数组，当用户勾选了上面 checkbox 的其中一个时，它的 value 值会被丢到`data.checkedNames`这个数组中来。而它的 value 值则可以通过`v-bind`绑定一个动态数据。

在HTML中，通过相同的一个name值来确定这组checkbox是一个组的，而在这里，则是通过v-model的值是同一个值来确定是一个组的。这个时候就可以使用:value来动态绑定value值了，这和单个复选框不一样。

### 单选按钮
``` html
<input type="radio" id="one" value="One" v-model="picked">
<input type="radio" id="two" value="Two" v-model="picked">

<span>Picked: {{ picked }}</span>

<script>
  new Vue({
    el: '#example-4',
    data: {
      picked: '',
    }
  })
</script>
```

单选框组跟多个checkbox复选框组是一样的。包括:value绑定。唯一不同的是picked是一个单选值，所以是一个值，而不是数组。

### 列表
#### 单选列表
``` html
<select id="example-5" v-model="selected">
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<span>Selected: {{ selected }}</span>

<script>
  new Vue({
    el: '#example-5',
    data: {
      selected: null
    }
  })
</script>
```

#### 多选列表（绑定到一个数组）
``` html
<select id="example-6" v-model="selected" multiple style="width: 50px">
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<span>Selected: {{ selected }}</span>

<script>
  new Vue({
    el: '#example-6',
    data: {
      selected: []
    }
  })
</script>
```

因为multiple要选择的值是一组，因此selected是一个数组。

#### 动态渲染：
``` html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
   {{ option.text }}
  </option>
</select>

<span>Selected: {{ selected }}</span>

<script>
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
</script>
```

### 修饰符
#### .lazy
在默认情况下， v-model 在 input 事件中同步输入框的值与数据 (除了 上述 IME 部分)，但你可以添加一个修饰符 lazy ，从而转变为在 change 事件中同步：

``` html
<!-- 在 "change" 而不是 "input" 事件中更新 -->
<input v-model.lazy="msg" >
```

#### .number
如果想自动将用户的输入值转为 Number 类型（如果原值的转换结果为 NaN 则返回原值），可以添加一个修饰符 number 给 v-model 来处理输入值：

``` html
<input v-model.number="age" type="number">
```

因为在 type="number" 时 HTML 中输入的值也总是会返回字符串类型。

#### .trim
如果要自动过滤用户输入的首尾空格，可以添加 trim 修饰符到 v-model 上过滤输入：

``` html
<input v-model.trim="msg">
```

## 十一、Class 与 Style
操作元素的 class 列表和它的内联样式可以用 v-bind 处理。v-bind:class 指令可以与普通的 class 属性共存

``` html
<div v-bind:class="{ active: isActive }"></div>

<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>

<!-- 动态地切换 class：-->
<div v-bind:class="[isActive ? activeClass : '', errorClass]">

<!-- style 就是一个对象。 -->
<!-- CSS 属性名可以用驼峰式（camelCase）或短横分隔命名 -->
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

<!-- 将多个样式对象应用到一个元素上 -->
<div v-bind:style="[baseStyles, overridingStyles]">
<div :class="[commonClass,item.active ? activeClass : '']">
```

## 十二、组件
组件系统是 Vue.js 另一个重要概念，因为它提供了一种抽象，让我们可以用独立可复用的小组件来构建大型应用。

### 组件声明
``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

### 组件名
组件名不能与H5标签重复，否则组件不起作用。

``` html
<!-- 推荐 -->
<app-header></app-header>
<user-list></user-list>
<range-slider></range-slider>

<!-- 避免 -->
<btn-group></btn-group> <!-- 虽然简短但是可读性差. 使用 `button-group` 替代 -->
<ui-slider></ui-slider> <!-- ui 前缀太过于宽泛，在这里意义不明确 -->
<slider></slider> <!-- 与自定义元素规范不兼容 -->
```

### 全局注册组件
要确保在初始化根实例之前注册了组件，这样就可以在任意地方使用。

``` html
<div id="example">
  <my-component></my-component>
</div>

<script>
  // 全局注册
  Vue.component('my-component', {
    template: '<div>A custom component!</div>'
  })
  // 创建根实例
  new Vue({
    el: '#example'
  })
</script>
```

### 局部注册组件
不必在全局注册每个组件。通过使用组件实例选项注册，可以使组件仅在另一个实例/组件的作用域中可用：

``` js
var Child = {
  template: '<div>Child component!</div>'
};

new Vue({
  el:"#example",
  components: {
    // <my-component> 将只在挂载的 example 上可用
    'my-component': Child
  }
})
```

### 组件构造器
1、 Vue.extend()是Vue构造器的扩展，调用Vue.extend()创建的是一个组件构造器，而不是一个具体的组件实例。

2、 Vue.extend()构造器有一个选项对象，选项对象的template属性用于定义组件要渲染的HTML。

3、 使用Vue.component()注册组件时，第2个参数可以是组件构造器。

``` js
var myComponent = Vue.extend({
  template: '<div>This is my first component!</div>'
});

Vue.component('my-component', myComponent);
```

### DOM 模版解析说明
像 `<ul>` ，`<ol>`，`<table>` ，`<select>` 限制了能被它包裹的元素， 而一些像 `<option>` 这样的元素只能出现在某些其它元素内部，通过添加特殊的 is 属性来包含其他组件。

``` html
<table>
  <!-- my-row 是一个组件 -->
  <tr is="my-row"></tr>
</table>
```

### data 必须是函数
``` js
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  }
})
```

### 组件通信
在 Vue.js 中，父组件通过 props 向下传递数据给子组件，子组件通过 events 给父组件发送消息。

####  Prop
父组件通过 props 向子组件传递数据。

``` html
<!-- 父组件向子组件传递数据 -->
<child my-message="hello!"></child>

<script>
Vue.component('child', {
  // 声明 props
  props: ['myMessage'],
  // 就像 data 一样，prop 可以用在模板内
  // 同样也可以在 vm 实例中像 “this.message” 这样使用
  template: '<span>{{ message }}</span>'
})
</script>
```

> 注意:当使用的不是字符串模版,驼峰式命名的 prop 需要转换为相对应的 kebab-case (短横线隔开式) 命名。

动态地绑定父组件的数据到子模板的 props  需要使用 v-bind 。

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child :my-message="parentMsg"></child>
</div>
```

>> 不应该在子组件内部改变 prop 。如果有需求修改传入的数据，应该将 prop 作为初始值传入，然后在 data 函数中 或者 computed 中处理。

#### Prop 验证
为组件的 props 指定验证规格。如果传入的数据不符合规格，Vue 会发出警告。

类型包括：String、Number、Boolean、Function、Object、Array 或者是 自定义构造器函数。

``` js
Vue.component('example', {
  props: {
    // 基础类型检测 （`null` 意思是任何类型都可以）
    propA: Number,
    // 多种类型
    propB: [String, Number],
    // 必传且是字符串
    propC: {
      type: String,
      required: true
    },
    // 数字，有默认值
    propD: {
      type: Number,
      default: 100
    },
    // 数组／对象的默认值应当由一个工厂函数返回
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

#### 自定义事件
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

#### 给组件绑定原生事件
有时候，可能想在某个组件的根元素上监听一个原生事件。可以使用 .native 修饰 v-on 。

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```

#### 非父子组件通信
有时候两个组件也需要通信(非父子关系)。在简单的场景下，可以使用一个空的 Vue 实例作为中央事件总线：

``` js
var bus = new Vue()

// 触发组件 A 中的事件
bus.$emit('id-selected', 1)

// 在组件 B 创建的钩子中监听事件
bus.$on('id-selected', function (id) {
  // ...
})
```

### slot（内容分发）
假定子组件 my-component 有下面模板：

``` html
<div>
  <h2>我是子组件的标题</h2>
  <slot>
    只有在没有要分发的内容时才会显示。
  </slot>
</div>
```

父组件模版：

``` html
<div>
  <h1>我是父组件的标题</h1>
  <my-component>
    <p>这是一些初始内容</p>
    <p>这是更多的初始内容</p>
  </my-component>
</div>
```

渲染结果：

``` html
<div>
  <h1>我是父组件的标题</h1>
  <div>
    <h2>我是子组件的标题</h2>
    <p>这是一些初始内容</p>
    <p>这是更多的初始内容</p>
  </div>
</div>
```

### 动态组件
#### component
通过使用保留的 `<component>` 元素，动态地绑定到它的 is 特性，我们让多个组件可以使用同一个挂载点，并动态切换：

``` html
<component v-bind:is="currentView">
  <!-- 组件在 vm.currentview 变化时改变！ -->
</component>

<script>
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* 局部组件 */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
</script>
```

#### keep-alive
如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。

``` html
<keep-alive>
  <component :is="currentView">
    <!-- 非活动组件将被缓存！ -->
  </component>
</keep-alive>
```

> 当引入keep-alive 的时候，页面第一次进入，钩子的触发顺序created-> mounted-> activated，退出时触发deactivated。当再次进入（前进或者后退）时，只触发activated。
> 
> 因为再次进入就不在重新解析，而是读取内存中的数据，。只有当数据变化时，才使用VirtualDOM进行diff更新。因而页面进入的数据获取应该在activated中也放一份。数据下载完毕手动操作DOM的部分也应该在activated中执行才会生效，或者不要created部分，直接将created中的代码转移到activated中。

#### activate（钩子）
在切换组件时，在切入前可能需要进行一些异步操作。

``` js
Vue.component('activate-example', { 
  activate: function (done) { 
    var self = this;
    loadDataAsync(function (data) { 
      self.someData = data;
      done();
    }) 
  } 
})
```

> 注意：activate回调只作用于动态组件切换或静态组件初始化渲染的过程中，不作用于使用实例方法手工插入的过程中。

#### transition
作为单个元素/组件的过渡效果。

Props：

* name - string, 用于自动生成 CSS 过渡类名。例如：name: 'fade' 将自动拓展为.fade-enter，.fade-enter-active等。默认类名为 "v"
* appear - boolean, 是否在初始渲染时使用过渡。默认为 false。
* css - boolean, 是否使用 CSS 过渡类。默认为 true。如果设置为 false，将只通过组件事件触发注册的 JavaScript 钩子。
* type - string, 指定过渡事件类型，侦听过渡何时结束。有效值为 "transition" 和 "animation"。默认 Vue.js 将自动检测出持续时间长的为过渡事件类型。
* mode - string, 控制离开/进入的过渡时间序列。有效的模式有 "out-in" 和 "in-out"；默认同时生效。
* enter-class - string
* leave-class - string
* enter-active-class - string
* leave-active-class - string
* appear-class - string
* appear-active-class - string

事件：

* before-enter
* enter
* after-enter
* before-leave
* leave
* after-leave
* before-appear
* appear
* after-appear

``` html
<!-- 简单元素 -->
<transition>
  <div v-if="ok">toggled content</div>
</transition>

<!-- 动态组件 -->
<transition name="fade" mode="out-in" appear>
  <component :is="view"></component>
</transition>

<!-- 事件钩子 -->
<div id="transition-demo">
  <transition @after-enter="transitionComplete">
    <div v-show="ok">toggled content</div>
  </transition>
</div>
```

两种模式：

* in-out：新组件先过渡进入，等它的过渡完成之后当前组件过渡出去。
* out-in：当前组件先过渡出去，等它的过渡完成之后新组件过渡进入。

## 十三、辅助工具
vue-devtools
	
## 十四、手动创建 Vue 的开发环境
vue-loader 是必须的，vue-template-compiler 是 vue-loader 必须的依赖。

``` bash
npm install --save-dev vue-loader vue-template-compiler
```

``` js
//webpack 配置
module: {
  rules: [
    {
      test: /\.vue$/,
      loader: 'vue-loader',
      options: {
        loaders: {
          css: ["vue-style-loader", "css-loader"],
          less: ["vue-style-loader", "css-loader", "postcss-loader", "less-loader"]
        },
        cssSourceMap: true
      }
    },
    //使用vue-style-loader!css-loader!postcss-loader 处理以 css 结尾的文件！
    {
      test: /\.css$/,
      use: [
        "vue-style-loader",
          {
            loader: "css-loader",
	         options: {
	         sourceMap: true
	       }
	     },
	     {
	       loader: "postcss-loader",
	       options: {
	         sourceMap: true
	       }
	     }
	   ]
	 },
	 //使用vue-style-loader!css-loader!postcss-loader处理以less结尾的文件！
    {
      test: /\.less$/,
      use: [
        "vue-style-loader",
        {
          loader: "css-loader",
          options: {
            sourceMap: true
          }
        },
        {
          loader: "less-loader",
          options: {
            sourceMap: true
          }
        },
        {
          loader: "postcss-loader",
          options: {
            sourceMap: true
          }
        }
      ]
    }
  ]
}
```

## 十五、快速搭建环境：vue-cli
Vue.js 提供一个官方命令行工具，可用于快速搭建大型单页应用。

	# 全局安装 vue 的脚手架	
	npm install -g vue-cli
	
	# 查看版本号，判断是否安装成功
	vue -V
	
	# 创建一个基于 webpack 模板的新项目，项目名字叫 my-project
	vue init webpack my-project
	cd my-project
	
	# 安装依赖（这里一定要从官方仓库安装，而不是国内镜像cnpm安装否则会导致后面缺了很多依赖库）
	npm install
	
	# 运行
	npm run dev
	
> 注意，目前提供了4套官方模板：

* browserify
* browserify-simple
* webpack
* webpack-simple（初始是没有vue-router的，需要另外安装）

## 十六、Vue in ES6
### 单文件组件: .vue
这是以 .vue 为后缀的单文件组件。

``` html
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>
</template>
<style>
  .hello {
    color:#ff0;
  }
</style>
<script>
export default {
  data () {
    return {
      msg: 'Hello World!'
    }
  }
}
</script>
```
也可以组合 HTML 模板、以及使用预处理器：

``` html
<template lang="jade">
  div.hello
    h1 {{ msg }}
    other-component
</template>
<style lang="sass" scoped>
  $primary-color:#ff0;
  .hello {
    color:$primary-color;
  }
</style>
<script>
  import OtherComponent from './other-component.vue'
  export default {
    data () {
      return {
        msg: 'Hello World!'
      }
    },
    components:{ OtherComponent }
  }
</script>
```

也可以把代码拆开放:

``` html
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

用props选项来接收父组件传入的数据，渲染组件

``` js
export default {
  props : ['artList']
}
```

## 十七、Vue组件间通信
### 父组件向子组件通信
#### 方法一：props
使用props，父组件可以使用props向子组件传递数据。

``` html
<!-- father.vue -->
<template>
  <child :msg="message"></child>
</template>

<script>
  import child from './child.vue';
  export default {
    components: {
      child
    },
    data () {
      return {
        message: 'father message';
      }
    }
  }
</script>

<!-- child.vue -->
<template>
  <div>{{msg}}</div>
</template>

<script>
  export default {
    props: {
      msg: {
        type: String,
        required: true
      }
    }
  }
</script>
```

#### 方法二 使用`$children`
使用 `$children` 可以在父组件中访问子组件。

### 子组件向父组件通信
#### 方法一:使用 vue 事件

父组件向子组件传递事件方法，子组件通过 `$emit` 触发事件，回调给父组件。

``` html
<!-- father.vue -->
<template>
  <child @msgFunc="func"></child>
</template>

<script>
  import child from './child.vue';

  export default {
    components: {
      child
    },
    methods: {
      func (msg) {
        console.log(msg);
      }
    }
  }
</script>

<!-- child.vue -->
<template>
  <button @click="handleClick">点我</button>
</template>

<script>
  export default {
    props: {
      msg: {
        type: String,
        required: true
      }
    },
    methods () {
      handleClick () {
        //........
        this.$emit('msgFunc');
      }
    }
  }
</script>
```

#### 方法二： 通过修改父组件传递的props来修改父组件数据
这种方法只能在父组件传递一个引用变量时可以使用，字面变量无法达到相应效果。因为引用变量最终无论是父组件中的数据还是子组件得到的 props 中的数据都是指向同一块内存地址，所以修改了子组件中 props 的数据即修改了父组件的数据。

但是并不推荐这么做，并不建议直接修改 props 的值，如果数据是用于显示修改的，在实际开发中我经常会将其放入 data 中，在需要回传给父组件的时候再用事件回传数据。这样做保持了组件独立以及解耦，不会因为使用同一份数据而导致数据流异常混乱，只通过特定的接口传递数据来达到修改数据的目的，而内部数据状态由专门的 data 负责管理。

#### 方法三：使用`$parent`
使用 `$parent` 可以访问父组件的数据。

### 非父子组件、兄弟组件之间的数据传递
非父子组件通信，Vue 官方推荐使用一个 Vue 实例作为中央事件总线。

* `$on` 方法用来监听一个事件。

* `$emit` 用来触发一个事件。

``` js
/*新建一个空的 Vue 实例作为中央事件总嫌*/
let event = new Vue();

/*监听事件*/
event.$on('eventName', (val) => {
  //......do something
});

/*触发事件*/
event.$emit('eventName', 'this is a message.');
```

### 复杂的单页应用数据管理
当应用足够复杂情况下，使用 vuex 进行数据管理。

## 十八、路由
### 安装
	npm install vue-router --save
	
### 基本使用
``` html
<!-- 使用 router-link 组件来导航. -->
<!-- 通过传入 `to` 属性指定链接. -->
<!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
<router-link to="/foo">Go to Foo</router-link>

<!-- 路由匹配到的组件将渲染在这里 -->
<router-view></router-view>
```

### 例子1
``` js
// index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import App from './App'
import Page01 from './components/page01'
import Page02 from './components/page02'
// 也可使用下面的懒加载写法
//const Page01 = (resolve) => require(['./components/page01'],resolve)

Vue.use(VueRouter) //全局安装路由功能

//定义路径
const routes = [
  { path: '/', component: Page01 },
  { path: '/02', component: Page02 },
]

//创建路由对象
const router = new VueRouter({
  routes
})

new Vue({
  el: '#app',
  template: '<App/>',
  components: { App },
  router
})
```
``` html
<!-- App.vue -->
<template>
  <div id="app">
    <!-- router-link 定义跳转路径 -->
    <router-link to="/">01</router-link>
    <router-link to="/02">02</router-link>
    <br/>
    <!-- router-view 来渲染组件 -->
    <router-view></router-view>
  </div>
</template>
```

``` html
<!-- page01.vue -->
<template>
  <div>
    <h1>page02</h1>
  </div>
</template>
```
``` html
<!-- page02.vue -->
<template>
  <div>
    <h1>page02</h1>
  </div>
</template>
```

### 动态路由匹配
一个『路径参数』使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。

``` js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

像 `/user/fo` 和 `/user/bar` 都将映射到相同的路由。

| 模式        | 匹配路径   |  `$route.params`  |
| --------   | :-----:   | :----: |
| `/user/:username`        | `/user/evan`      |   `{ username: 'evan' }`    |
| `/user/:username/post/:post_id`     | `/user/evan/post/123` |   `{ username: 'evan', post_id: 123 }`  |

`$route` 对象：

* `$route.query`
* `$route.hash`
* `$route.path`
* `$route.fullPath`
* `$route.matched`
* `$route.name`

### 监听路由参数的变化
``` js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

### 嵌套路由
``` html
<div id="app">
  <!-- 顶层路由 -->
  <router-view></router-view>
</div>

<script>
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <!-- 子路由 -->
      <router-view></router-view>
    </div>
  `
}

const router = new VueRouter({
  routes: [
    { 
      path: '/user/:id', 
      component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
</script>
```

### 路由拦截
``` js
router.beforeEach((to, from, next) => {
//假设登陆成功后，user信息保存在sessionStorage中。
  if (to.path == '/login') {
    sessionStorage.removeItem('user');
  //如果访问登录页，清空之前sessionStorage中的user信息
  }
  let user = JSON.parse(sessionStorage.getItem('user'));
  if (!user && to.path != '/login') {
    next({ path: '/login' })
  } else {
    next()
  }
//如果访问非登陆页，判断是否有保存的user信息，如果没有，则判断为非法访问，重定向到登录页面
});
```

### http拦截器
拦截器是全局的，拦截器可以在请求发送前和发送请求后做一些处理。拦截器在一些场景下会非常有用，比如请求发送前在 headers 中设置 access_token  ，或者在请求失败时，提供通用的处理方式。

``` js
// http request 拦截器
axios.interceptors.request.use(
    config => {
        if (store.state.token) {  // 判断是否存在token，如果存在的话，则每个http header都加上token
            config.headers.Authorization = `token ${store.state.token}`;
        }
        return config;
    },
    err => {
        return Promise.reject(err);
    });

// http response 拦截器
axios.interceptors.response.use(
    response => {
        return response;
    },
    error => {
        if (error.response) {
            switch (error.response.status) {
                case 401:
                    // 返回 401 清除token信息并跳转到登录页面
                    store.commit(types.LOGOUT);
                    router.replace({
                        path: 'login',
                        query: {redirect: router.currentRoute.fullPath}
                    })
            }
        }
        return Promise.reject(error.response.data)   // 返回接口返回的错误信息
    });
```

### 路由切换动效
``` html
<!--app.vue 根组件-->
<template>
  <div id="app">
        <transition name="fade" mode="out-in">
        <router-view></router-view>
        </transition>
    </div>
</template>
<script>
  export default {
      name: 'app',
    components: {}
  }
</script>
<style>
    .fade-enter-active,.fade-leave-active {
        transition: opacity .2s ease;
    }
    .fade-enter,.fade-leave-active {
        opacity: 0;
    }
</style>
```

### 全局过滤器
一个项目中，可能要用到很多过滤器来处理数据，多个组件公用的，可以注册全局过滤器。单个组件使用的，就挂载到实例 filters 中。

项目做的多了以后，可以整理一套常用的 filters ，不用反复的写。比如：时间等各种操作，数据格式转化，单位换算，部分数据的 md5 加密等...

``` js
//filters.js 过滤器文件
export function formatDateTime (date) {
    //格式化时间戳
  var y = date.getFullYear()
  var m = date.getMonth() + 1
  m = m < 10 ? ('0' + m) : m
  var d = date.getDate()
  d = d < 10 ? ('0' + d) : d
  var h = date.getHours()
  var minute = date.getMinutes()
  minute = minute < 10 ? ('0' + minute) : minute
  return y + '-' + m + '-' + d + ' ' + h + ':' + minute
}
export function test (a) {
   return `${a}aaaa`
}
......
//main.js 入口js文件
import Vue from 'vue'
import * as filters from './filters'

Object.keys(filters).forEach(key => {
  Vue.filter(key, filters[key])
});
```

## 十九、AJAX
### vue-resource(作者已停止更新)

### axios（官网推荐）
#### 使用方法一：
步骤一：

	npm install --save axios

步骤二：

``` js
// 在main.js中引入

import axios from 'axios'
Vue.prototype.$http = axios
```

步骤三：

``` js
// 在组件中使用axios

this.$http.get(URL).then(response => {
  // success callback
}, response => {
  // error callback
})
```

#### 使用方法二：
步骤一：

	npm install --save axios vue-axios

步骤二：

``` js
// 在main.js中引入

import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios, axios)
```

步骤三：

``` js
// 在组件中使用axios

this.axios.get('/api/user').then((response) => {
    console.log(response.data)
}).catch((error) => {
    console.error(error)
})
```

axios更详细的使用方法 => [(官网入口)](https://github.com/mzabriskie/axios)

## 二十、vuex（状态管理）
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

### 安装
	npm install vuex --save
	
``` js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
```

Vuex结构：

``` js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
const store = new Vuex.Store({
  state: {
  },
  actions: {
  },
  mutations: {
  },
  getters: {
  },  
  modules: {
    
  }
})

export default store
```

### Store
"store" 基本上就是一个容器，它包含着你的应用中大部分的状态(state)。

``` js
// 如果在模块化构建系统中，请确保在开头调用了 Vue.use(Vuex)

// 创建 store
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

``` js
// 触发状态变更，这是改变 store 中状态的唯一途径
store.commit('increment')

// 通过 store.state 来获取状态对象
console.log(store.state.count) // -> 1
```

### State
state 定义了应用状态的数据结构，同样可以在这里设置默认的初始状态。

``` js
state: {
  projects: [],
  userProfile: {}
}
```

通过在根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中：

``` js
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```

子组件能通过 `this.$store` 访问到。

``` js
// 在 Vue 组件中从 store 实例中读取状态最简单的方法就是在计算属性中返回
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```

#### mapState 辅助函数
当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 mapState 辅助函数帮助我们生成计算属性，让你少按几次键：

``` js
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

### Getters
Vuex 允许我们在 store 中定义『getters』（可以认为是 store 的计算属性），主要是对于state中数据的一种过滤。Getters 接受 state 作为其第一个参数：

``` js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

Getters 会暴露为 `store.getters` 对象：

``` js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

### Mutations
更改 Vuex 的 store 中的状态的唯一方法是提交 mutation，并且这个过程是同步的。Vuex 中的 mutations 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

``` js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})

// 更改状态
store.commit('increment')
```

#### 提交载荷（Payload）
可以向 `store.commit` 传入额外的参数，即 mutation 的 载荷（payload）：

``` js
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}
store.commit('increment', 10)
```

在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读：

``` js
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
store.commit('increment', {
  amount: 10
})

// or 以对象风格的提交方式
store.commit({
  type: 'increment',
  amount: 10
})
```

#### 在组件中提交 Mutations
可以在组件中使用 `this.$store.commit('xxx')` 提交 mutation，或者使用 mapMutations 辅助函数将组件中的 methods 映射为 `store.commit` 调用（需要在根节点注入 store）。

``` js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment' // 映射 this.increment() 为 this.$store.commit('increment')
    ]),
    ...mapMutations({
      add: 'increment' // 映射 this.add() 为 this.$store.commit('increment')
    })
  }
}
```

### Actions
Action 类似于 mutation，不同在于：

* Action 是触发 mutation 修改数据，而不是直接变更状态。
* Action 可以包含任意异步操作。

常见的例子有从服务端获取数据，在数据获取完成后会调用store.commit()来调用更改 Store 中的状态。可以在组件中使用dispatch来发出 Actions。

``` js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state,n) {
      state.count += n
    }
  },
  actions: {
    loadData (context) {
      this.axios.get(url)
      .then((res) => {
        context.commit('increment',{n:res.data})
      })
      .catch((res) => {
      console.log(err)
      
    }
  }
})
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。

#### 使用 ES2015 的 参数解构 来简化代码：

``` js
actions: {
  incrementActions (context) {
    context.commit('increment')
  }
}

// 简化
actions: {
  incrementActions ({ commit }) {
    commit('increment')
  }
}
```

#### 触发actions的方法：
``` js
store.dispatch('incrementActions')
```

Actions 支持同样的载荷方式和对象方式进行触发：

``` js
// 以载荷形式触发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式触发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

#### 在组件中触发 Action
``` js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment' // 映射 this.increment() 为 this.$store.dispatch('increment')
    ]),
    ...mapActions({
      add: 'increment' // 映射 this.add() 为 this.$store.dispatch('increment')
    })
  }
}
```

### Modules
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

``` js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

#### 模块的局部状态
对于模块内部的 mutation 和 getter，接收的第一个参数是模块的局部状态对象。

``` js
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```

## 二十一 版本更新细则
### Vue v2.5
#### 使用 errorCaptured 钩子来处理组件内异常
在 v2.5 之前，可以用个全局的 config.errorHandler 设置来为应用提供一个处理未知异常的函数，也可以设置 renderError 组件来处理 render 函数内的异常。但是，这些都不能提供一个完整的机制来处理一个单独组件内的异常。

v2.5 中，组件内提供了一个新的钩子函数 errorCaptured，可以捕获该组件内所有子组件树中（不含自身）产生的所有的异常（包括异步调用中的异常），这个钩子函数接收的参数和 errorHandler 一样，可以让开发者更加友好地处理组件内异常。

要利用 errorCaputerd，可以封装一个通用组件，来包含其他的业务组件，来捕获业务组件内的异常，并做对应的展示处理。下面列一个官方给的简单示例，封装一个通用组件(ErrorBoundary)来包含和处理其他业务组件(another component)的异常。

``` js
Vue.component('ErrorBoundary', {
  data: () => ({ error: null }),
  errorCaptured (err, vm, info) {        this.error = `${err.stack}\n\nfound in ${info} of component`    return false
  },
  render (h) {        if (this.error) {            return h('pre', { style: { color: 'red' }}, this.error)
    }        // ignoring edge cases for the sake of demonstration
    return this.$slots.default[0]
  }
})
```

#### 单文件组件支持“函数式组件”
通过 vue-loader v13.3.0 或以上版本，支持在单文件组件内定义一个“函数式组件”，且支持模板编译、作用域 CSS 和 热部署等功能。

函数式组件的定义，需要在 template 标签上定义 functional 属性来声明。且模板内的表达式的执行上下文是 函数式声明上下文，所以要访问组件的属性，需要使用 props.xxx 来获取。简单例子见下：

``` html
<template functional>
  <div>{{ props.msg }}</div>
</template>
```

#### v-on 修饰符
在 Vue v2.5 之前的版本中，如果要在 v-on 中使用没有内置别名的键盘键值，要么直接使用 keyCode 当修饰符（@keyup.13=”foo”），要么需要使用 config.keyCodes 来为键值注册别名。

在 v2.5中，你可以直接使用合法的键值 key 值（参考MDN中的 KeyboardEvent.key）作为修饰符来串联使用它。如下：

``` html
<input @keyup.page-down="onPageDown">
```

上述例子中，事件处理函数只会在 `$event.key === ‘PageDown’` 时被调用。

> 注意：现有键值修饰符仍然可用。在IE9中，一些键值（.esc 和 方向键的 key）不是一致的值，如果要兼容 IE9，需要按 IE9 中内置的别名来处理。

#### 新增 .exact 修饰符
该修饰符应该和其他系统修饰符(.ctrl, .alt, .shift and .meta)结合使用，可用用来区分一些强制多个修饰符结合按下才会触发事件处理函数。如下：

``` html
<!-- 当 Alt 或 Shift 被按下也会触发处理函数 -->
<button @click.ctrl="onClick">A</button>
<!-- 只有当 Ctrl 被按下，才会触发处理函数 -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

#### 简化 Scoped Slots 的使用
之前，如果要在 template 标签上使用 scope 属性定义一个 scoped slot，可以像下面这样定义：

``` html
<comp>
  <template scope="props">
    <div>{{ props.msg }}</div>
  </template>
</comp>
```

在 v2.5 中，scope 属性已被弃用（仍然可用，但是会爆出一个警告），我们使用 slot-scope 属性替代 scope 属性来表示一个 scoped slot，且 slot-scope 属性除了可以被用在 template 上，还可以用在标签元素和组件上。如下：

``` html
<comp>
  <div slot-scope="props">
    {{ props.msg }}
  </div>
</comp>
```

> 注意：这次的调整，表示 slot-scope 已经是一个保留属性了，不能再被单独用在组件属性上了。

#### Inject 新增了默认值选项
本次调整中，Injections 可以作为可选配置，并且可以声明默认值。也可以用 from 来表示原属性。

``` js
export default {
  inject: {
    foo: {
      from: 'bar',      default: 'foo'
    }
  }
}
```

与属性类似，数组和对象的默认值需要使用一个工厂函数返回。

``` js
export default {
  inject: {
    foo: {
      from: 'bar',      default: () => [1, 2, 3]
    }
  }
}
```

## 二十二 vue技巧
### 删除数组索引
在数组中删除一项的标准做法是用 Array.splice(index, 1)

``` js
del (index) {
  this.arr.splice(index ,1);
}
```

在 Vue.js 2.2.0+ 的版本里可以直接用 Vue.delete

``` js
del (index) {
  this.$delete(this.arr, index);
}
```

### 选中 input 框中文字
调用 select 方法即可

``` html
<input @focus="$event.target.select()">
```

组件中调用就需要加上 native 属性了

``` html
<component @focus.native="$event.target.select()"></component>
```

### 子组件修改父组件的数据
在 Vue 中子组件是不允许修改父组件穿进来的值，否则就会引发一个报错，如果需要的只是修改渲染的数据，那么可以 emit 出去，然后在父组件重新赋值，通过双向绑定，触发子组件的再次渲染。

``` js
<!--父组件HTML内容-->
<children-component :value="data" @changeProp="changeData"></children-component>


<!--父组件的js内容-->
export default({
    data(){
        return {
            data : [1,2,3,4]
        }
    },
    methods : {
        changeData(value){
            this.data = value;
        }
    }
})

<!--子组件的js内容-->
export default({
    props : ["value"],
    created : {
        this.$emit("changeProp",[5,6,7,8])
    }
})
```
原理就是 emit 一个数据到父组件上去，然后在父组件中接受到这个传递上来的新值，将 data 赋予新值，然后重新传递到了子组件，起到一个变向修改子组件的效果。

### Vue 给每个组件提供了一个默认的父子传递事件 v-model
vue 为每个组件提供了一个默认 v-model 的语法糖。

``` js
<!--父组件HTML内容-->
<children-component v-mode="data" ></children-component>

<!--父组件的js内容-->
export default({
    data(){
        return {
            data : [1,2,3,4]
        }
    },
    watch : {
        //可以在此处监听子组件传递上来的数据
        data(n,o){
            console.log(n,o);   //[5,6,7,8],[1,2,3,4]
        }
    }
})

<!--子组件的js内容-->

export default({
    <!--大家注意我下面这行代码是打了注释的，说明我在子组件没有定义任何属性-->
    //  props : ["value"]
    <!--并且我接下去直接在代码中使用了this.value(一个完全没有定义过的value)。-->
    created : {
        console.log(this.value);
        this.$emit("input",[5,6,7,8]);  //当我想改变传进来值的时候
    }
})
```

对于这个语法糖的解释。

``` js
//注意，该组件不是表单组件
<children-component :value="data" @input="data = arguments[0]"></children-component>
```

### 全局一次性引用写好的组件
假如我们写好了一些组件，接下去肯定还要引入和使用吧。但是你写了这么多组件，在每个地方一个个引用想要的是一件很麻烦的事情。我们最好是在一个初始的地方一次性全部引入，然后在用的地方直接使用。

``` js
// 在文件开头初始引入所有的组件文件
import b from "./components/common/b.vue"
import c from "./components/common/c.vue"

const components = [b,c];

const install = function (Vue, opts = {}) {
	components.map(component => {
		Vue.component(component.name, component);
	});
};

export default install
```

然后直接在启动的main.js文件里面引入就好：

``` js
import ui from "install.js"
Vue.use(ui);
```

然后你就可以随意在任何一个组件里面直接调用了，比自己之前在每个组件里重复调用要方便一点。

``` js
<b v-model="data1"></b>
<c v-model="data2"></c>
```

### CSS模块化功能
实现css的模块化，避免全局冲突，在单文件组件上，给 style 标签添加 scoped 即可：

``` html
<style lang="scss" scoped></style>
```

这样打包后每个类或者 id 乃至标签都会给自动在 css 后面添加 hash!

## 二十三、问题
### 跨域问题
1、 CORS , 前后端都要对应去配置,IE10+

2、 nginx 反向代理,一劳永逸 <-- 线上环境可以用这个

3、 如果是使用 vue-cli 创建的环境：

``` js
// 在 config 目录下的index.js

proxyTable: {
  "/bp-api": {
    target: "http://new.d.st.cn",
    changeOrigin: true,
    // pathRewrite: {
    //   "^/bp-api": "/"
    // }
  }
}

// target : 就是 api 的代理的实际路径
// changeOrigin: 就是是变源,必须是...
// pathRewrite : 就是路径重定向,一看就知道
```