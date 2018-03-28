# 组件异步加载
在实际的项目中，如果不使用异步加载，则 Vue.js 组件的 JS、CSS 和模板都会打包到一个 .js 文件中，这个文件可能达到几 MB 甚至更多，严重影响首屏加载时间。所以在项目中我们需要启用组件的异步加载。

### 方式一：
``` js
Vue.component(
  'async-demo',
  // 该 import 函数返回一个 Promise 对象。
  () => import('./async-demo')
)

//异步子组件
Vue.component('parent-demo', {
  // 父组件的其他选项
  components: {
    'async-demo': () => import('./async-demo')
  }
})
```

### 方式二：
针对 webpack2 以下：

``` js
Vue.component('async-demo', function(resolve) {
  require.ensure([], function(require) {
    resolve(require('./async-demo'))
  }, function(error) {
    // 加载出错执行这里
  })
})
```

## 异步组件生命周期控制
在异步子组件中，mounted 函数中是无法获取到子组件的实例的，所以我们需要一些技巧来实现这个功能。

``` html
<template>
  <div>
    <my-demo ref="demo"></my-demo>
  </div>
</template>

<script>
export default {
  components: {
    MyDemo: () => import('./Demo').then(component => {
      // 清理已缓存的组件定义
      component.default._Ctor = {}

      if (!component.default.attached) {
        // 保存原组件中的 created 生命周期函数
        component.default.backupCreated = component.default.created
      }

      // 注入一个特殊的 created 生命周期函数
      component.default.created = function() {
        // 子组件已经实例化完毕

        // this 即为子组件 vm 实例
        console.log(this)

        if (component.default.backupCreated) {
          // 执行原组件中的 created 生命周期函数
          component.default.backupCreated.call(this)
        }
      }

      // 表示已经注入过了 
      component.default.attached = true

      return component
    })
  }
}
</script>
```
