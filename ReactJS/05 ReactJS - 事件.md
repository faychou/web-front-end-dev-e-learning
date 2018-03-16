# 事件
React事件采用驼峰命名。

``` js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

> 注意：

> 1、在JavaScript中，类方法不会默认绑定this。如果忘记绑定this.handleClick而将其直接传递给onClick，当函数被调用时，this的值是undefined的。

> 2、React中我们无法通过返回false阻止默认行为。必须显式的调用preventDefault。