# Vue 数据绑定
Vue.js 使用了基于 HTML 的模版语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。所有 Vue.js 的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。

在底层的实现上， Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。

## Mustache（双大括号）
数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值，Mustache 将会被替代为对应数据的值 ,而且一直会监听该值，一但改变会跟着改变。

``` html
<p>hello {{world}}</p>
<p>{{`hello ${world}`}}</p>
<p v-text="`hello ${world}`"></p>
```

在为标签的属性赋值的时候不能使用 Mustache 语法，我们要用 v-bind 指令。

``` html
<!-- 错误的写法 -->
<div id="{{id}}"></div>

<!-- 正确的写法 -->
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

v-bind 指令可以缩写为一个冒号：

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

### javascript 表达式
迄今为止，在我们的模板中，我们一直都只绑定简单的属性键值。但实际上，对于所有的数据绑定， Vue.js 都提供了完全的 JavaScript 表达式支持。

``` js
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

### 双向绑定
在 Vue.js 中可以使用 `v-model` 指令在表单元素上创建双向数据绑定。

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

### 动态添加数据
只有 data 中的数据才是响应式的，动态添加进来的数据默认为非响应式。可以通过以下方式实现动态添加数据的响应式：

* Vue.set(object, key, value) - 适用于添加单个属性；
* Object.assign() - 适用于添加多个属性。

``` js
var vm = new Vue({
  data: {
    stu: {
      name: 'jack',
      age: 19
    }
  }
})

/* Vue.set */
Vue.set(vm.stu, 'gender', 'male')

/* Object.assign 将参数中的所有对象属性和值 合并到第一个参数 并返回合并后的对象*/
vm.stu = Object.assign({}, vm.stu, { gender: 'female', height: 180 })
```

### 异步 DOM 更新
由于 Vue 异步执行 DOM 更新，监视所有数据改变，一次性更新 DOM。如果需要拿到更新后 dom 中的数据 则需要通过 `Vue.nextTick(callback)` 在 DOM 更新后，执行某个操作（属于 DOM 操作）。

``` js
methods: {
  fn() {
    this.msg = 'change'
    this.$nextTick(function () {
      console.log('$nextTick中打印：', this.$el.children[0].innerText);
    })
    console.log('直接打印：', this.$el.children[0].innerText);
  }
}
```

> 注意：实例调用 `vm.$nextTick(function () {})`。
