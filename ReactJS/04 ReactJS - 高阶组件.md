# 高阶组件(HOC)
Higher-Order Function（高阶函数），是函数式编程中的一个基本概念，它描述了一种函数接受函数作为参数，然后输出一个函数。比如常用的工具方法 reduce，map 等都是高阶函数。

而 Higher-Order Components（高阶组件）其实也是类似于高阶函数，它接受一个或多个组件作为参数，返回一个全新的组件。

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

从以上代码可以看出这个新的组件会使用传给它的组件作为子组件。一般在开发过程中，当需要在多个组件类复用时，就可以创建一个高阶组件。如下，一个常见的实例：

``` js
const List = ({ data }) => (
  <ul>
    {data.map(item => <li key={item.name}>{item.name}</li>)}
  </ul>
)
```

现在需要给这个 List 组件加一个 loading 功能，我们可以这么做：

``` js
const List = ({ data, isLoading }) => (
  isLoading ?
    <div>我正在加载...</div> :
    <ul>
      {data.map(item => <li key={item.name}>{item.name}</li>)}
    </ul>
)
```

通过 isLoading 的状态来判断是否出现加载动画。这样做也能解决问题，但是不够优雅。第一，需要修改原来 List 组件的代码；第二，如果有其它组件也需要 loading 功能，又需要重复写相同的判断逻辑。而如果使用高阶组件就可以完美的解决这个问题。

``` js
const withLoading = BaseComponent => ({ isLoading, ...otherProps }) => (
  isLoading ?
    <div>我正在加载...</div> :
    <BaseComponent {...otherProps} />
)

const LoadingList = withLoading(List)
```

通过以上代码我们可以总结出，编写的组件尽量纯净或者说其中的业务逻辑尽量单一。但是如果各种组件间又需要增加新功能，如打印日志，获取数据和校验数据等和展示无关的逻辑的时候，这些公共的代码就会被重复写很多遍。因此，我们可以抽象出一个高阶组件，可以在原有组件的基础上，对其增加新的功能和行为，如读取、添加、编辑、删除等，也可以用其它元素包裹组件，用来实现封装样式、添加布局或其它操作。

来看另一个案例，假设我有一个组件，需要从 LocalStorage 中获取数据，然后渲染出来。于是我们可以这样写组件代码：

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

代码很简单，但当我有其他组件也需要从 LocalStorage 中获取同样的数据展示出来时，我需要在每个组件都重复 componentWillMount 中的代码，这显然是很冗余的。下面让我们来看看使用高阶组件可以怎么改写这部分代码：

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

高阶组件也有缺点，首先是被包裹组件的静态方法会消失，因为我们将组件当做参数传入函数中，返回的已经不是原来的组件，而是一个新的组件。

## 案例
### 组合方式
如果用户已经登录则显示主页面，否则显示登录界面；又或者在渲染列表时，传入 List 和 Loading 组件，为新组件添加加载中的行为。

``` js
const HoC = (HomeComponent, LoginView) => {
  const NewComponent = () => {
    const {user} = this.props
    if (user) {
      return <HomeComponent {...this.props} />
    } else {
      return <LoginView {...this.props} />
    }
  }
  return NewComponent
}
```

### 组件继承
``` js
const HoC = (WrappendComponent) => {
  class NewComponent extends WrappendComponent {
    render() (
      const {user, ...otherProps} = this.props
      this.props = otherProps
      return super.render()
    }
  }
  return NewComponent
}
```

NewComponent 是一个新组件，它继承自 WrappendComponent，共享父级的函数和属性。可以使用 super.render() 或者 super.componentWillUpdate() 调用父级的生命周期函数，但是这样会让两个组件耦合在一起，降低组件的复用性。

## 应用场景
### 属性代理
高阶组件通过被包裹的 React 组件来操作 props 。

``` js
import React, { Component } from 'react'
import ExampleHoc from './example-hoc'

@ExampleHoc
export default class UseContent extends Component {
  render() {
    console.log('props:',this.props);
    return (
        <div>
           {...this.props}
        </div>
    )
  }
}
```

``` js
import React, { Component } from 'react'

const ExampleHoc = WrappedComponent => {
  return class extends Component {
    render() {
       return <WrappedComponent {...this.props} />
    }
  }
}
export default ExampleHoc
```

这样的组件就可以作为参数被调用，原始组件就具备了高阶组件对它的修饰。保持单个组件封装性的同时还保留了易用性。

#### 控制 props
可以读取，编辑，增加，移除从 WrappedComponent 传来的 props，同时对高阶组件的 props 作新的命名防止混淆。

``` js
import React, { Component } from 'react'

const ExampleHoc = WrappedComponent => {
  return class extends Component {
    render() {
      const newProps = {
        name: newText,
      }
      return <WrappedComponent {...this.props}  {...newProps}/>
    }
  }
}
export default ExampleHoc。
```

#### 通过 refs 使用引用
在高阶组件中，可以接受 refs 使用 WrappedComponent 的引用。 

``` js
import React, { Component } from 'react'

const ExampleHoc = WrappedComponent => {
  return class extends Component {
    proc = wrappedComponentInstance => {
        wrappedComponentInstance.method()
    }
    render() {
       const newProps = Object.assign({}, this.props,{
           ref: this.proc,
       })
       return <WrappedComponent {...this.newProps} />
    }
  }
}
export default ExampleHoc
```

