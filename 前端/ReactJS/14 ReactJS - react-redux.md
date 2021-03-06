# react-redux
redux 可以直接在 react 中使用，因为 redux 本身是独立于其他框架而存在，但是又可以结合任意框架使用。而直接在 react 中使用，不是很方便，所以就可以通过 react-redux 这个库将他们绑定起来，提高效率和灵活性。

react-redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。也就是说 UI 组件负责 UI 的呈现，不处理逻辑，容器组件负责管理数据和逻辑。

### 展示 UI 组件特点
* 只负责 UI，不带任何逻辑；
* 无状态组件；
* 所有数据都是通过 props 提供；
* 不使用任何 Redux API；

### 容器组件特点
* 负责获取数据，处理业务逻辑，通常在render()函数内返回展示型组件；
* 带有内部状态；
* 使用 Redux API；

但是很多时候我们发现有的组件又负责 UI 又负责处理逻辑，所以一般做法是在外面封装一层，将逻辑和 UI 分离，外层是容器组件负责写逻辑，内层是 UI 组件负责渲染视图。

## 安装
``` bash
npm install --save react-redux
```

### 引入
``` javascript
import { Provider, connect } from 'react-redux'
```

## 使用方法
react-redux 提供一个组件 Provider 和一个方法 connect。

### Provider
Provider 组件一般作为 React Redux 应用最顶层的组件，它的作用就是接收全局 store 作为 props，将 store 放在 context 里，这样所有子组件就都可以拿到 state 了。

``` javascript
import { createStore } from 'redux'
import { Provider } from 'react-redux'
const store = createStore(reducer)

<Provider store={store}>
  <App />
</Provider>
```

Provider 是一个 react 组件，提供了一个参数 store，然后渲染了一个子组件，我们通常把路由渲染成子组件：

``` html
<!-- react-router4 -->
<Provider store={store}>
  <Router history={hashHistory}>
    {routes}
  </Router>
</Provider>
```

### connect()
React-Redux 提供 connect 方法，用于从 UI 组件生成容器组件。这样 UI 组件就可以直接通过 `this.props` 拿到容器组件上所有的属性和方法了。

``` javascript
import {connect} from 'react-redux'
import {TodoList} from './TodoList'

function mapStateToProps(state) {
  return {
    todo:state.todo
  }
}

function mapDispatchToProps(dispatch) {
  return {
    add:() => dispatch({type: 'ADD'})
  }
}

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```
说明：

TodoList 是 UI 组件，通过 React-Redux 的 connect 方法自动生成为容器组件，在 UI 组件中通过调用 `this.props.add` 就会触发对应的 dispatch，从而更新状态。

#### connect 参数
connect 是一个高阶函数，接受两个参数，定义了 UI 组件的业务逻辑。

如果你不为 connect() 指明第二个参数，你的组件会默认接收 dispatch。

#### mapStateToProps
用于建立 组件 跟 store.state 的映射关系，接收一个从 reducer 传递过来的 state 作为参数，定义从 state 转换成 UI 组件 props 的规则，并返回 props 对象。也就是说该方法从 Redux 状态树中提取需要的部分作为 props 传递给当前的组件。

``` js
function mapStateToProps(state) {
  return {
    todoList: state.todoList
  }
}
```

此函数还可以接收第二个 ownProps 参数，代表直接在 UI 组件上声明的 props。

``` js
function mapStateToProps(state, ownProps) {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}
```

mapStateToProps 可以不传，如果不传，组件不会监听 store 的变化，也就是说 Store 的更新不会引起 UI 的更新。

``` js
export default connect(null, mapDispatchToProps)(Counter)
```

#### mapDispatchToProps

用于建立组件跟 store.dispatch 的映射关系，接收 Store 中的  dispatch 方法作为参数，用户发出的动作如何变为 Action 对象，从 UI 组件传出去，并返回 props 对象。也就是说将需要绑定的响应事件（action）作为 props 传递到组件上。

``` js
function mapDispatchToProps(dispatch) {
  return {
    add:() => dispatch({type: 'ADD'})
  }
}
```

如果你不为 connect() 指明第二个参数，你的组件会默认接收 dispatch。

``` js
export default connect(mapStateToProps)(App)
```

### 案例
``` javascript
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider, connect } from 'react-redux'

// Store
const store = createStore(counter)

// Action
const increaseAction = { type: 'increase' }

// Reducer
function counter(state = { count: 0 }, action) {
  const count = state.count
  switch (action.type) {
    case 'increase':
      return { count: count + 1 }
    default:
      return state
  }
}

// 定义 value 到 state 的映射
function mapStateToProps(state) {
  return {
    value: state.count
  }
}

// onIncrease 到 dispatch 的映射
function mapDispatchToProps(dispatch) {
  return {
    onIncrease: () => dispatch(increaseAction)
  }
}

// 计数器组件，它是一个纯的 UI 组件
// value 需要从 state 计算得到
// onIncreaseClick 向外发出的 Action
class Counter extends Component {
  render() {
    const { value, onIncrease } = this.props
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncrease}>Increase</button>
      </div>
    )
  }
}

// 使用 connect 方法生成容器组件
const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('app')
)
```

### 多 reducer 返回的 state
在只有一个 reducer 的情况下，容器组件的 mapStateToProps 函数接收到的 state 是唯一 reducer 返回的对象。而如果有多个 reducer ，就会有多个返回值。这时候容器组件的 mapStateToProps 函数接收到的 state 是包含所有 reducer 返回值的对象。可以用 key 值来区分，这个 key 值就是在 combineReducers 时传入的。

