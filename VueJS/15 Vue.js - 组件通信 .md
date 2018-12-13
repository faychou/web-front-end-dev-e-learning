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
    props: {
      msg: {
        type: String,
        required: true,
        default: () => { return 'from child' }
      }
    }
  }
</script>
```

#### 方法二：使用`$children`
在父组件中使用 `$children` 可以访问子组件，但是官网并不推荐。

### 子组件向父组件通信
#### 方法一：使用 vue 事件
子组件通过 events 给父组件发送消息。具体操作方法：父组件向子组件传递事件方法，子组件通过 `$emit` 触发事件，回调给父组件。

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
        console.log(msg);
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
        //........
        this.$emit('msgFunc','子组件数据');
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

#### 方法三： 通过修改父组件传递的 props 来修改父组件数据
这种方法只能在父组件传递一个引用变量时可以使用，字面变量无法达到相应效果。因为引用变量最终无论是父组件中的数据还是子组件得到的 props 中的数据都是指向同一块内存地址，所以修改了子组件中 props 的数据即修改了父组件的数据。

但是并不推荐这么做，并不建议直接修改 props 的值，如果数据是用于显示修改的，在实际开发中我经常会将其放入 data 中，在需要回传给父组件的时候再用事件回传数据。这样做保持了组件独立以及解耦，不会因为使用同一份数据而导致数据流异常混乱，只通过特定的接口传递数据来达到修改数据的目的，而内部数据状态由专门的 data 负责管理。

#### 方法四：使用 `$parent`
在子组件中使用 `$parent` 可以访问父组件，同样的官网并不推荐。

#### 方法五：`$root`
当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。通过访问根组件也能进行数据之间的交互。

#### 方法六：`$attrs` 和 `$listeners`
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

#### 方法七：provide / inject
provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。并且这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

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
  provide: {
    house: '房子',
    car: '车子',
    money: '￥10000'
  },
  components: {
    son
  }
}
</script>

<!-- 子组件 son.vue -->
<template>
  <h2>son</h2>
</template>

<script>
export default {
  inject: {
    house: {
      default: '没房'
    },
    car: {
      default: '没车'
    },
    money: {
      // 长大工作了虽然有点钱
      // 仅供生活费，需要向父母要
      default: '￥4500'
    }
  },
  created () {
    console.log(this.house, this.car, this.money)
    // -> '房子', '车子', '￥10000'
  }
}
</script>
```

## 非父子组件通信
### 空 Vue 实例
有时候两个非父子关系组件也需要通信。在简单的场景下，Vue 官方推荐使用一个空的 Vue 实例作为中央事件总线，把所有的通信数据，事件监听都存储到这个变量上。这样就达到在组件间数据共享了，有点类似于 Vuex。但这种方式只适用于极小的项目，复杂项目还是推荐 Vuex：

``` js
/* 新建一个全局的空 Vue 实例作为中央事件总线 */
let bus = new Vue();

// 在组件 A 创建的钩子中监听事件
bus.$on('id-selected', function (id) {
  // console.log('received: '+ id)
})


// 在组件 B 中触发组件 A 中的事件
bus.$emit('id-selected', 'bus data.')
```

示例：

``` js
<script>
  // 中间组件
  var bus = new Vue()
  // 通信组件
  var vm = new Vue({
    el: '#app',
    components: {
      comB: {
        template: '<p>组件A告诉我：{{msg}}</p>',
        data() {
          return {
            msg: ''
          }
        },
        created() {
          // 给中间组件绑定自定义事件 注意:如果用到this 需要用箭头函数
          bus.$on('tellComB', (msg) => {
            this.msg = msg
          })
        }
      },
      comA: {
        template: '<button @click="emitFn">告诉B</button>',
        methods: {
          emitFn() {
            // 触发中间组件中的自定义事件
            bus.$emit('tellComB', '土豆土豆我是南瓜')
          }
        }
      }
    }
  })
</script>
```

### vuex
当应用足够复杂情况下，使用 vuex 进行数据管理。
