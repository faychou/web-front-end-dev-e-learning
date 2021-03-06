# redux（状态管理）

## 一、原则
* 单向数据流；
* state 是只读的；
* 使用纯函数进行更改。

## 二、环境安装
``` bash
npm install redux --save
```

## 三、概念
### store
store 是一个对象，用来存放整个应用的 state，相当于一个保存数据的容器，且整个应用只能有一个 store。

``` javascript
import { createStore } from 'redux'
const store = createStore(reducer)
```
这里的 createStore 函数只能被调用一次，createStore 方法还可以接受第二个参数，表示 State 的最初状态。注意，如果提供了这个参数，它会覆盖 Reducer 函数的默认初始值。

### state
当前时刻的 state，可以通过 `store.getState()` 拿到：

``` javascript
import { createStore } from 'redux'
const store = createStore(fn)
const state = store.getState()
```

### action
是一个描述事件的简单对象，告诉我们发生了什么，惟一改变 state 的办法就是去触发 action。其中的 type 属性是必须的，表示将要执行的动作，其他属性可以自由设置。

``` javascript
const action = {
  type: 'ADD_TODO', //发生事件的类型
  payload: 'Learn Redux' // 该类型携带的数据
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

### store.dispatch()
action 描述发生了什么事情，但是这个事情并不会主动发生，而是需要通过 dispatch 方法来触发，它将 action 发送到 reducer 函数中，根据匹配规则进行状态的更新。

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

### reducer
action 只是一个描述事件的简单对象，而 dispatch 方法只是触发了一个动作，通知我们该更新 state 了，但是并没有告诉应用该如何更新 state，而这正是 reducer 函数的工作，它会根据不同的 action 来决定返回一个新的 state。接收两个参数，第一个参数是当前的 state，第二个参数是 action。

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

所以基本思路是：组件每次调用 dispatch(action) 后，都会将一个 action 对象传递给 reducer 函数，然后 reducer 函数根据 action.type 来更新 state 并返回新的 state。最后会用 reducer 的返回值 nextState 来替换掉原来的 state。

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

//监视当前 state 的变化
store.subscribe(function() {
  console.log(store.getState());
});

store.dispatch(inc());
store.dispatch(inc());
store.dispatch(dec());
```

规则：view => 触发 dispatch(action)  => reducer 根据 action.type 返回数据 => store 被更新 => view 被更新。

### subscribe()
store 允许使用 `store.subscribe()` 方法设置监听函数，一旦 state 发生变化，就自动执行这个函数。

``` javascript
// 注册监听器，在每次 state 更新时，打印日志
const unsubscribe = store.subscribe(() =>
    console.log(store.getState())
    // 或则state更新时，重新渲染视图
)
```

### 分散 reducer
随着业务量的增加，reducer 必定也会越来越大，就需要按模块来拆分 reducer，有利于模块化开发，降低耦合度。通过 redux 提供的 combineReducers 方法可以将多个 reducer 进行组合。

``` javascript
import { combineReducers, createStore } from 'redux'
import homeReducer from './homeReducer'
import otherReducer from './otherReducer'

const mainReducer = combineReducers({
    homeReducer,
    otherReducer
})
let store = createStore(mainReducer);
```

> 需要特别注意，使用 combineReducers 来合并 reducer，需要子 reducer 的名字跟对应要接收的 state 的 key 一致。同时注意每发出一个事件，所有 reducer 都会收到。

## 中间件
中间件的位置很明确，就是在 action 到达 reducer 之前做一些操作。

### applyMiddleware（异步操作）
createStore() 方法包含了参数 applyMiddleware() ,它是 Redux 的原生方法，作用是将所有的中间件组成一个数组，依次执行。

操作流程：

* 操作开始时，dispatch(action)，触发 state 更新为正在操作状态；
* 操作结束后 再次 dispatch(action)，获取结果。

解决方案：
写出一个返回函数的 Action Creator，然后使用 redux-thunk 中间件改造 `store.dispatch`。

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
  return function(dispatch) {
    // 请求发起时 dispatch action
    dispatch(requestPost())
    
    // 这里的 fetchPost 是一个 Promise
    return fetchPost()
    .then(response => response.json())
    .then(json => dispatch(receivePost()) // 请求成功时 dispatch action
    )
    .catch(err => dispatch(handleError(err)) // 请求错误时 dispatch action
    )
  }
}
```

### compose


### redux-thunk
redux 的一个中间件。可以使 action creator 返回一个 function（而不仅仅是 object），并且使得 dispatch 方法可以接收一个 function 作为参数，通过这种改造使得 action 支持异步（或延迟）操作。

``` js
import thunkMiddleware from 'redux-thunk'
import { createLogger } from 'redux-logger'
import { createStore, applyMiddleware } from 'redux'
import { selectSubreddit, fetchPosts } from './actions'
import rootReducer from './reducers'

const loggerMiddleware = createLogger()

const store = createStore(
  rootReducer,
  applyMiddleware(
    thunkMiddleware, // 允许我们 dispatch() 函数
    loggerMiddleware // 一个很便捷的 middleware，用来打印 action 日志
  )
)

store.dispatch(selectSubreddit('reactjs'))
store
  .dispatch(fetchPosts('reactjs'))
  .then(() => console.log(store.getState())
)
```



#### redux-persist
要实现 redux 的持久化，包括 redux store 的本地持久化存储及恢复启动两个过程，如果完全自己编写实现，代码量比较复杂，可以使用开源库 redux-persist，它提供 persistStore 和 autoRehydrate 方法分别持久化本地存储 store 及恢复启动 store，另外还支持自定义传入持久化及恢复 store 时对 store state 的转换拓展。

``` bash
yarn add redux-persist
```

#### redux-saga
redux-saga 是一个致力于使应用中如数据获取，本地缓存访问等异步任务易于管理，高效运行，便于测试，能更好的处理异常的三方库。只负责管理异步任务，它可以接受应用主进程的 redux action 以决定启动，暂停或者是取消进程任务，它也可以访问 redux 应用 store state，然后分发 action。

``` js
import { createStore, applyMiddleware, compose } from 'redux';
import createSagaMiddleware from 'redux-saga';
import rootSaga from '../sagas/';

// 调用 createSagaMiddleware 方法创建中间件
const sagaMiddleware = createSagaMiddleware({ sagaMonitor });
middleware.push(sagaMiddleware);

// 启用中间件
enhancers.push(applyMiddleware(...middleware));

const store = createStore(rootReducer, initialState, compose(...enhancers));

// 调用 run 方法启动根 saga 
sagasagaMiddleware.run(rootSaga);
```

