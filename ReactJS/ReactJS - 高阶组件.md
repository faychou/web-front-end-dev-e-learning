# 高阶组件
高阶组件就是一个函数，它接受一个组件作为参数，返回一个新的组件。这个新的组件会使用传给它的组件作为子组件。

``` javascript
import React, { Component } from 'react'

export default (WrappedComponent) => {
  class NewComponent extends Component {
    // 可以做很多自定义逻辑
    render () {
      return <WrappedComponent />
    }
  }
  return NewComponent
}
```

做为一个高阶组件，可以在原有组件的基础上，对其增加新的功能和行为。我们一般希望编写的组件尽量纯净或者说其中的业务逻辑尽量单一。但是如果各种组件间又需要增加新功能，如打印日志，获取数据和校验数据等和展示无关的逻辑的时候，这些公共的代码就会被重复写很多遍。因此，我们可以抽象出一个高阶组件，用以给基础的组件增加这些功能，类似于插件的效果。

假设我有一个组件，需要从LocalStorage中获取数据，然后渲染出来。于是我们可以这样写组件代码：

``` js
import React, { Component } from 'react'

class MyComponent extends Component {

  componentWillMount() {
    let data = localStorage.getItem('data');
    this.setState({data});
  }

  render() {
    return <div>{this.state.data}</div>
  }
}
```

代码很简单，但当我有其他组件也需要从LocalStorage中获取同样的数据展示出来时，我需要在每个组件都重复componentWillMount中的代码，这显然是很冗余的。下面让我们来看看使用高阶组件可以怎么改写这部分代码：

``` js
import React, { Component } from 'react'

function withPersistentData(WrappedComponent) {
  return class extends Component {
    componentWillMount() {
      let data = localStorage.getItem('data');
        this.setState({data});
    }

    render() {
      // 通过{...this.props} 把传递给当前组件的属性继续传递给被包装的组件WrappedComponent
      return <WrappedComponent data={this.state.data} {...this.props} />
    }
  }
}

class MyComponent2 extends Component {  
  render() {
    return <div>{this.props.data}</div>
  }
}

const MyComponentWithPersistentData = withPersistentData(MyComponent2)
```