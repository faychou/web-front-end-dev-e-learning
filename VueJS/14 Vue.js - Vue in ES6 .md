# Vue in ES6
## 单文件组件: .vue
这是以 `.vue` 为后缀的单文件组件。

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

用 props 选项来接收父组件传入的数据，渲染组件：

``` js
export default {
  props : ['artList']
}
```