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
        required: true
      }
    }
  }
</script>
```

#### 方法二：使用`$children`
使用 `$children` 可以在父组件中访问子组件。

### 子组件向父组件通信
#### 方法一:使用 vue 事件
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
    props: {
      msg: {
        type: String,
        required: true
      }
    },
    methods () {
      handleClick () {
        //........
        this.$emit('msgFunc');
      }
    }
  }
</script>
```

#### 方法二： 通过修改父组件传递的 props 来修改父组件数据
这种方法只能在父组件传递一个引用变量时可以使用，字面变量无法达到相应效果。因为引用变量最终无论是父组件中的数据还是子组件得到的 props 中的数据都是指向同一块内存地址，所以修改了子组件中 props 的数据即修改了父组件的数据。

但是并不推荐这么做，并不建议直接修改 props 的值，如果数据是用于显示修改的，在实际开发中我经常会将其放入 data 中，在需要回传给父组件的时候再用事件回传数据。这样做保持了组件独立以及解耦，不会因为使用同一份数据而导致数据流异常混乱，只通过特定的接口传递数据来达到修改数据的目的，而内部数据状态由专门的 data 负责管理。

#### 方法三：使用 `$parent`
使用 `$parent` 可以访问父组件的数据。

## 非父子组件通信
### 空 Vue 实例
有时候两个非父子关系组件也需要通信。在简单的场景下，Vue 官方推荐使用一个空的 Vue 实例作为中央事件总线：

``` js
/* 新建一个空的 Vue 实例作为中央事件总线 */
var bus = new Vue()

// 在组件 B 创建的钩子中监听事件
bus.$on('id-selected', function (id) {
  // ...
})


// 触发组件 A 中的事件
bus.$emit('id-selected', 1)
```

示例：

``` js
<!-- 组件A： -->
<com-a></com-a>
<!-- 组件B： -->
<com-b></com-b>

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
