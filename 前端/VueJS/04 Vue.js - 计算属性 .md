# Vue 计算属性（computed）
表达式只用于简单的运算，而处理模板语法中的复杂业务逻辑就需要使用计算属性。

所谓计算属性，就是跟 ES5 的 getter 一样的，用 function 来定义个属性，当你获取这个属性的时候，实际上是要执行这个 function，执行 function 的过程就是计算过程，所以也就叫计算属性。所有的计算属性被放在 computed 里面，computed 和 data, methods 同级。

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

> 注意：computed 中的属性不能与 data 中的属性同名，否则会报错。