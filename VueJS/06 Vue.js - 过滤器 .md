# Vue 过滤器
用来对文本数据格式化，一般用在 `{{}}` 和 `v-bind` 表达式。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符指示:

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

### 局部过滤器
局部过滤器是在某一个 vue 实例的内容创建的，只在当前实例中起作用。过滤器函数总接受表达式的值作为第一个参数。

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

Vue2.0 把过滤器这块移除是有道理的，他们希望你更多使用 computed，而不是过滤器。

### 全局过滤器
通过全局方式创建的过滤器，在任何一个 vue 实例中都可以使用。

``` js
Vue.filter('filterName', function (value) {
  // value 表示要过滤的内容
})
```

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