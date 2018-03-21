# vuex（状态管理）
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

## 安装
	npm install vuex --save
	
``` js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
```

## API
Vuex 基本结构：

``` js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
const store = new Vuex.Store({
  state: {
  },
  actions: {
  },
  mutations: {
  },
  getters: {
  },  
  modules: {
    
  }
})

export default store
```

### Store
"store" 基本上就是一个容器，它包含着你的应用中大部分的状态(state)。

``` js
// 如果在模块化构建系统中，请确保在开头调用了 Vue.use(Vuex)

// 创建 store
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

``` js
// 触发状态变更，这是改变 store 中状态的唯一途径
store.commit('increment')

// 通过 store.state 来获取状态对象
console.log(store.state.count) // -> 1
```

### state
state 定义了应用状态的数据结构，同样可以在这里设置默认的初始状态。

``` js
state: {
  projects: [],
  userProfile: {}
}
```

通过在根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中：

``` js
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```

子组件能通过 `this.$store` 访问到。

``` js
// 在 Vue 组件中从 store 实例中读取状态最简单的方法就是在计算属性中返回
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```

#### mapState 辅助函数
当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 mapState 辅助函数帮助我们生成计算属性，让你少按几次键：

``` js
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}

/* 不使用mapState */
  computed: { 
    count() { 
	   // 直接转换为一般的计算属性的使用方式
		return this.$store.state.count 
    },
	countAlias() { 
	  // 也是转为一般的计算属性的使用方式，只不过有指定名字的会使用中括号括起来
	  return this.$store.state['count']
	}, 
	countPlusLocalState() { 
	  // 因为是函数，所以会被直接执行，并且传入了当前 store 上的 state 和 getters作为参数
	  //（但这里没使用 getters）
	  return this.$store.state.count + this.localCount 
	} 
  }
```

### Getters
Vuex 允许我们在 store 中定义 getters，就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。Getters 接受 state 作为其第一个参数：

``` js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

Getters 会暴露为 `store.getters` 对象：

``` js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
// 或者可以 this.$store.getters.xxxx 这样使用。
```

``` js
// 可以在第二个参数里面传一个 getter 作为参数
getters: {
  // ...
  doneTodosCount: (state, getters) => {
	  // 传入了之前设置的doneTodos的 getters，所以直接使用了doneTodos
    return getters.doneTodos.length
  }
}

store.getters.doneTodosCount // -> 1

// 让 getter 返回一个函数，来实现给 getter 传参。在你对 store 里的数组进行查询时非常有用。
getters: {
  // ...
  getTodoById: (state) => (id) => { // 返回一个函数
    return state.todos.find(todo => todo.id === id)
  }
}

// 对返回的函数传入参数来使用
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```

### mapGetters 辅助函数
mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性。

### Mutations
更改 Vuex 的 store 中的状态的唯一方法是提交 mutation，并且这个过程是同步的。Vuex 中的 mutations 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

``` js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})

// 更改状态
store.commit('increment')
```

#### 提交载荷（Payload）
可以向 `store.commit` 传入额外的参数，即 mutation 的 载荷（payload）：

``` js
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}
store.commit('increment', 10)
```

在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读：

``` js
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
store.commit('increment', {
  amount: 10
})

// or 以对象风格的提交方式
store.commit({
  type: 'increment',
  amount: 10
})
```

#### 在组件中提交 Mutations
可以在组件中使用 `this.$store.commit('xxx')` 提交 mutation，或者使用 mapMutations 辅助函数将组件中的 methods 映射为 `store.commit` 调用（需要在根节点注入 store）。

``` js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment' // 映射 this.increment() 为 this.$store.commit('increment')
    ]),
    ...mapMutations({
      add: 'increment' // 映射 this.add() 为 this.$store.commit('increment')
    })
  }
}
```

### Actions
Action 类似于 mutation，不同在于：

* Action 是触发 mutation 修改数据，而不是直接变更状态。
* Action 可以包含任意异步操作。

常见的例子有从服务端获取数据，在数据获取完成后会调用 store.commit() 来调用更改 Store 中的状态。可以在组件中使用 dispatch 来发出 Actions。

``` js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state,n) {
      state.count += n
    }
  },
  actions: {
    loadData (context) {
      this.axios.get(url)
      .then((res) => {
        context.commit('increment',{n:res.data})
      })
      .catch((res) => {
      console.log(err)
      
    }
  }
})
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。

#### 使用 ES2015 的 参数解构 来简化代码：

``` js
actions: {
  incrementActions (context) {
    context.commit('increment')
  }
}

// 简化
actions: {
  incrementActions ({ commit }) {
    commit('increment')
  }
}
```

#### 触发 actions 的方法：
``` js
store.dispatch('incrementActions')
```

Actions 支持同样的载荷方式和对象方式进行触发：

``` js
// 以载荷形式触发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式触发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

#### 在组件中触发 Action
``` js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment' // 映射 this.increment() 为 this.$store.dispatch('increment')
    ]),
    ...mapActions({
      add: 'increment' // 映射 this.add() 为 this.$store.dispatch('increment')
    })
  }
}
```

### Modules
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

``` js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

#### 模块的局部状态
对于模块内部的 mutation 和 getter，接收的第一个参数是模块的局部状态对象。

``` js
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```