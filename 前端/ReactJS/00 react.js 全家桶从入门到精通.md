# React.js 全家桶从入门到精通
React 起源于 Facebook 的内部项目。用来架设 Instagram 的网站，并于 2013 年 5 月开源。React 并不是一个完整的 MVC 框架，而仅仅只是 V（view） ，是一个用于构建用户界面的 JavaScript 库，主要用于构建 UI。

React是基于状态驱使的。在整个React开发中，你会体会到，组件的状态贯穿着一切，一切都是基于状态驱使开发的。

React 大体包含下面这些概念：

* 组件:应用都是构建在组件之上。
* JSX
* Virtual DOM
* Data Flow:单向数据绑定

React 实现了一个Virtual DOM，组件 DOM 结构就是映射到这个 Virtual DOM 上，React 在这个 Virtual DOM 上实现了一个 diff 算法，当组件状态 state 有更改的时候,需要重新渲染组件的时候，会通过 diff 寻找到要变更的 DOM 节点，再把这个修改更新到浏览器实际的 DOM 节点上，所以实际上不是真的渲染整个 DOM 树。这个 Virtual DOM 是一个纯粹的 JS 数据结构，所以性能会比原生 DOM 快很多。

## 一、认识react
### 文件引用
``` html
<script src="react.js"></script>
<script src="react-dom.js"></script>
<script src="babel.min.js"></script>

<script type="text/babel">
  <!-- React.js 代码 -->
</script>
```

### Hello World
``` html
<div id="example"></div>

<script type="text/babel">
  ReactDOM.render(
    <span>Hello World !</span>,
    document.getElementById('example')
  );
</script>
```

`ReactDOM.render` 方法接受两个参数：一个虚拟 DOM 节点（要渲染的元素或组件）和一个真实 DOM 节点（在什么位置渲染），作用是将虚拟 DOM 挂载到真实 DOM。
