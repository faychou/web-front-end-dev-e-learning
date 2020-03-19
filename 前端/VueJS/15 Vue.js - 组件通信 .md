# Vue 组件通信
## 父子组件通信
### 父组件向子组件通信
#### 方法一：props
在 Vue.js 中，父组件通过 props 向下传递数据给子组件。

``` html
<!-- father.vue -->
<template>
  <child :msg="message"></child>
</template>

<script>
  import child from './child.vue';
  export default {
    components: {
      child
    },
    data () {
      return {
        message: 'father message';
      }
    }
  }
</script>

<!-- child.vue -->
<template>
  <div>{{ msg }}</div>
</template>

<script>
  export default {
    props:[ 'msg']
  }
</script>
```

#### 方法二：使用`$children`
在父组件中使用 `$children` 可以访问子组件，但是官网并不推荐。

``` html
<!-- 父组件 -->
<template>
  <div @click="handleClick" class="home">
    <my-comp v-for="msg in msgs" :key="msg.id" :msg="msg"></my-comp>
  </div>
</template>

<script>
import MyComp from '@/components/MyComp.vue'

export default {
  // ...
  data () {
    return {
      msgs: [{
          // ...
      }]
    }
  },
  methods: {
    handleClick () {
      this.$children.forEach(child => {
        child.$data.colored = !child.$data.colored // 逐一控制子组件的 $data
      })
    }
  }
}
</script>
```

``` js
// 子组件
export default {
  name: 'MyComp',
  data () {
    return {
      colored: false // colored 状态
    }
  },
  computed: {
    color () {
      return this.colored ? 'red' : 'black'
    }
  },
  props: ['msg']
}
```

### 子组件向父组件通信
#### 方法一：使用 vue 事件
父组件向子组件传递事件方法，子组件通过 `$emit` 触发事件，回调给父组件。

``` html
<!-- father.vue -->
<template>
  <child @msgFunc="func"></child>
</template>

<script>
  import child from './child.vue';

  export default {
    components: {
      child
    },
    methods: {
      func (msg) {
        console.log('来自于子组件的数据：',msg);
      }
    }
  }
</script>

<!-- child.vue -->
<template>
  <button @click="handleClick">点我</button>
</template>

<script>
  export default {
    props: {},
    methods () {
      handleClick () {
        this.$emit('msgFunc','子组件回调给父组件的数据');
      }
    }
  }
</script>
```

#### 方法二：.sync 修饰符
在 vue@1.x 的时候曾作为双向绑定功能存在，即子组件可以修改父组件中的值。因为它违反了单向数据流的设计理念，所以在 vue@2.0 的时候被干掉了。但是在 vue@2.3.0+ 以上版本又重新引入了这个 .sync 修饰符。但是这次它只是作为一个编译时的语法糖存在。它会被扩展为一个自动更新父组件属性的 v-on 监听器。也就是说让我们手动进行更新父组件中的值了，从而使数据改动来源更加的明显。

``` html
<!-- 父组件：father.vue -->
<template>
  <div id="app">
    <login :name.sync="userName"></login>
    {{ userName }}
  </div>
</template>

<script>
import child from './child.vue';

export default {
  data() {
    return {
      userName: ''
    }
  },
  components: {
    login
  }
}
</script>

<!-- 子组件 login.vue -->
<template>
  <div class="input-group">
    <label>姓名:</label>
    <input v-model="text">
  </div>
</template>

<script>
export default {
  data() {
    return {
      text: ''
    }
  },
  props: ['name'],
  watch: {
    text (newVal) {
      this.$emit('update:name', newVal)
    }
  }
}
</script>
```

#### 方法三：使用 `$parent`
在子组件中使用 `$parent` 可以访问父组件，同样的官网并不推荐。

``` html
<template>
  <div class="home">
    <my-comp v-for="msg in msgs" :key="msg.id" :msg="msg" :colored="colored"></my-comp>
  </div>
</template>

<script>
import MyComp from '@/components/MyComp.vue'

export default {
  name: 'home',
  components: {
    MyComp
  },
  data () {
    return {
      colored: false, // 父组件维护一个 colored 状态
      msgs: [{
          // ...
      }]
    }
  }
}
</script>

<!-- 子组件 -->
<template>
  <div>
    <div @click="handleClick" :style="{color}">
      {{msg.id}} - {{msg.data}} ⭕
    </div>
  </div>
</template>

<script>
export default {
    // ...
  props: ['msg', 'colored'],
  methods: {
    handleClick (e) {
      this.$parent.$data.colored = !this.$parent.$data.colored
    }
  }
}
</script>
```

#### 方法四：`$root`
当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。通过访问根组件也能进行数据之间的交互。

