# Vue 实例
所有的 vue 程序都需要实例化之后使用，实例主要有两种，一个是 Vue 实例，一个是组件实例。
## 构造器
Vue 实例是作用于某一个 HTML 元素上的，这个元素可以是 HTML 的 body 元素，也可以是指定了 id 的某个元素。每个 vue.js 应用的起步都是通过构造函数 Vue 创建一个 Vue 的根实例:

``` js
var vm = new Vue({
  el:'#app', //实例挂载元素id
  data:{}, //数据
  computed:{}, //计算属性
  methods:{}, // 方法
  watch:{}, //观察
  mouted:{}, //生命周期钩子
  directives:{}, //自定义局部指令
  components:{}, //子组件
  filters:{} //过滤器
})
```

实例化 Vue 时需要传入一个对象，该对象中可以包含以下属性：el（挂载元素）、data（数据）、template（模板）、method（方法）、生命周期钩子等。

``` js
const vm = new Vue({
  data:{
    message:'hello vue'
 }
});

const app = new Vue({
  // 用 render 函数渲染引入的组件 App 到 index.html 中的 #app 元素中
  render: h => h(App)
}).$mount('#app')


// new Vue({
//   el:'#app',
//   render: h => h(App)
// })
```

## 组件构造器
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
数据都要绑定要 data 属性，方法都要绑定到 methods 方法，每个 Vue 实例都会代理其 data 对象里所有的属性：

``` js
var data = { a: 1 }
var vm = new Vue({ 
  data: data // 这里的 data 是被代理的属性
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
