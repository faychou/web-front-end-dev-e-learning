# 版本变化
## render 方法能够返回数组了
在以前，一个组件的 render 方法中如果要返回多个 element，必须使用一个元素将它们包裹起来。这样可能会导致很多不必要的嵌套。v16 版本更新后，可以这样写了：

``` js
render() {
  // 不需要再包裹一层了！
  return [
    "列表：", //TEXT 节点需要用引号包起来
    // 但是要使用 key
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

还支持了只返回一个字符串：

``` js
render() {
  return 'Look ma, no spans!';
}
```

## Fragment
虽然上面 render 的时候可以采用数组写法了，但是写起来依然麻烦，所以 React v16.2 提供了更直接的方法，就是 Fragment:

``` js
render() {
  return (
    <React.Fragment>
      列表：        
      <li>First item</li>
      <li>Second item</li>
      <li>Third item</li>
    </React.Fragment>
  );
}
```

Fragment 本身并不会产生真实的 DOM 节点，因此也不会导致层级嵌套增加。

另外 Fragment 还提供了新的 JSX 简写方式 `<></>`：

``` js
<>
  列表：        
  <li>First item</li>
  <li>Second item</li>
  <li>Third item</li>
</>
```

不过注意如果需要给 Fragment 添加 key prop，是不支持使用简写的：

``` js
<dl>
  {props.items.map(item => (
    // 要传key用不了 <></>
    <Fragment key={item.id}>
      <dt>{item.term}</dt>
      <dd>{item.description}</dd>
    </Fragment>
  ))}
</dl>
```

## ReactDOM.createPortal
在一般的 React 结构中，组件的嵌套关系和渲染出来的 DOM 的嵌套关系是一致的（子组件渲染出的 DOM 一定是在父组件渲染出的 DOM 的内部的）。

但某些情况下，这样的限制会导致问题，例如实现一个模态框（Modal），虽然模态框所在的组件在它的父组件内部，但通常是需要渲染在 body 元素下。 而新增的顶级 API `ReactDOM.createPortal` 则可以解决这个问题，具体使用方式如下：

``` js
class Portal extends Component {
  render(){
    const node = getDOMNode();
    return createPortal(
      this.props.children,
      node 
    ); 
  }
}

class Creater extends Component {
  render(){
    return (
      <div onClick={() => 
        alert("clicked!")
      }>
        <Portal>
          <img src={myImg} />
        </Portal>
      </div>
    ); 
  }
}
```

## Context API
以前的版本中 Context API 是作为未公开的实验性功能存在的，随着越来越多的声音要求对其进行完善，在 v16.3 版本，React 团队重新设计并发布了新的官方 Context API。

使用 Context API 可以更方便的在组件中传递和共享某些 "全局" 数据，这是为了解决以往组件间共享公共数据需要通过多余的 props 进行层层传递的问题 (props drilling)。

Context API 主要适用于全局、不常修改的数据共享。

实现方式，首先第一步，创建一个 Context，并设置默认值：

``` js
const LangContext = React.createContext({
  title:"默认标题"
});
```

然后在顶层通过 Provider 向组件树提供 Context 的访问。这里可以通过传入 value 修改 Context 中的数据，当value变化的时候，涉及的 Consumer 内整个内容将重新 render：

``` js
class App extends React.Component {
  render() {
    return (
      <LangContext.Provider
        value={this.state.lang}
      >
        <Head />
      </LangContext.Provider>
    );
  }
}
```

在需要使用数据的地方，直接用 Context.Consumer 包裹，里面可以传入一个 render 函数，执行时从中取得 Context 的数据。

``` js
const HeadTitle = (props) => {
  return (
    <LangContext.Consumer>
      {lang => 
        <Text>{lang.title}</Text>
      }
    </LangContext.Consumer>
  );
};
```

## 新增 componentDidCatch(error, info) 的生命周期函数
可以定义组件的错误处理函数，这样组件在遇到运行时错误时会像增加了 try...catch 一样，不会将错误直接抛出了，错误被局部组件 catch 住，保证了整个应用的可用性。

``` js
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { error: null };
  }
  componentDidCatch(error, 
   {componentStack}
  ) {
    this.setState({
      error,
      componentStack,
    });
  }
  render() {
    if (this.state.error) {
      return (
        <>
          <h1>报错了.</h1>
          <ErrorPanel {...this.state} />
        </>
      );
    }
    return this.props.children;
  }
}

export default function App(){
  return (
    <ErrorBoundary>
      <Content />
    </ErrorBoundary>
  );
}
```

> 注意：该函数只能捕捉生命周期中的错误 (willMount / render 等方法内)。无法捕捉异步的、事件回调中的错误，要捕捉和覆盖所有场景依然需要配合 window.onerror、Promise.catch、 try/catch 等方式。

## 部分非核心模块被移出
`React.createClass`、`React.PropTypes` 等模块被移出了 react 包，现在你必须从单独的包里引入。
