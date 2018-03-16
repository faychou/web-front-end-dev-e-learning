# 组件

### 组件声明
``` js
//创建一个组件
var MyComponent = React.createClass({
  render: function() {
    return <h1>hello world!</h1>;
  }
});

//把组件渲染到页面body中
React.render(
  <MyComponent />,
  document.body
);
```

> 注意：

* 每个组件都必须有 render 方法，定义输出的样式；

* React 规定，自定义组件的第一个字母必须大写，比如 MyComponent 不能写成 myComponent ，以便与内置的原生类相区分；

* `<MyComponent/>` 表示生成一个组件类的实例，每个实例一定要有闭合标签，写成 `<MyComponent></MyComponent>` 也可。


### 属性
`getDefaultProps` 初始化属性，只调用一次，实例之间共享引用。



### 状态
`getInitialState` 初始化每个实例特有的状态。

``` js
var MyComponent = React.createClass({
  getInitialState: function() {
    return {
      msg:"hello world!"
    }
  },
  render: function() {
    return <h1>{this.state.msg}</h1>;
  }
});
```

#### setState 接受一个函数作为参数
从性能的角度考虑，React 可能会在某次单独的更新中批量执行多个`setState()`。
由于`this.props`和`this.state`可能会异步更新，我们不应该依靠他们的值来计算下一步的状态。

``` js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为避免这个问题，可以使用setState()的另一种形式-接受函数而不是对象。传递给setState的函数将之前的状态作为第一个参数，需增加的属性作为第二个参数：

``` js
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

``` js
// ES5
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```

### 函数组件
函数组件是指没有状态、没有方法的纯组件，也称为无状态组件，是最基础的组件形式，由于没有状态的影响所以就是纯静态展示的作用。如按钮、标签、输入框等。我们应该最大限度地编写和使用这一类组件。编写这类组件的方法通常是使用 ES6 的箭头函数，将 props 作为函数的参数。

``` js
const PureComponent = (props) => (
  <div>
    //use props
  </div>
)

//当然也可以使用 es5 的函数，只是没有 es6 更加直观
function ExpandableForm({ onExpand, expanded = false, children, onSubmit }) {
    const formStyle = expanded ? {height: 'auto'} : {height: 0}
    return (
        <form style={formStyle} onSubmit={onSubmit}>
            {children}
        <button onClick={onExpand}>Expand</button>
        </form>
    )
}
```

### 组件生命周期
#### componentWillMount
该方法在整个组件生命周期只会被调用一次，所以可以利用该方法做一些组件内部的初始化工作。
#### componentDidMount
这个阶段表示组件对应的 DOM 已经存在，我们可以在这个时候做一些依赖 DOM 的操作或者其他的一些如请求数据，和第三方库整合的操作。如果嵌套了子组件，子组件会比父组件优先渲染，所以这个时候可以获取子组件对应的 DOM。
#### componentWillReceiveProps(newProps)
当组件获取新属性的时候，可以根据新的属性来修改组件状态，注意首次渲染组件时不会调用。
#### shouldComponentUpdate(nextProps, nextState)
接收到新属性或者新状态的时候在 render 前会被调用，该方法让我们决定是否重渲染组件，默认返回 true，也就是每次更新都会重新渲染。如果返回 false，那么不会重渲染组件，借此可以优化应用性能。

``` js
shouldComponentUpdate() {
  return false;
}
```
#### componentWillUpdate(nextProps, nextState)
当组件确定要更新，在 render 之前调用，(?方法中不能使用 setState ，setState 的操作应该在 componentWillReceiveProps 方法中调用)。
#### componentDidUpdate(prevProps,prevState)
更新被应用到 DOM 之后，可以执行组件更新过后的操作。

#### componentWillUnmount
组件将要卸载时调用，一些事件监听和定时器需要解除。

### 组件通信