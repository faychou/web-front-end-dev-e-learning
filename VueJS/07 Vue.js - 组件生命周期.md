# 生命周期
从组件被创建，到组件挂载到页面上运行，再到页面关闭组件被卸载，总是伴随着组件各种各样的事件，这些事件，统称为组件的生命周期钩子函数。在这个过程中，组件会自动调用这些生命周期钩子函数，给予用户机会在一些特定的场景下执行自定义的逻辑。

与 react 中监听 props 和 state 属性变化不同的是，在 vue 中是只监听 data 属性的变化。

![生命周期](https://cn.vuejs.org/images/lifecycle.png)

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  beforeCreate:function(){}, // 组件实例化之前，无法获取 data 中的数据、methods 中的方法
  created: function () { // 实例创建后调用，可以改变 data 中的数据，常在这个阶段发起请求
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  },
  beforeMount:function(){}, // 开始执行挂载，但是组件还写入 dom
  mounted:function(){ // 实例已经挂载到页面，可以进行 DOM 操作，只执行一次
    console.log(this.$children);
    console.log(this.$refs);
  },
  beforeUpdate:function(){}, // 组件更新前
  updated:function(){}, // 组件已经更新
  beforeDestroy:function(){}, // 组件销毁之前，实例仍然完全可用，常用：清除定时器等清理任务
  destroyed:function(){} // 组件已经销毁
})
// -> "a is: 1"
```

> 注意：钩子函数的名称都是Vue中规定好的！

像 AJAX 请求，根据实际情况，一般在 created 里面就可以，如果涉及到需要页面加载完成之后的话就用 mounted。