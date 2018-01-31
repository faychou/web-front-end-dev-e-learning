# redux（状态管理）

### （一）、原则
* 单向数据流；
* state 是只读的；
* 使用纯函数进行更改。

### （二）、环境安装
``` bash
npm install redux --save
```

### （三）、概念
#### Store
Store 是一个对象，用来存放整个应用的 state，相当于一个保存数据的容器，且整个应用只能有一个 Store。

``` javascript
import { createStore } from 'redux'
const store = createStore(reducer)
```
这里的 createStore 函数只能被调用一次，createStore 方法还可以接受第二个参数，表示 State 的最初状态。注意，如果提供了这个参数，它会覆盖 Reducer 函数的默认初始值。

#### State
当前时刻的 State，可以通过store.getState()拿到：

``` javascript
import { createStore } from 'redux'
const store = createStore(fn)
const state = store.getState()
```

#### Action
是一个描述事件的简单对象，它是改变 store 中 state 的唯一方法。其中的 type 属性是必须的，表示将要执行的动作，其他属性可以自由设置。

``` javascript
const action = {
    type: 'ADD_TODO',
    payload: 'Learn Redux'
}
```

在实际项目中，action 的值很少会是一个常量，所以可以为每个 action 创建一个工厂方法来返回一个 action 对象，这个函数就叫 Action Creator：

``` javascript
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

#### store.dispatch()
dispatch 方法是触发 state 改变的唯一途径，它将 action 发送到 reducer 函数中，进行状态的更新。

``` javascript
import { createStore } from 'redux'

const store = createStore(reducer)

//生成 Action 的函数
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}

//触发更新
store.dispatch(addTodo('Learn Redux'))
```

#### Reducer
Action 只是一个描述事件的简单对象，而 dispatch 方法只是触发了一个动作，通知我们该更新 state 了，但是并没有告诉应用该如何更新 state，而这正是 reducer 函数的工作，它会根据不同的 Action 来决定返回一个新的 state。

``` javascript
var defaultState = 'This language is JavaScript'
function reducer(previousState = defaultState, action) {
  switch (action.type) {
    case 'HTML':
      return 'This language is HTML';
    case 'CSS':
      return 'This language is CSS';
    default:
      return previousState;
  }
}
```

这里需要注意的是 reducer 是一个纯函数：

* 不要修改传入的参数
* 不要执行有副作用的操作，如 API 请求或路由跳转
* 不要调用非纯函数，如 Date.now() 或 Math.random() 等。

``` javascript
const initialState = {
  todos: []
}

function reducer(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      }
    case TOGGLE_TODO:
      return {
        ...state,
        todos: state.todos.map((todo, index) => {
          if (index === action.index) {
            return {
              ...todo,
              completed: !todo.completed
            } // 时刻谨记不要修改 state，保证 reducer 是纯函数
          }
          return todo
        })
      }
    default:
      return state
  }
}
```

所以基本思路是：用户每次 dispatch(action) 后，都会触发 reducer 的执行。Reducer 是一个纯函数，根据 action.type 来更新 state 并返回新的 state。最后会用 reducer 的返回值 nextState 来替换掉原来的 state。

``` javascript
import { createStore } from 'redux'
/** 生成 Action */
function inc() {
  return { type: 'INCREMENT' };
}
function dec() {
  return { type: 'DECREMENT' };
}

// reducer 函数
function reducer(state, action) {
  // 设置初始 state
  state = state || { counter: 0 };

  switch (action.type) {
    case 'INCREMENT':
      return { counter: state.counter + 1 };
    case 'DECREMENT':
      return { counter: state.counter - 1 };
    default:
      return state; // 无论如何都返回一个 state
  }
}

var store = createStore(reducer);

console.log( store.getState() ); // { counter: 0 }

store.dispatch(inc());
console.log( store.getState() ); // { counter: 1 }

store.dispatch(inc());
console.log( store.getState() ); // { counter: 2 }

store.dispatch(dec());
console.log( store.getState() ); // { counter: 1 }
```

规则：Action => dispatch 触发 => Reducer 根据 Action 返回数据 => Store 被更新 => View 被更新。

#### store.subscribe()
Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。

``` javascript
// 注册监听器，在每次 state 更新时，打印日志
const unsubscribe = store.subscribe(() =>
    console.log(store.getState())
    // 或则state更新时，重新渲染视图
)
```

#### 分散Reducer
随着业务量的增加，Reducer必定也会越来越大，就需要按模块的不同来拆分Reducer，通过combineReducers方法将多个Reducer合并。

需要特别注意，使用combineReducers来合并reducer，需要子reducer的名字跟对应要接收的state的key一致。

``` javascript
import { combineReducers, createStore } from 'redux'
const mainReducer = combineReducers({
    homeReducer,
    profileReducer
})
let store = createStore(mainReducer);
```

#### 中间件 applyMiddleware（异步操作）
createStore()方法包含了参数applyMiddleware(),它是Redux的原生方法，作用是 将所有的中间件组成一个数组，依次执行。

操作流程：
* 操作开始时，dispatch action，触发State更新为正在操作状态；
* 操作结束后 再次 dispatch action,获取结果。

解决方案：
写出一个返回函数的 Action Creator，然后使用redux-thunk中间件改造store.dispatch。

``` javascript
import {createStore, applyMiddleware} from 'redux'
import thunk from 'react-thunk'

const store = createStore(
  rootReducer,
  applyMiddleware(thunk)
)

export function fetchPost() {

}
export function requestPost(){}

export function receivePost(){}

export function handleError(err) {

}

export function requstAync() {
  return function(dispatch){
    // 请求发起时 dispatch action
    dispatch(requestPost())
    // 这里的fetchPost 是一个Promise
    return fetchPost()
    .then(response => response.json())
    .then(json =>
     // 请求成功时 dispatch action
      dispatch(receivePost())
    )
    .catch(err =>
     // 请求错误时 dispatch action
      dispatch(handleError(err))
    )
  }
}
```