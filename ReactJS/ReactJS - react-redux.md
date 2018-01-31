# react-redux
React-Redux 是 Redux 的 React 版，Redux 本身独立于其他框架而存在，又可以结合其他视图框架使用。

React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。也就是说 UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

#### UI组件特点
* 只负责UI显示，不带任何逻辑
* 无状态组件
* 所有数据都是通过 props 提供
* 不使用任何 Redux API

#### 容器组件特点
* 负责管理数据和业务逻辑，不负责UI显示
* 带有内部状态
* 使用 Redux API

如果遇到一个组件既有UI和业务逻辑时，需要拆分成下面的结构：
外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

#### 安装
``` bash
npm install --save react-redux
```

#### 引入
``` javascript
  import { Provider, connect } from 'react-redux'
```

#### Provider
这个 Provider 其实就是一个中间件，他是在原有 App Container 上面再包一层，他的作用就是接收 store 里面的 store 作为 props，这样一来，App的所有子组件就默认都可以拿到state了。

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

#### connect()
React-Redux 提供connect方法，用于从 UI 组件生成容器组件。通俗的讲就是 connect 将 state 上的 props（属性）和 方法（dispatch）添加到对应的 UI组件上。

``` javascript
import {connect} from 'react-redux'
import {TodoList} from './TodoList'

function mapStateToProps(state){
  return{
    // do something
  }
}

function mapDispatchToProps(dispatch){
  return{
    // do something
  }
}
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```
说明：

TodoList是 UI 组件，通过 React-Redux 的 connect 方法自动生成为容器组件。

##### connect方法接受两个参数，定义了 UI 组件的业务逻辑：

1、 mapStateToProps（输入逻辑）：外部的数据（即state对象）如何转换为 UI 组件的 props；

2、 mapDispatchToProps（输出逻辑）：用户发出的动作如何变为 Action 对象，从 UI 组件传出去。

#### 案例
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
    const { value, onIncreaseClick } = this.props
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncreaseClick}>Increase</button>
      </div>
    )
  }
}

Counter.propTypes = {
  value: PropTypes.number.isRequired,
  onIncreaseClick: PropTypes.func.isRequired
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

// onIncreaseClick 到 dispatch 的映射
function mapDispatchToProps(dispatch) {
  return {
    onIncreaseClick: () => dispatch(increaseAction)
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