# Vue watch
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