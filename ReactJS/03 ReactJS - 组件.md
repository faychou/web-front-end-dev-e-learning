# 组件
React 通过组件的思想，将界面拆分成一个个可以复用的模块，每一个模块就是一个 React 组件。一个 React 应用由若干组件组合而成，一个复杂组件也可以由若干简单组件组合而成。

## 组件声明
``` js
//React v16 之前的版本创建组件的方法
var MyComponent = React.createClass({
  render: function() {
    return <h1>hello world!</h1>;
  }
});

//React v16 推荐创建组件方式
class MyComponent extends React.Component {
  render() {
    return <h1>hello world!</h1>;
  }
}

//把组件渲染到页面 body 中
React.render(
  <MyComponent />,
  document.body
);
```

> 注意：

* 每个组件都必须有 render 方法，定义输出的样式；

* React 规定，自定义组件的第一个字母必须大写，比如 MyComponent 不能写成 myComponent ，以便与内置的原生类相区分；

* `<MyComponent/>` 表示生成一个组件类的实例，每个实例一定要有闭合标签，写成 `<MyComponent></MyComponent>` 也可。


## 属性
props 是组件对外的接口，是一个只读属性，上层组件通过 props 向下传递数据，组件内部不能直接修改 props，要想修改 props，只能在该组件的上层组件中修改。

## 状态
每个组件 都有 state 属性，代表当前组件的状态。

``` js

```

### 修改 State
不能直接修改 State，否则组件并不会重新重发 render。

``` js
// 错误
this.state.count = 1;

// 正确
this.setState({
  count:1
});
```

### setState 是异步更新的
调用 setState()，组件的 state 并不会立即改变，因为只是把要修改的状态放入一个队列中，React 出于性能的考虑，可能会将多次 setState 的状态修改合并成一次状态修改。所以不能依赖当前的 state，计算下个 state。因为依赖的 this.state 并不能保证是最新的 state，另外需要注意的是，同样不能依赖当前的 props 计算下个 state，因为 props 的更新也是异步的。

``` js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为避免这个问题，可以使用 setState() 的另一种形式 -- 接受函数而不是对象。该函数有两个参数，第一个参数是组件之前的 state，第二个参数是组件当前最新的 props：

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

## 函数组件
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

### 添加 prop-types
``` js
PureComponent.propTypes = {
  onSubmit: func.isRequired,
  expanded: bool,
  onExpand: func.isRequired
}

const PureComponent = (props) => (
  <div>
    //use props
  </div>
)
```

## 组件生命周期
### componentWillMount
该方法在整个组件生命周期只会被调用一次，所以可以利用该方法做一些组件内部的初始化工作。

许多开发者为了避免第一次渲染时页面因为没有获取到异步数据导致的白屏，而将数据请求部分的代码放在了 componentWillMount 中，希望可以避免白屏并提早异步请求的发送时间。但事实上在 componentWillMount 执行后，第一次渲染就已经开始了，所以如果在 componentWillMount 执行时还没有获取到异步数据的话，页面首次渲染时也仍然会处于没有异步数据的状态。

而关于提早发送数据请求，官方也鼓励将数据请求部分的代码放在组件的 constructor 中，而不是 componentWillMount。

另一个注意的是 componentWillMount 在服务端渲染时是不会被调用的。
### componentDidMount
这个阶段表示组件对应的 DOM 已经存在，我们可以在这个时候做一些依赖 DOM 的操作、请求数据或者和第三方库整合的操作。如果嵌套了子组件，子组件会比父组件优先渲染，所以这个时候可以获取子组件对应的 DOM。
### componentWillReceiveProps(newProps)
当组件获取新属性的时候，可以根据新的属性来修改组件状态，注意首次渲染组件时不会调用。

### getDerivedStateFromProps(nextProps, prevState)
如果组件自身的某个 state 跟其 props 密切相关的话，在 componentWillReceiveProps 中判断前后两个 props 是否相同，如果不同再将新的 props 更新到相应的 state 上去。这样做一来会破坏 state 数据的单一数据源，导致组件状态变得不可预测，另一方面也会增加组件的重绘次数。

``` js
// before
componentWillReceiveProps(nextProps) {	
  if (nextProps.translateX !== this.props.translateX) {
    this.setState({	
      translateX: nextProps.translateX,	
    });	
  }	
}

// after
static getDerivedStateFromProps(nextProps, prevState) {
  if (nextProps.translateX !== prevState.translateX) {
    return {
      translateX: nextProps.translateX,
    };
  }
  return null;
}
```
### shouldComponentUpdate(nextProps, nextState)
接收到新属性或者新状态的时候在 render 前会被调用，该方法让我们决定是否重渲染组件，默认返回 true，也就是每次更新都会重新渲染。如果返回 false，那么不会重渲染组件，借此可以优化应用性能。

``` js
shouldComponentUpdate() {
  return false;
}
```
### componentWillUpdate(nextProps, nextState)
当组件确定要更新，在 render 之前调用，(?方法中不能使用 setState ，setState 的操作应该在 componentWillReceiveProps 方法中调用)。
### componentDidUpdate(prevProps,prevState)
更新被应用到 DOM 之后，可以执行组件更新过后的操作。

### getSnapshotBeforeUpdate(prevProps, prevState)
getSnapshotBeforeUpdate 会在最终的 render 之前被调用，也就是说在 getSnapshotBeforeUpdate 中读取到的 DOM 元素状态是可以保证与 componentDidUpdate 中一致的。

``` js
class ScrollingList extends React.Component {
  listRef = null;

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // Are we adding new items to the list?
    // Capture the scroll position so we can adjust scroll later.
    if (prevProps.list.length < this.props.list.length) {
      return (
        this.listRef.scrollHeight - this.listRef.scrollTop
      );
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // If we have a snapshot value, we've just added new items.
    // Adjust scroll so these new items don't push the old ones out of view.
    // (snapshot here is the value returned from getSnapshotBeforeUpdate)
    if (snapshot !== null) {
      this.listRef.scrollTop =
        this.listRef.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.setListRef}>
        {/* ...contents... */}
      </div>
    );
  }

  setListRef = ref => {
    this.listRef = ref;
  };
}
```

### componentWillUnmount
组件将要卸载时调用，一些事件监听和定时器需要解除。

> 注意：以下生命周期方法都不能调用 setState：shouldComponentUpdate、componentWillUpdate 和 render 中调用 setState，组件本次的更新还没有执行完成，又会进入新一轮的更新，导致不断循环更新，进入死循环。componentWillUnmount 调用时，组件即将被卸载，setState 是为了更新组件，在一个即将卸载的组件上更新 state 显然是无意义的。

## 组件通信