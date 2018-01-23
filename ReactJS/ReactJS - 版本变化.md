# 版本变化
### render 方法能够返回数组了
在以前，一个组件的 render 方法中如果要返回多个 element，必须使用一个元素将它们包裹起来。这样可能会导致很多不必要的嵌套。现在你可以这样写了：

``` js
render() {
  // 不需要再包裹一层了！
  return [
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

### 新增了一个顶级 API: ReactDOM.createPortal
在一般的 React 结构中，组件的嵌套关系和渲染出来的 DOM 的嵌套关系是一致的（子组件渲染出的 DOM 一定是在父组件渲染出的 DOM 的内部的）。

但某些情况下，这样的限制会导致问题，例如实现一个模态框（Modal），虽然模态框所在的组件在它的父组件内部，但是通常需要被渲染在 body 元素下。 新的 API 使用方式如下：

``` js
render() {
  // React 会在你提供的 domNode 下渲染，而不是在当前组件所在的 DOM
  return ReactDOM.createPortal(
    this.props.children,
    domNode,
  );
}
```

### 部分非核心模块被移出
`React.createClass`、`React.PropTypes` 等模块被移出了 react 包，现在你必须从单独的包里引入。

### 增加了 componentDidCatch(error, info) 的生命周期函数
可以定义组件的错误处理函数，这样组件在遇到运行时错误时会像增加了 try...catch 一样，不会将错误直接抛出了，错误被局部组件 catch 住，保证了整个应用的可用性。