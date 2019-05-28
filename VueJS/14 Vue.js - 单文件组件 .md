# Vue in ES6
## 单文件组件
这是一个自定义的文件类型，用类 HTML 语法描述一个 Vue 组件。每个 `.vue` 文件包含三种类型的顶级语言块 `<template>`、`<script>` 和 `<style>`，还允许添加可选的自定义块。

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
  name: 'app',
  data () {
    return {
      msg: 'Hello World!'
    }
  }
}
</script>
```

* 每个 `.vue` 文件最多包含一个 `<template>`，默认语言：html ；
* 每个 `.vue` 文件最多包含一个 `<script>`，默认语言：js；
* 一个 `.vue` 文件可以包含多个 `<style>` 标签，默认语言：css；
* 在语言块中使用该语言块对应的注释语法 (HTML、CSS、JavaScript、Jade 等)。

> 注意：data 的书写方式不同。在 .vue 组件中, data 必须是一个函数，它 return 一个对象。

### 父子组件
在子组件中需要通过 props 显示定义需要从父组件中接收的数据。该数据可以是各种数据类型，同样也可以传递一个函数。

``` html
<!-- 父组件 -->
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <child-component :msg='msgData' :fn = 'handlerFn'></child-component>
  </div>
</template>

<script>
  import ChildComponent from './child-component.vue'
  export default {
    data () {
      return {
        msg: 'Hello World!',
        msgData: '父组件传递的数据'
      }
    },
    components:{ ChildComponent },
    methods: {
      handlerFn () {
        console.log('vue');
      }
    }
  }
</script>
```

``` html
<!-- 子组件 -->
<template>
  <div>
    <p>{{msg}}</p>
    <button @click='fn'>按钮</button>
  </div>
</template>

<script>
export default {
  name: 'ChildComponent',
  props: [ 'msg','fn' ]
}
</script>
```

### 使用 HTML 模版
``` html
<template lang="jade">
  div.hello
    h1 {{ msg }}
    other-component
</template>
```

### 使用预处理器
``` html
<style lang="sass">
  <!-- write Sass! -->
</style>
```

具有不同封装模式的多个 `<style>` 标签可以在同一个组件中混合使用。

### CSS 作用域
当 `<style>` 标签有 scoped 属性时，它的 CSS 只作用于当前组件中的元素。

``` html
<style>
/* 全局样式 */
</style>

<style scoped>
/* 本地样式 */
</style>
```

> 注意：可以在一个组件中同时使用有作用域和无作用域的样式。

使用 scoped 后，父组件的样式将不会渗透到子组件中。不过一个子组件的根节点会同时受其父组件有作用域的 CSS 和子组件有作用域的 CSS 的影响。

如果你希望 scoped 样式中的一个选择器能够作用得“更深”，例如影响子组件，你可以使用 `>>>` 操作符：

``` html
<style scoped>
  .a >>> .b { /* ... */ }
</style>
```

有些像 Sass 之类的预处理器无法正确解析 `>>>`。这种情况下你可以使用 `/deep/` 操作符取而代之。

### CSS Modules
CSS Modules是一个用于模块化和组合 CSS 的流行系统，可以作为模拟 CSS 作用域的替代方案。

``` html
<style module>
  .red {
    color: red;
  }
  .bold {
    font-weight: bold;
  }
</style>
```

生成的 CSS 对象将为组件注入一个名叫 `$style` 的计算属性，你可以在你的模块中使用动态 class 绑定：

``` html
<template>
  <p :class="$style.red">
    This should be red
  </p>
</template>
```

也可以在 JavaScript 访问它：

``` js
export default {
  created () {
    console.log(this.$style.red)
  }
}
```

在 `.vue` 中你可以定义不止一个 `<style>`，为了避免被覆盖，你可以通过设置 module 属性来为它们定义注入后计算属性的名称：

``` html
<style module="a">
  /* identifiers injected as a */
</style>

<style module="b">
  /* identifiers injected as b */
</style>
```

### 代码拆分
通过 src 属性导入外部文件，相对路径需要以 `./` 开始：

``` html
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

### props
用 props 选项来接收父组件传入的数据，注意父组件元素中的属性在这里必须转化为驼峰式大小写：

``` js
export default {
  props : ['artList']
}
```

为了明确表达出所需要的 props 类型，一般将上述的数组写法改为下列的 props 验证方式：

``` js
export default {
  props: {
    artList: {
      type: String,
      required: true,
      default: ''
    }
  }
}
```

### 计算属性
``` js
export default {
  computed: {
    listeners () {
      return {
        //
      }
    }
  }
}
```

### 方法
``` js
export default {
  methods: {
    addTodo () {
      //
	 }
  }
}
```

## 函数式组件
函数式组件，即无状态，无法实例化，内部没有任何生命周期处理方法，非常轻量，因而渲染性能高，特别适合用来只依赖外部数据传递而变化的组件。

要声明一个应该编译为函数式组件的模板，请将 functional 特性添加到模板块中。这样做以后就可以省略 `<script>` 块中的 functional 选项。模板中的表达式会在函数式渲染上下文中求值。这意味着在模板中，props 需要以 `props.xxx` 的形式访问：

``` jsx
// App.vue 
<template>
  <div id="app">
    <List
      :items="['Wonderwoman', 'Ironman']"
      :item-click="item => (clicked = item)"
    />
    <p>Clicked hero: {{ clicked }}</p>
  </div>
</template>

<script>
import List from "./List";

export default {
  name: "App",
  data: () => ({ clicked: "" }),
  components: { List }
};
</script>

// List.vue 函数式组件 
<template functional>
  <div>
    <p v-for="item in props.items" @click="props.itemClick(item);">
      {{ item }}
    </p>
  </div>
</template>
```
