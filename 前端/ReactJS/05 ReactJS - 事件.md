# 事件

## 事件命名
React 实现了一个“合成事件”层（synthetic event system），这个事件模型保证了和 W3C 标准保持一致，所以不用担心有什么诡异的用法，并且这个事件层消除了 IE 与 W3C 标准实现之间的兼容问题。React 事件采用驼峰式命名：

``` js
<button onClick={this.handleClick}>按钮</button>
```

## 事件绑定方式
### 元素内绑定 this
``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick() {
    //...
  }

  render() {
    return (
      <button onClick={this.handleClick.bind(this)}>按钮</button>
    );
  }
}
```

这种方式在每次 render 的时候都会重新生成一个新的函数，性能不高。

### 构造器内绑定 this
``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);

    // 新增
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    //...
  }

  render() {
    return (
      <button onClick={this.handleClick}>按钮</button>
    );
  }
}
```

这种方式的好处是每次 render 的时候不会重新创建一个回调函数，没有额外的性能损失。

> 注意：因为 ES6 中，类方法不会默认绑定 this。所以需要在构造函数中为事件回调函数绑定 this；

### 箭头函数
``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick = () => {
    //...
  }

  render() {
    return (
      <button onClick={() => {this.handleClick}}>按钮</button>
    );
  }
}
```

这种方式最大的好处就是不需要考虑 this 的指向问题，因为这种写法保证箭头函数中的 this 指向的总是当前组件。但是也有一个最大的问题，每次 render 调用时，都会重新创建一个事件的回调函数，带来额外的性能开销，当组件的层级越低时，这种开销就越大，因为任何一个上层组件的变化都可能会触发这个组件的 render 方法。

### ES7
``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick = () => {
    //...
  }

  render() {
    return (
      <button onClick={this.handleClick}>按钮</button>
    );
  }
}
```

该特性还处于试验阶段，需要 babel 支持。


## 事件对象以及传参
### bind 传参
``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick(arg,e) {
    //...
  }

  render() {
    return (
      <button onClick={this.handleClick.bind(this,arg)}>按钮</button>
    );
  }
}
```

> 注意： 如果使用 bind 方法来传参，event 这个形参是不写的。

### 箭头函数
``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
  }

  handleClick(arg,e) {
    //...
  }

  render() {
    return (
      <button onClick={(e)=>this.handleClick(arg,e)}>按钮</button>
    );
  }
}
```

关于事件响应的回调函数，需要注意：不管你在回调函数中有没有显式的声明事件参数 Event，React 都会把事件 Event 作为参数传递给回调函数，且参数 Event 的位置总是在其他自定义参数的后面。

> 注意：React 中我们无法通过返回 false 阻止默认行为。必须显式的调用 preventDefault。