``` js
// main.js

new Vue({
  data () {
    return { // 将数据存储在 Vue 实例
      colored: false
    }
  },
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

``` js
// 其他组件
export default {
  name: 'MyComp',
  // ...
  mounted () {
    console.log(this.$root) // 直接访问到根组件
  },
  // ...
}
```

#### 方法五：`$attrs` 和 `$listeners`
`$attrs` ：包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。

`$listeners` ：包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件——在创建更高层次的组件时非常有用。

其实就是说 `$attrs` 和 `$listeners` 属性像两个收纳箱，一个负责收纳属性，一个负责收纳事件，都是以对象的形式来保存数据。

``` html
<!-- father.vue -->
<template>
  <div id="app">
	 <child 
	   :foo="foo" 
	   :bar="bar"
	   @one.native="triggerOne"
	   @two="triggerTwo">
	 </child>
  </div>
</template>

<script>
import child from './child.vue';

export default {
  data() {
    return {
      foo: 'parent foo',
      bar: 'parent bar'
    }
  },
  components: {
    Child
  },
  methods: {
    triggerOne () {
      alert('one')
    },
    triggerTwo () {
      alert('two')
    }
  }
}
</script>

<!-- 子组件 Child.vue -->
<template>
  <h2>{{ foo }}</h2>
</template>

<script>
export default {
  data() {
    return {
      text: ''
    }
  },
  props: ['foo'],
  inheritAttrs: false, // 默认是 true（去掉渲染后 html 上的非 props 属性）
  created () {
    console.log(this.$attrs, this.$listeners)
    // -> {bar: "parent bar"}
    // -> {two: fn}
    
    // 这里我们访问父组件中的 `triggerTwo` 方法
    this.$listeners.two()
    // -> 'two'
  }
}
</script>
```

#### 方法六：provide / inject
适用于祖先和后代关系的组件间的通信，祖先元素通过 provide 提供一个值，后代元素无论嵌套多深都可通过 inject 获取到这个值。但是 provide 和 inject 主要为高阶插件/组件库提供用例，并不推荐直接用于应用程序代码中。

provide 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。

``` html
<!-- 父组件：father.vue -->
<template>
  <div id="app">
    <son></son>
  </div>
</template>

<script>
import son from './son.vue';

export default {
  provide(){ 
    return {
      foo: 'bar'
    }
  },
  components: {
    son
  }
}
</script>
```

inject 选项应该是一个字符串数组或者一个对象：

``` html
<!-- 子组件 son.vue -->
<template>
  <h2>son</h2>
</template>

<script>
export default {
  inject: ['foo'],
  created () {
    console.log(this.foo)
  }
}
</script>
```

>> 注意：provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

## 非父子组件通信
### 中央事件总线
有时候两个非父子关系组件也需要通信。在简单的场景下，Vue 官方推荐使用一个空的 Vue 实例作为中央事件总线，把所有的通信数据，事件监听都存储到这个变量上。这样就达到在组件间数据共享了，有点类似于 Vuex。但这种方式只适用于极小的项目，复杂项目还是推荐 Vuex：

``` js
// src/eventBus.js
import Vue from 'vue'

let bus = new Vue() // 新建一个全局的空 Vue 实例作为中央事件总线

export default bus
```

``` js
// src/component/A.vue
import bus from '@/eventbus'

export default {
    // ...
  mounted () {
    // A 组件中监听事件
    bus.$on('change-color', () => {
      this.colored = !this.colored
    })
  }
}
```

``` js
// src/component/B.vue
import bus from '@/eventbus'

export default {
    // ...
  methods: {
    handleClick (e) {
      // B 组件中触发
      bus.$emit('change-color')
    }
  }
}

```

>> 缺点：需要在组件销毁时，手动清除事件监听。

### Observable
vue.js 2.6新增加的Observable API ，通过使用这个api我们可以应对一些简单的跨组件数据状态共享的情况。

第一步、创建一个 store.js，包含一个 store 和一个 mutations，分别用来指向数据和处理方法：

``` js
import Vue from "vue"

export const store = Vue.observable({ count: 0 })

export const mutations = {
  setCount(count) {
    store.count = count
  }
}
```

第二步、然后在App.vue里面引入这个store.js，在组件里面使用引入的数据和方法：

``` jsx
<template>
  <div id="app">
    <img width="25%" src="./assets/logo.png">
    <p>count:{{count}}</p>
    <button @click="setCount(count+1)">+1</button>
    <button @click="setCount(count-1)">-1</button>
  </div>
</template>

<script>
import { store, mutations } from "./store";
export default {
  name: "App",
  computed: {
    count() {
      return store.count;
    }
  },
  methods: {
    setCount: mutations.setCount
  }
};
</script>
```

### vuex
当应用足够复杂情况下，使用 vuex 进行数据管理。
