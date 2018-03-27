# Vue 组件
组件系统是 Vue.js 另一个重要概念，因为它提供了一种抽象，让我们可以用独立可复用的小组件来构建大型应用。

### 组件声明
``` js
Vue.component('my-component', {
  props:[],
  template: '<p class="foo bar">Hi</p>',
  render:function() {},
  data:function() {},
  components:{}
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
1、 `Vue.extend()` 是 Vue 构造器的扩展，调用 `Vue.extend()` 创建的是一个组件构造器，而不是一个具体的组件实例。

2、 `Vue.extend()` 构造器有一个选项对象，选项对象的 template 属性用于定义组件要渲染的 HTML。

3、 使用 `Vue.component()` 注册组件时，第 2 个参数可以是组件构造器。

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

###  Prop 属性
在 Vue.js 中，父组件通过 props 向子组件传递数据。

``` html
<!-- 父组件向子组件传递数据 -->
<child my-message="hello!"></child>

<script>
Vue.component('child', {
  // 声明 props
  props: ['myMessage'],
  // 就像 data 一样，prop 可以用在模板内
  // 同样也可以在 vm 实例中像 “this.message” 这样使用
  template: '<span>{{ myMessage }}</span>'
})
</script>
```

> 注意:当使用的不是字符串模版,驼峰式命名的 props 需要转换为相对应的 kebab-case (短横线隔开式) 命名。

动态地绑定父组件的数据到子模板的 props  需要使用 v-bind 。

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child :my-message="parentMsg"></child>
</div>
```

>> 不应该在子组件内部改变 prop 。如果有需求修改传入的数据，应该将 prop 作为初始值传入，然后在 data 函数中 或者 computed 中处理。

### Prop 验证
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

## slot（内容分发）
slot 的作用就是占个位置。假定子组件 my-component 有下面模板：

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

## 动态组件
### component
通过使用 vue 内置的 `<component>` 组件，动态地绑定到它的 is 特性，我们让多个组件可以使用同一个挂载点，并动态切换，如点击 button，随机生成 a、b、c 组件中的一个：

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

还有一个常见的场景就是在注册页面选择邮箱注册还是手机注册，我们知道除了中间的表单内容不一致之外，其他的界面内容是一样的。

``` html
<div>
  <h3>新用户注册</h3>
  <div class="register-type">
    <span :class="{active: registerType === 'mobileForm'}" @click="registerType = 'mobileForm'">手机注册</span>
    <span :class="{active: registerType === 'emailForm'}" @click="registerType = 'emailForm'">邮箱注册</span>
  </div>

  <component :is="registerType" ref="form"></component>

  <input type="button" value="注册" />
  <span>已有账号直接登入</span>
</div>
```

``` js
const mobileForm = {
  template:`
    <div>
      <input type="text" placeholder="请输入手机号" />
      <input type="password" placeholder="请设置密码" />
      <input type="password" placeholder="请确认密码" />
    </div>
  `
}

const emailForm = {
  template:`
    <div>
      <input type="text" placeholder="请输入邮箱地址" />
      <input type="password" placeholder="请设置密码" />
      <input type="password" placeholder="请确认密码" />
    </div>
  `
}

export default {
  data() {
    return {
      registerType: 'mobileForm'
    }
  },
  components:{
    mobileForm,
    emailForm
  }
}
```

### keep-alive
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

### activate（钩子）
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

### refs
在 HTML 元素 中，添加 ref 属性，然后在 JS 中通过 `vm.$refs.属性` 来获取获取组件（或元素）。如果获取的是一个子组件，那么通过 ref 就能获取到子组件中的 data 和 methods 。

``` html
<div id="app">
  <div ref="dv"></div>
  <my res="my"></my>
</div>

<!-- js -->
<script>
  new Vue({
    el : "#app",
    mounted() {
      this.$refs.dv //获取到元素
      this.$refs.my //获取到组件
    },
    components : {
      my : {
        template: `<a>sss</a>`
      }
    }
  })
</script>
```
