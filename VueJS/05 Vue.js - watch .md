# watch
watch 是和 data, methods, computed 同级的一个参数，你可以在 watch 里面规定你要观察的属性，当这些属性发生变化的时候，它会执行对应的那个函数。

``` js
new Vue({
  data: { a: 1, b: { age: 10 } },
  watch: {
    a: function(val, oldVal) {
      // val 表示当前值
      // oldVal 表示旧值
      console.log('当前值为：' + val, '旧值为：' + oldVal)
    },

    // 监听对象属性的变化
    b: {
      handler: function (val, oldVal) { /* ... */ },
      // deep : true表示是否监听对象内部属性值的变化 
      deep: true
    }
  }
})
```

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