``` js
const mapStateToProps = (state) => {
  const homeReducer = state.homeReducer;
  const otherReducer = state.otherReducer;
  return {
    value1: homeReducer.value,
    value2: otherReducer.value
  };
}

export default connect(mapStateToProps)(Counter);
```
## 路由与 Redux
同时使用 React-Router 和 Redux 时，大多数情况是正常的，但是也可能出现路由变更组件未更新的情况，因为 Redux 会实现组件的 shouldComponentUpdate 方法，当路由变化时，该组件并没有接收到 props 表明发生了变更，需要更新组件。要解决这个问题只需要简单的使用 react-router-dom 提供的 withRouter 方法包裹组件：

``` js
import { withRouter } from 'react-router-dom'
export default withRouter(connect(mapStateToProps)(Home))
```

## react-router-redux
在使用 Redux 以后，需要遵循 redux 的原则：单一可信数据来源，即所有数据来源都只能是 reudx store，react 路由状态也不应例外，所以需要将路由 state 与 store state 连接。

``` bash
yarn add react-router-redux@next
yarn add history
```

然后，在创建store时，需要实现如下配置：

1、创建一个 history 对象，对于 web 应用，我们选择 browserHisotry，对应需要从 `history/createBrowserHistory` 模块引入 createHistory 方法以创建 history 对象；

2、添加 routerReducer 和 routerMiddleware 中间件“，其中 routerMiddleware 中间件接收 history 对象参数，连接 store 和 history，等同于旧版本的 syncHistoryWithStore；

``` js
import createHistory from 'history/createBrowserHistory'
import { ConnectedRouter, routerReducer, routerMiddleware, push } from 'react-router-redux'
// Create a history of your choosing (we're using a browser history in this case)
export const history = createHistory()

// Build the middleware for intercepting and dispatching navigation actions
const middleware = routerMiddleware(history)

// Add the reducer to your store on the `router` key
// Also apply our middleware for navigating
const store = createStore(
  combineReducers({
    ...reducers,
    router: routerReducer
  }),
  applyMiddleware(middleware)
)

return store
```

## redux hook

**useDispatch**

可以直接在组件中直接使用`dispatch`派发`action`。

``` js
import React, { memo, useEffect } from 'react'
import { useDispatch } from 'react-redux'
import { getTopBannersAction } from './store/actionCreator'

function JMRecommend(props) {
  const dispatch = useDispatch()
  useEffect(() => {
    dispatch(getTopBannersAction())
  }, [dispatch])

  return (
    <div>
      <h2>JMRecommend</h2>
    </div>
  )
}
export default memo(JMRecommend)
```

**useSelector**

- 不用在组件中定义依赖的`state`, 直接在组件中使用`useSelector`传递函数的参数是`state`
- 函数返回一个对象, 在对象中定义需要依赖的`state`

``` js
function JMRecommend(props) {
  const { topBanners } = useSelector(state => ({
    topBanners: state.recommend.topBanners,
  }))

  return (
    <div>
      <h2>JMRecommend</h2>
      <h3>{topBanners.length}</h3>
    </div>
  )
}
export default memo(JMRecommend)

```

`useSelector`优化: 

* `useSelector`的第二个参数传递一个`ShallowEqual`

- `ShallowEqual`作用: 对一次浅层比较, 和前一次`useSelector`返回的对象及进行比较

``` js
import React, { memo, useEffect } from 'react'
import { shallowEqual, useDispatch, useSelector } from 'react-redux'
import { getTopBannersAction } from './store/actionCreator'

function JMRecommend(props) {
  // redux Hook 组件和redux关联: 获取数据和进行操作
  const { topBanners } = useSelector(state => ({
    topBanners: state.recommend.topBanners,
  }), shallowEqual)
  const dispatch = useDispatch()
  
  useEffect(() => {
    dispatch(getTopBannersAction())
  }, [dispatch])

  return (
    <div>
      <h2>JMRecommend</h2>
      <h3>{topBanners.length}</h3>
    </div>
  )
}
export default memo(JMRecommend)
```



## immutableJS

使用 `immutable` 可以让 `redux` 中的维护的 `state` 不在是浅层拷贝再赋值，而是使用 `immutable`数据结构保存数据，这样修改数据后，会返回一个新的对象，原来的对象不会发生改变。

使用 immutableJS 好处: 修改的 `state` 不会修改原有数据结构, 而是返回修改后新的数据结构, 可以利用之前的数据结构而不会造成内存的浪费。

**安装**

``` bash
yarn add immutable
```

**使用**

``` js
// 1.在 reducer.js 文件使用 immutable设置: discover->child-cpn->recommend->store->reducer.js
import { Map } from "immutable"
import * as actionTypes from './actionTypes'

// 使用 immutable 管理 redux 中的 state (修改的 state 不会修改原有数据结构, 而是返回修改后新的数据结构)
// 对象转换成 immutable 对象：Map
// 数组转换成 immtable 数组：List
const defaultState = Map({
  topBanners: [],
})

export default function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.CHANGE_TOP_BANNER:
       return state.set('topBanners', action.topBanners) 
    default:
      return state
  }
}


// 2.在 recommend 的 index.js 文件获取的是 immutable 对象, 需要进行设置
const { topBanners } = useSelector(state => ({
     topBanners: state.recommend.get('topBanners') 
}))
```

### redux-Immutable

使用 `redux-immutable` 中的 `combineReducers` 管理数据

**安装**

``` bash
yarn add redux-immutable
```

**使用**

``` js
// src->store->reducer
import { combineReducers } from 'redux-immutable'

import { reducer as recommendReducer } from '../pages/discover/child-pages/recommend/store'

// 多个 reducer 合并
const cRducer = combineReducers({
  recommend: recommendReducer
})

export default cRducer

```

