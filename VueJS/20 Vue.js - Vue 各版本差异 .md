# Vue 各版本更新细则
## Vue v2.5
#### 使用 errorCaptured 钩子来处理组件内异常
在 v2.5 之前，可以用个全局的 config.errorHandler 设置来为应用提供一个处理未知异常的函数，也可以设置 renderError 组件来处理 render 函数内的异常。但是，这些都不能提供一个完整的机制来处理一个单独组件内的异常。

v2.5 中，组件内提供了一个新的钩子函数 errorCaptured，可以捕获该组件内所有子组件树中（不含自身）产生的所有的异常（包括异步调用中的异常），这个钩子函数接收的参数和 errorHandler 一样，可以让开发者更加友好地处理组件内异常。

要利用 errorCaputerd，可以封装一个通用组件，来包含其他的业务组件，来捕获业务组件内的异常，并做对应的展示处理。下面列一个官方给的简单示例，封装一个通用组件(ErrorBoundary)来包含和处理其他业务组件(another component)的异常。

``` js
Vue.component('ErrorBoundary', {
  data: () => ({ error: null }),
  errorCaptured (err, vm, info) {        this.error = `${err.stack}\n\nfound in ${info} of component`    return false
  },
  render (h) {        if (this.error) {            return h('pre', { style: { color: 'red' }}, this.error)
    }        // ignoring edge cases for the sake of demonstration
    return this.$slots.default[0]
  }
})
```

#### 单文件组件支持“函数式组件”
通过 vue-loader v13.3.0 或以上版本，支持在单文件组件内定义一个“函数式组件”，且支持模板编译、作用域 CSS 和 热部署等功能。

函数式组件的定义，需要在 template 标签上定义 functional 属性来声明。且模板内的表达式的执行上下文是 函数式声明上下文，所以要访问组件的属性，需要使用 props.xxx 来获取。简单例子见下：

``` html
<template functional>
  <div>{{ props.msg }}</div>
</template>
```

#### v-on 修饰符
在 Vue v2.5 之前的版本中，如果要在 v-on 中使用没有内置别名的键盘键值，要么直接使用 keyCode 当修饰符（@keyup.13=”foo”），要么需要使用 config.keyCodes 来为键值注册别名。

在 v2.5中，你可以直接使用合法的键值 key 值（参考MDN中的 KeyboardEvent.key）作为修饰符来串联使用它。如下：

``` html
<input @keyup.page-down="onPageDown">
```

上述例子中，事件处理函数只会在 `$event.key === ‘PageDown’` 时被调用。

> 注意：现有键值修饰符仍然可用。在IE9中，一些键值（.esc 和 方向键的 key）不是一致的值，如果要兼容 IE9，需要按 IE9 中内置的别名来处理。

#### 新增 .exact 修饰符
该修饰符应该和其他系统修饰符(.ctrl, .alt, .shift and .meta)结合使用，可用用来区分一些强制多个修饰符结合按下才会触发事件处理函数。如下：

``` html
<!-- 当 Alt 或 Shift 被按下也会触发处理函数 -->
<button @click.ctrl="onClick">A</button>
<!-- 只有当 Ctrl 被按下，才会触发处理函数 -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

#### 简化 Scoped Slots 的使用
之前，如果要在 template 标签上使用 scope 属性定义一个 scoped slot，可以像下面这样定义：

``` html
<comp>
  <template scope="props">
    <div>{{ props.msg }}</div>
  </template>
</comp>
```

在 v2.5 中，scope 属性已被弃用（仍然可用，但是会爆出一个警告），我们使用 slot-scope 属性替代 scope 属性来表示一个 scoped slot，且 slot-scope 属性除了可以被用在 template 上，还可以用在标签元素和组件上。如下：

``` html
<comp>
  <div slot-scope="props">
    {{ props.msg }}
  </div>
</comp>
```

> 注意：这次的调整，表示 slot-scope 已经是一个保留属性了，不能再被单独用在组件属性上了。

#### Inject 新增了默认值选项
本次调整中，Injections 可以作为可选配置，并且可以声明默认值。也可以用 from 来表示原属性。

``` js
export default {
  inject: {
    foo: {
      from: 'bar',      default: 'foo'
    }
  }
}
```

与属性类似，数组和对象的默认值需要使用一个工厂函数返回。

``` js
export default {
  inject: {
    foo: {
      from: 'bar',
      default: () => [1, 2, 3]
    }
  }
}
```