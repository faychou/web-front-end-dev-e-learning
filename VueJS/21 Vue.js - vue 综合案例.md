# 案例
## 模态框
先看一个基本模态框的雏形：

``` html
<!-- 父组件 -->
<template>
  <div class="hello">
    <button @click="show=true">打开model</button>
    <demo v-model="show"></demo>
  </div>
</template>

<script>
import Demo from './Demo.vue'
export default {
  name: 'hello',
  components: {
    Demo
  },
  data () {
    return {
      show: false
    }
  }
}
</script>


<!-- 子组件 -->
<template>
  <div v-show="value">
    <div>
      <p>这是一个Model框</p>
      <button @click="close">关闭model</button>
    </div>
  </div>
</template>

<script>
export default {
  props: ['value'],
  methods: {
    close () {
      this.$emit('input',false)
    }
  }
}
</script>
```

升级版：

``` html
<!-- 父组件 -->
<template>
  <div class="hello">
    <button @click="show=true">打开model</button>
    <demo v-model="show" ></demo>
  </div>
</template>

<script>
  import Demo from './Demo.vue'
  export default {
    name: 'hello',
    components: {
      Demo
    },
    data () {
      return {
        show: false
      }
    }
  }
</script>


<!-- 子组件 -->
<template>
  <div v-show="show">
    <div>
      <p>这是一个Model框</p>
      <input type="text" v-model="value">
      {{value}}
      <button @click="closeModel">关闭model</button>
    </div>
  </div>
</template>

<script>
export default {
  model: {
    prop: 'show',
    event: 'close'
  },
  props: ['show'],
  data () {
    return {
      value: 10
    }
  },
  methods: {
    closeModel () {
      this.$emit('close',false)
    }
  }
}
</script>
```