当 WrappedComponent 被渲染的时候，refs 回调函数就会被执行，这样就会拿到一份 WrappedComponent 的实例的引用。这样就可以方便地用于读取和增加实例 props，并调用实例。

#### 抽象state
通过 WrappedComponent 提供 props 和回调函数抽象 state。可以把原组件抽象为展示型组件，分离内部状态，搞成无状态组件。

``` js
import React, { Component } from 'react';

const ExampleHoc = WrappedComponent => {
  return class extends Component {
    constructor(props) {
        super(props)
        this.state = {
         name: '',
        }
    }
    onNameChange = e => {
        this.setState({
            name: e.target.value,
        })
    }
    render() {
       const newProps = {
         name: {
            value: this.state.name,
            onChange: this.onNameChange,
         }
       }
       return <WrappedComponent {...this.props} {...newProps} />
    }
  }
}
export default ExampleHoc
```

通过把 input 的 name prop 和 onchange 方法提到了高阶组件中，这样有效的抽象了同样的 state 操作。

``` js
//用法：
import React, { Component } from 'react'
import ExampleHoc from './example-hoc'

//@ExampleHoc
export default class UseContent extends Component {
  render() {
    console.log('props:',this.props);
    return (
        <input name="name" {this.props.name} />
    )
  }
}
//这样就是一个受控组件
```

#### 其它元素包裹 WrappedComponent
可以使用其它元素包裹 WrappedComponent ，这样既可以增加样式，也可以方便布局。

``` js
import React, { Component } from 'react'

const ExampleHoc = WrappedComponent => {
  return class extends Component {
    render() {
       return (
            <div style={{display: 'flex'}}>
                <WrappedComponent {...this.props} />
            </div>
       )
    }
  }
}
export default ExampleHoc
```

### 反向继承
高阶组件继承于被包裹的 React 组件。 

``` js
const ExampleHoc = WrappedComponent => {
  return class extends WrappedComponent {
    render() {
       return super.render()
    }
  }
}
```

高阶组件返回的组件继承与 WrappedComponent，因为被动继承了 WrappedComponent ，所有的调用都是反向。它通过继承 WrappedComponent 来实现，方法可以通过 super 来顺序调用。在反向继承中，高阶函数可以使用 WrappedComponent 的引用，这意味着可以使用 WrappedComponent 的 state，props,生命周期和 render 方法。但它并不能保证完整的子组件树被解析。

#### 渲染劫持
渲染劫持就是高阶组件可以控制 WrappedComponent 的渲染过程，并渲染各种各样的结果。我们可以在这个过程中任何 React 元素的结果中读取，增加，修改，删除 props,或者修改 React 的元素树，又或者用样式控制包裹这个 React 元素树。

``` js
const ExampleHoc = WrappedComponent => {
  return class extends WrappedComponent {
    render() {
      if(this.props.loggedIn) { //当登录了就渲染
           return super.render()
      } else {
          return null
      }
      
    }
  }
}
```

#### 控制 state
高阶组件是可以读取，修改，删除 WrappedComponent 实例的 state，如果需要的话，也可以增加 state，但这样你的 WrappedComponent 会变得一团糟。因此大部分的高阶组件多都应该限制读取或者增加 state，尤其是增加 state，可以通过重新命名 state，以防止混淆。

``` js
const ExampleHoc = WrappedComponent => {
  return class extends WrappedComponent {
    render() {
      <div>
       <h3>HOC debugger</h3>
       <p>Props <pre>{JSON.stringfy(this.props,null,1)}</pre></p>
       <p>State <pre>{JSON.stringfy(this.state,null,2)}</pre></p>
       {super.render()}
      </div>
    }
  }
}
```

### 高阶组件接受其它参数
举个列子，我把用户信息存在本地 LocalStorage 中，当然里面有很多 key，但是我不需要用到所有，我希望按照我的喜好得到我自己想要的。

``` js
import React, { Component } from 'react'

const ExampleHoc = (key) => (WrappedComponent) => {

  return class extends Component {
    componentWillMount() {
      let data = localStorage.getItem(key);
        this.setState({data});
    }

    render() {
      return <WrappedComponent data={this.state.data} {...this.props} />
    }
  }
}
```

``` js
import React, { Component } from 'react'

class MyComponent2 extends Component {  
  render() {
    return <div>{this.props.data}</div>
  }

}

const MyComponent2WithHOC = ExampleHoc(MyComponent2, 'data')

export default MyComponent2WithHOC
```

``` js
import React, { Component } from 'react'

class MyComponent3 extends Component {  
  render() {
    return <div>{this.props.data}</div>
  }
}
const MyComponent3WithHOC = ExampleHoc(MyComponent3, 'name')

export default MyComponent3WithHOC
```

实际上，此时的 ExampleHoc 和我们最初对高阶组件的定义已经不同。它已经变成了一个高阶函数，但这个高阶函数的返回值是一个高阶组件。我们可以把它看成高阶组件的变种形式。这种形式的高阶组件大量出现在第三方库中。


