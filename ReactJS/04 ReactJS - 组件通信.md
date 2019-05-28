# 组件通信
## props
父组件通过向子组件传递 props，子组件得到 props 后进行相应的处理。

``` jsx
class Parent extends Component{
  state = {
    msg: 'start'
  };

  render() {
    return <Child parms={this.state.msg} />;
  }
}

class Child extends Component{
  render() {
    return <p>{this.props.parms}</p>
  }
}
```

## 事件
利用回调函数，实现子组件向父组件通信：父组件将一个函数作为 props 传递给子组件，子组件调用该回调函数。

``` js
// 父组件
export default class App extends React.Component {
  callback(msg) {
    console.log(msg);
  }
  render(){
    return(
      <div>
        <Child callback = { this.callback.bind(this) } />
      </div>
    )
  }
}

// 子组件
class Child extends React.component{
  construtor(props){
    super(props)
    this.state = {}
  }
  cb = (msg) => {
    this.props.callback(msg);
  }
  render(){
    return(
      <div>
        <button onClick = { this.cb("通信") }>点击我</button>
      </div>
    )
  }
}
```

## Context
Context 可以在组件之间共享“全局”数据。例如：登陆的用户信息，用户选择的主题、语言等等。

``` jsx
const ThemeContext = React.createContext('light');

class App extends Component {
  render() {
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar></Toolbar>
      </ThemeContext.Provider>
    );
  }
}

const Toolbar = (props)=> {
  <div>
    <ThemedButton />
  </div>
};

class ThemedButton extends Component {
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

### React.createContext
``` jsx
const MyContext = React.createContext(defaultValue);
```

创建一个新的Context对象。当React渲染一个组件，且该组件注册了Context时，它将读取父组件中，距离该组件最近的Provider组件的Context值。

### Context.Provider
``` jsx
<MyContext.Provider value={/* some value */}>
```

每个 Context 对象都携带一个名叫 Provider 的 React 组件。Provider 可以使得“Consumer”组件监听 context 的变更。

### Context.Consumer
Consumer 是一个监听 context 变化的 React 组件。它使得我们可以在一个函数组件中，监听 contxt 的改变。

Consumer组件要求其子元素为一个函数。该函数的参数接收当前的context的value值，要求返回一个React节点(node)
传递给该函数的参数value等于距离此Consumner最近的外层Provider组件的context值。如果没有外层的Provider组件，则等于调用createContext()时传递的参数值(context的默认值)。

## Redux
一个状态管理工具，详见后文。