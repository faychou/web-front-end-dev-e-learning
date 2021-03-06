# react in ES6、ES7
之前学习的 react 写法是基于 ES5 的，如果使用最新的 ES6 的语法，则有些地方需要做细微的调整：

``` js
import React from 'react'

// 注意加上 export default 是指该组件能够被引用
export default class MyComponent extends React.Component {

  // 构造函数
  constructor (props) {
    super(props)
    
    // 定义 initialState
    this.state = { smiling: false }
    
    // 定义 eventHandler
    this.handleClick = this.handleClick.bind(this)
  }

  // 生命周期方法
  componentWillMount () {}
  componentDidMount () {}
  componentWillUnmount () {}
  

  // getters and setters
  get attr() {}

  // handlers
  handleClick() {}

  // render
  render () {}

}

MyComponent.defaultProps = {
  name: 'Guest'
}

/**
 * 统一都要定义 propTypes
 */
MyComponent.propTypes = {
  name: React.PropTypes.string
}
```

注意以下两种组件写法的区别：

``` js
// 方式一：
import React from 'react'
class MyComponent extends React.Component {
  // ...
}

// 方式二：
import React, { Component } from 'react'
class MyComponent extends Component {
  // ...
}
```

最新 state 、 propTypes 和 defaultProps 的 ES7 写法：

``` js
export default class MyComponent extends Component {
  state = { expanded: false }
    
  static propTypes = {
    model: React.PropTypes.object.isRequired,
    title: React.PropTypes.string
  }

  static defaultProps = {
    model: {
      id: 0
    },
    title: 'Your Name'
  }
  
  constructor(props) {
    super(props);
  }
  
  // 事件
  handleSubmit = (e) => {
    e.preventDefault()
    //
  }
  
  // ……
}
```

> 注意：propTypes 和 defaultProps 都是组件的静态属性。在组件的代码中，这两个属性的设定位置越高越好。因为这样方便其他阅读代码者或者开发者自己 review，一眼就能看到这些信息。这些信息就如同组件文档一样，对于理解或熟悉当前组件非常重要。

### super 中传递的 props
``` js
class Component {
  constructor(props) {
    super(props)
    //...
  }
}
```

这里把 props 传进 super 是必要的，这使得基类 React.Component 可以初始化 this.props。然而，即便在调用 super() 时没有传入 props 参数，你依然能够在 render 和其它方法中访问 this.props。

### prop-types
可能大家在使用 propTypes 设置属性类型时会发现控制台有以下警告信息：

``` bash
Accessing PropTypes via the main React package is deprecated, and will be removed in  React v16.0. Use the latest available v15.* prop-types package from npm instead.
```

大致意思是不要在 react 里直接使用 propTypes，而是安装一个 prop-types 包。所以对之前代码稍微修改下：

``` js
//第一步、终端安装：
npm install --save prop-types

//第二步、在需要设置 propTypes 的 js 文件中添加以下代码：
import React,{Component} from 'react'
import PropTypes from 'prop-types'  //引入 prop-types

export default class MyComponent extends Component {
  static propTypes = {
    title:PropTypes.string  //设置类型
  }
  static defaultProps = {
    title:'默认 props'
  }
  constructor() {
    super()
  }
  render() {
    // ... do things with the props
  }
}
```

### 解构 Props
``` js
export default class MyComponent extends Component {
  constructor() {
    super()
  }
  render() {
    const {
      model,
      title
    } = this.props
    
    <div>
      <h1>{title}</h1>
      <input
        type="text"
        value={model.name} />
    </div>
  }
}
```