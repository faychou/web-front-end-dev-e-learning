# vuex（状态管理）
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

## 初识
``` bsh
# 安装
npm install vuex --save
```

``` html
<div id="app">
  <p>{{ count }}</p>
  <p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
  </p>
</div>
```

``` js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex) // Vuex 注册到 Vue 中

// 创建一个 store
const store = new Vuex.Store({
  // 初始化 state   
  state: {
    count: 0
  },
  // 改变状态唯一声明处
  mutations: {
  	 increment: state => state.count++,
    decrement: state => state.count--
  }
});

new Vue({
  el: '#app',
  // 从根组件将 store 的实例注入到所有的子组件  
  store,  
  computed: {
    count () {
      // Vuex 的状态存储是响应式的，从 store 实例中读取状态最简单的方法就是在计算属性中返回某个状态,
      // 每当状态 count 发生变化，都会重新求取计算
	   return this.$store.state.count
    }
  },
  methods: {
    increment () {
      this.$store.commit('increment')
    },
    decrement () {
    	this.$store.commit('decrement')
    }
  }
});
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

### 一、Store
Store 基本上就是一个容器，包含着应用中大部分的状态，其中至少要注入两项，state 和 mutation。

``` js
// 如果在模块化构建系统中，请确保在开头调用了 Vue.use(Vuex)

// 创建 store
const store = new Vuex.Store({
  // 初始状态
  state: {
    count: 0
  },
  // 改变状态的规则
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

``` js
// 触发状态变更，这是改变 state 的唯一途径
store.commit('increment'); //组件中使用：this.$store.commit('increment');

// 通过 store.state 来获取状态对象
console.log(store.state.count); // 组件中使用：this.$store.state.count
```

### 二、state
state 其实就是根据项目的需求，自己定义的一个应用状态的数据结构，同样可以在这里设置默认的初始状态。

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

### 三、Getters
Getters 相当于从数据库里获取数据的 API，由于是取数据，所以 getters 是一个“纯函数”，也就是不会对原数据造成影响的函数。

Vuex 允许我们在 store 中定义 getters，就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。也是就将直接获取到 State 后在 computed 里进行再次的过滤、包装逻辑统统提取出放到 Getter 里进行，提高了代码的复用性、可读性。Getters 接受 state 作为其第一个参数：

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
    },
    getReverseName: state => {
      return state.name.split('').reverse().join('')
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

#### 在组件中使用 getter
``` js
this.$store.getters.doneTodosCount
```

#### mapGetters 辅助函数
mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性。和前面 mapState 辅助函数作用和使用上基本相同。

``` js
import { mapGetters } from 'vuex'

// getter 名称和 计算属性名称相同的情况下，可以传递字符串数组

export default {
  // ...
  computed: {
  	...mapGetters([
    	'doneTodos'
    ])
  }
}

// 传递对象的方式

export default {
  // ...
  computed: {
  	...mapGetters({
    	doneTodos: 'doneTodos',
        getTodoById: 'getTodoById' // 此处传递回来的是一个函数，所以在使用的时候 => {{ getTodoById(2) }}
    })
  }
}
```

### 四、Mutations
这是更改 state 的唯一方法，并且这个过程是同步的。说白了 Mutations 就类似于把数据存入数据库的 API。

Vuex 中的 mutations 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

``` js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: { //同步
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

#### mapMutation
使用方式跟 mapState 和 mapGetters 基本相同。

``` js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    // 传递字符串数组，同名哦～  
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
      
    // 传递对象  
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

### 五、Actions
Action 类似于 mutation，不同在于：

* Action 是触发 mutation 修改数据，而不是直接变更状态。
* Action 可以包含任意异步操作。

常见的例子有从服务端获取数据，在数据获取完成后会调用 store.commit() 方法来触发 mutation 修改数据，然后 Actions 可以在组件中使用 dispatch 来发出。

``` js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: { //同步
    increment (state,n) {
      state.count += n
    }
  },
  actions: { //异步
    incrementActions (context) {
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
store.dispatch('incrementActions', {
  amount: 10
})

// 以对象形式触发
store.dispatch({
  type: 'incrementActions',
  amount: 10
})
```

#### 在组件中触发 Action
``` js
this.$store.dispatch('incrementActions')
```

#### mapActions
和 mapMutions 使用方式基本一致。

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

#### 组合 Action
Action 通常是异步的，所以会很难控制结束时间，此时就可以借助返回一个 Promise 对象来实现，更重要的是，还能够组合多个 action，以处理更加复杂的异步流程：

``` js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('mutationA')
        resolve()
      }, 1000);
    });
  }
}

// 使用
store.dispatch('actionA').then(() => {
  // ...
})
```

当然也可以使用 async / await 来实现：

``` js
actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData());
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData());
  }
}
```

### 六、Modules
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

##### 注意：
* 模块内部的 mutation 和 getter，接收的第一个参数 state 是模块的局部状态对象；

* 模块内部的 action，局部状态通过 context.state 暴露出来，根节点状态则为 context.rootState；

* 模块内部的 getter，根节点状态会作为第三个参数暴露出来。

#### 命名空间
默认情况下，模块内部的 action、mutation 和 getter 是注册在全局命名空间的——这样使得多个模块能够对同一 mutation 或 action 作出响应，所以必须防止模块里属性或方法重名。
为了模块具有更高的封装度、复用性和独立性，可以通过添加 namespaced: true 的方式使其成为带命名空间的模块。在调用上也就需要添加上声明 getter、action 及 mutation 到底属于那个模块了，以路径的形式表示属于那个模块。

``` js
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true, // 开启命名空间

      // 模块内容（module assets）
      state: { ... }, // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin'] 调用时以路径的形式表明归属
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: { ... },
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: { ... },
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
});
```

带命名空间的模块内访问全局 state 、getter 和 action， rootState和 rootGetter会作为第三和第四参数传入 getter，也会通过 context 对象的属性传入 action。

需要在全局命名空间内分发 action 或提交 mutation，将 { root: true } 作为第三参数传给 dispatch 或 commit 即可。

``` js
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 全局的 state 和 getters 可以作为第三、四个参数进行传入，从而访问全局 state 和 getters
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

需要在带命名空间的模块注册全局 action，你可添加 root: true，并将这个 action 的定义放在函数 handler 中。

``` js
{
  actions: {
    someOtherAction ({dispatch}) {
      dispatch('someAction')
    }
  },
  modules: {
    foo: {
      namespaced: true,

      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}
```

使用带命名空间模块里的辅助函数时，将模块的空间名称作为第一个参数传递给上述函数，这样所有绑定都会自动将该模块作为上下文。

``` js
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo',
    'bar'
  ])
}
```

还可以通过使用 createNamespacedHelpers 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：

``` js
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

#### 模块动态注册
在 store 创建之后，你可以使用 store.registerModule 方法注册模块：

``` js
// 注册模块 `myModule`
store.registerModule('myModule', {
  // ...
})
// 注册嵌套模块 `nested/myModule`
store.registerModule(['nested', 'myModule'], {
  // ...
});
```

模块动态注册功能使得其他 Vue 插件可以通过在 store 中附加新模块的方式来使用 Vuex 管理状态。例如，vuex-router-sync 插件就是通过动态注册模块将 vue-router 和 vuex 结合在一起，实现应用的路由状态管理。

你也可以使用 store.unregisterModule(moduleName)来动态卸载模块。注意，你不能使用此方法卸载静态模块（即创建 store 时声明的模块）。
