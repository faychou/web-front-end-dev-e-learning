# react-redux
redux 和 react 之间没有关系，redux 本身是可以结合其他视图框架使用，但是又独立于其他框架而存在，而我们可以通过 react-redux 这个库将他们绑定起来。

react-redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。也就是说 UI 组件负责 UI 的呈现，不处理逻辑，容器组件负责管理数据和逻辑。

### UI组件特点
* 只负责 UI，不带任何逻辑
* 无状态组件
* 所有数据都是通过 props 提供
* 不使用任何 Redux API

### 容器组件特点
* 负责管理数据和业务逻辑，不负责 UI
* 带有内部状态
* 使用 Redux API

但是很多时候我们发现有的组件又负责 UI 又负责处理逻辑，所以一般做法是在外面封装一层，将逻辑和 UI 分离，外层是容器组件负责写逻辑，内层是 UI 组件负责渲染出视图。

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

#### mapStateToProps
该函数是属于输入逻辑，接收一个从 reducer 传递过来的 state 作为参数，定义从 state 转换成 UI 组件 props 的规则，并返回 props 对象。

此函数还可以接收第二个 ownProps 参数，代表直接在 UI 组件上声明的 props。

#### mapDispatchToProps
该函数是属于输出逻辑，接收 Store 中的  dispatch 方法作为参数，用户发出的动作如何变为 Action 对象，从 UI 组件传出去，并返回 props 对象。

### 案例
``` javascript
import React, { Component } from 'react'
import PropTypes from 'prop-types'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider, connect } from 'react-redux'

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

Counter.propTypes = {
  value: PropTypes.number.isRequired,
  onIncrease: PropTypes.func.isRequired
}

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
在只有一个 reducer 的情况下，容器组件的 mapStateToProps 函数接收到的 state 是唯一 reducer 返回的对象。而如果有多个 reducer ，就会有多个返回值。这时候容器组件的 mapStateToProps 函数接收到的 state 是包含所有 reducer 返回值的对象。可以用 key 值来区，这个 key 值就是在 combineReducers 时传入的。

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
