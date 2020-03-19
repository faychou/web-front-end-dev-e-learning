# react优化
### this 绑定
传统的 `React.createClass` 创建的组件，可以自动绑定 this，但是最新的 class 情况下，this 无法自动绑定。一个常见的解决方案便是：

``` js
onChange = {this.handleChange.bind(this)}
```

这种方法简明扼要，但是有一个潜在的性能问题：当组件每次重新渲染时，都会有一个新的函数创建。

#### 箭头函数绑定
通过箭头函数可以隐式绑定 this ，但是它同样存在潜在的性能问题：

``` js
onChange = {e => this.handleChange(e)}
```

#### Constructor 内绑定
这是一种最佳实践方案，但是 constructor 内绑定在可读性和可维护性上也许有些欠缺。

``` js
constructor(props) {
  super(props);
  this.handleChange = this.handleChange.bind(this);
}
```

#### Class 属性中使用 = 和箭头函数
这个方法依赖于 ES next 的新特性，要求能够使用 stage-2 的特性：

``` js
handleChange = () => {
  // call this function from render 
  // and this.whatever in here works fine.
}
```

### 合理利用解构
``` js
export default class ProfileContainer extends Component {
  // ……
  render() {
    const {model, title} = this.props

    return ( 
      // ……
    )
  }
}
```

如果使用的是函数组件，正常 props 应该是作为函数的参数：

``` js
function MyComponent(props) {
  return (
    <div style={props.style}>{props.children}</div>
  )
}
```

如果使用解构可以这样修改：

``` js
function MyComponent({children, style}) {
  return (
    <div style={style}>{children}</div>
  )
}
```

### 使用展开符
一般写法：

``` js
handleCommentSubmit(comment) {
  let {comments} = this.state
  let newComment = comment

  newComment.id = Date.now()

  let newComments = comments.concat([newComment])

  // ...
}
```

使用展开符后，可以重构为：

``` js
handleCommentSubmit(comment) {
  let {comments} = this.state
  let newComment = {...comment, id: Date.now()}
  let newComments = [...comments, newComment]
  // ...
}
```

### 切割组件
当一个 React 组件功能复杂且臃肿的时候，就需要考虑对该组件进行优化了，因为组件的原则是保持单一性，其中一个优化方法就是对组件的 render 方法进行切割：

``` js
class Panel extends React.Component {
  renderHeading() {
    // ...
  }

  renderBody() {
    // ...
  }

  render() {
    return (
      <div>
        {this.renderHeading()}
        {this.renderBody()}
      </div>
    )
  }
}
```

这种方法虽然对组件进行了 sub-render ，但是该组件仍然保持有原先的 state, props, 以及 methods。而要真正做到减少复杂度，我们可以通过创建函数式子组件的方法直接分离组件：

``` js
const PanelHeader = (props) => (
  // ...
)

const PanelBody = (props) => (
  // ...
)

class Panel extends React.Component {
  render() {
    return (
      <div>
        <PanelHeader title={this.props.title}/>
        <PanelBody content={this.props.content}/>
      </div>
    )
  }
}
```