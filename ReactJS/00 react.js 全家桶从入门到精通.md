# React.js 全家桶从入门到精通
React 起源于 Facebook 的内部项目。用来架设 Instagram 的网站，并于 2013 年 5 月开源。React 并不是一个完整的 MVC 框架，而仅仅只是 V（view） ，是一个用于构建用户界面的 JAVASCRIPT 库，主要用于构建 UI。

React是基于状态驱使的。在整个React开发中，你会体会到，组件的状态贯穿着一切，一切都是基于状态驱使开发的。

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
