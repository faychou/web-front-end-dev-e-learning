# 组件生命周期
### getInitialState
初始化 this.state 的值，只在组件装载之前调用一次。如果是使用 ES6 的语法，则是在构造函数中初始化状态。

``` js
class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = { count: props.initialCount };
  }

  render() {
    // ...
  }
}
```

### getDefaultProps
只在组件创建时调用一次并缓存返回的对象。如果是使用 ES6 语法，可以直接定义 defaultProps 这个类属性来替代，这样能更直观的知道 default props 是预先定义好的对象值：

``` js
Counter.defaultProps = { initialCount: 0 };
```

### componentWillMount[即将废弃]
该方法在整个组件生命周期只会被调用一次，所以可以利用该方法做一些组件内部的初始化工作。

许多开发者为了避免第一次渲染时页面因为没有获取到异步数据导致的白屏，而将数据请求部分的代码放在了 componentWillMount 中，希望可以避免白屏并提早异步请求的发送时间。但事实上在 componentWillMount 执行后，第一次渲染就已经开始了，所以如果在 componentWillMount 执行时还没有获取到异步数据的话，页面首次渲染时也仍然会处于没有异步数据的状态。

而关于提早发送数据请求，官方也鼓励将数据请求部分的代码放在组件的 constructor 中，而不是 componentWillMount。

另一个注意的是 componentWillMount 在服务端渲染时是不会被调用的。
### componentDidMount
在 render 之后调用一次，这个阶段表示组件对应的 DOM 已经存在，我们可以在这个时候做一些依赖 DOM 的操作（如：动画的启动，输入框自动聚焦）、请求数据或者和第三方库整合的操作。如果嵌套了子组件，子组件会比父组件优先渲染，所以这个时候可以获取子组件对应的 DOM。可以通过 `this.getDOMNode()` 或者 `ReactDOM.findDOMNode(this)` 获取和操作 dom 节点。
### componentWillReceiveProps(newProps)[16.3废弃]
当组件获取新 props 时调用，可以根据新的属性来修改组件状态，注意首次渲染组件时不会调用。
### shouldComponentUpdate(nextProps, nextState)
接收到新 props 或者新 state 的时候在 render 前会被调用，该方法让我们决定是否重渲染组件，默认返回 true，也就是每次更新都会重新渲染。如果返回 false，那么不会重渲染组件，是 React 性能优化非常重要的手段。我们可以设置在此对比前后两个 props 和 state 是否相同，如果相同则返回 false 阻止更新，因为相同的属性状态一定会生成相同的 dom 树，这样就不需要创造新的 dom 树和旧的 dom 树进行 diff 算法对比，节省大量性能，尤其是在 dom 结构复杂的时候。不过调用 `this.forceUpdate` 会跳过此步骤。

``` js
shouldComponentUpdate() {
  return false;
}
```
### componentWillUpdate(nextProps, nextState)
当组件确定要更新，在 render 之前调用。
### componentDidUpdate(prevProps,prevState)
更新被应用到 DOM 之后，可以执行组件更新过后的操作，如可以获取 dom 节点。

### componentWillUnmount
组件将要卸载时调用，一些事件监听和定时器需要解除。

### getDerivedStateFromProps(nextProps, prevState)[新增]
用于替换 componentWillMount，如果组件自身的某个 state 跟其 props 密切相关的话，在 componentWillReceiveProps 中判断前后两个 props 是否相同，如果不同再将新的 props 更新到相应的 state 上去。这样做一来会破坏 state 数据的单一数据源，导致组件状态变得不可预测，另一方面也会增加组件的重绘次数。

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

### getSnapshotBeforeUpdate(prevProps, prevState)[新增]
可以用于替换 componentWillUpdate，getSnapshotBeforeUpdate 会在最终的 render 之前被调用，也就是说在 getSnapshotBeforeUpdate 中读取到的 DOM 元素状态是可以保证与 componentDidUpdate 中一致的。

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

``` js
// 新版的建议生命周期
class Component extends React.Component {
  // 替换 `componentWillReceiveProps` ，
  // 初始化和 update 时被调用
  // 静态函数，无法使用 this
  static getDerivedStateFromProps(nextProps, prevState) {
    // 当新 props 中的 data 发生变化时，同步更新到 state 上
	if (nextProps.data !== prevState.data) {
		return {
			data: nextProps.data
		}
	} else {
		return null1
	}
  }
  
  constructor() {
    super()
    // 初始化 state
  }
  
  // 判断是否需要更新组件
  // 可以用于组件性能优化
  shouldComponentUpdate(nextProps, nextState) {}
  
  // 组件被挂载后触发
  componentDidMount() {
    // 事件监听、数据的请求
  }
  
  // 替换 componentWillUpdate
  // 可以在更新之前获取最新 dom 数据
  getSnapshotBeforeUpdate() {}
  
  // 组件更新后调用
  componentDidUpdate(prevProps) {
    // 当 id 发生变化时，重新获取数据
	if (this.props.id !== prevProps.id) {
		this.fetchData(this.props.id);
	}
  }
  
  // 组件即将销毁
  componentWillUnmount() {
    // 解绑事件
  }
  
  // 组件已销毁
  componentDidUnMount() {}
}
```

> 注意：以下生命周期方法都不能调用 setState：shouldComponentUpdate、componentWillUpdate 和 render 中调用 setState，组件本次的更新还没有执行完成，又会进入新一轮的更新，导致不断循环更新，进入死循环。componentWillUnmount 调用时，组件即将被卸载，setState 是为了更新组件，在一个即将卸载的组件上更新 state 显然是无意义的。