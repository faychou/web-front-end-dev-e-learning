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
在JS中context指的是函数的执行上下文，函数被调用时，this指向谁，谁就是当前的执行上下文；

而 React 中的 Context 是组件提供的一个传递数据的方法，从而避免了在每一个层级手动的传递 props 属性。通过 Context 可以在组件之间共享“全局”数据。例如：登陆的用户信息，用户选择的主题、语言等等。

``` jsx
import React, { Component, createContext } from 'react';

const DEFAULT_STATE = {color: 'red'};  
const { Provider, Consumer } = createContext(DEFAULT_STATE); // 生成数据并放入context中

class DeliverComponent extends Component {  
 state = { color: "purple" };

 render() {    
   return (
     // 数据的生产者
     <Provider value={this.state}>
       <MidComponent />
     </Provider>
   )
 }
}
// 中间与context无关的组件
const MidComponent = (props) => <ReceiverComponent />;

// 接收者，需要用到context中的数据
const ReceiverComponent = (props) => (  
 <Consumer>
   {context => <div style={{ color: context.color }}> Hello, this is receiver. </div>}
 </Consumer>
);

ReactDOM.render(  
 <DeliverComponent>
   <MidComponent>
     <ReceiverComponent />
   </MidComponent>
 </DeliverComponent>, document.getElementById('root'));
```

### React.createContext
``` jsx
const MyContext = React.createContext(defaultValue);
```

这个方法用来创建context对象，并包含Provider、Consumer两个组件。当React渲染一个组件，且该组件注册了Context时，它将读取父组件中，距离该组件最近的Provider组件的Context值。

### Context.Provider

通过value属性接收存储的公共状态，来传递给子组件或后代组件

``` jsx
const {Provider} = React.createContext();
<Provider value={/* some value */}>
```

### Context.Consumer
Consumer 是一个监听 context 变化的 React 组件。它使得我们可以在一个函数组件中，监听 context 的改变。

Consumer组件要求其子元素为一个函数。该函数的参数接收当前的context的value值，要求返回一个React节点(node)
传递给该函数的参数value等于距离此Consumner最近的外层Provider组件的context值。如果没有外层的Provider组件，则等于调用createContext()时传递的参数值(context的默认值)。

```js
const {Consumer} = React.createContext();
<Consumer>{value => /* render something based on the context value */}</Consumer>
```

*每当Provider的值发生改变时, 作为Provider后代的所有Consumers都会重新渲染>*

## Redux

一个状态管理工具，详见后文。