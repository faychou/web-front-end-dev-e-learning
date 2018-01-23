# Portal 
默认情况下，React 组件树 是和 DOM 树 完全对应的，但是这样会给部分 UI 组件带来限制，比如常见的蒙层、进度条，React 16 为我们提供的 portal 特性能够让我们把组件树的部分渲染到当前组件树之外。

弹出框案例：

``` js
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Overlay extends Component {
  constructor(props) {
    super(props);

    this.container = document.createElement('div');
    document.body.appendChild(this.container);
  }

  componentWillUnmount() {
    document.body.removeChild(this.container);
  }

  render() {
    return ReactDOM.createPortal(
      <div className="overlay">
        <span className="overlay__close" onClick={this.props.onClose}>
          &times;
        </span>
        {this.props.children}
      </div>,
      this.container
    );
  }
}

class App extends Component {
  constructor(props) {
    super(props);

    this.state = { overlayActive: true };
  }

  closeOverlay() {
    this.setState({ overlayActive: false });
  }

  render() {
    return (
      <div>
        <h2>Dashboard</h2>
        {this.state.overlayActive && <Overlay onClose={this.closeOverlay.bind(this)}>overlay content</Overlay>}
      </div>
    );
  }
}

export default App;
```