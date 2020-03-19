# Vue.js 起步
引入Vue.js：

``` html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
```

hello world 示例：

``` html
<div id="app">
  {{ message }}
</div>
```
``` js
  var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
```

el 属性提供一个在页面上存在的 DOM 元素 id，然后在 DOM 节点上就可以使用双大括号 `{{}}` 来绑定数据。