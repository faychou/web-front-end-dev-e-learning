# ref
用来获取实际 DOM 节点。

## 字符串定义
这个方案已经不被 react 官方推荐，而且会在未来的版本中移除，所以了解一下即可。

``` js
// 在 render 函数里面
<input type="text" ref="user" />

// 获取 DOM
this.refs.user;
```

## 使用回调函数
### 方式一：
``` js
export default class AutoFocus extends Component{
  constructor() {
    super()
  }
  /*组件渲染完毕后 textarea 自动获得焦点*/ 
  componentDidMount () {
    this.textarea.focus()
  }
  render() {
    return (
      <textarea ref={(a) => this.textarea = a} />
    )
  }
}
```

该方式是比较推荐的一种，但是写起来略显麻烦，而且 update 过程中有发生清除可能会有多次调用 (callback 收到 null 的情况)。

### 方式二：
``` js
export default class AutoFocus extends Component{
  constructor() {
    super()
  }
  /*组件渲染完毕后 textarea 自动获得焦点*/ 
  componentDidMount () {
    this.textarea.focus()
  }
  
  // 组件被挂载后，回调函数被立即执行，回调函数的参数为该组件的具体实例
  refDom(node) { 
    console.log(node);
    this.textarea = node;
  }
  render() {
    return (
      <textarea ref={this.refDom} />
    )
  }
}
```

组件被卸载或者原有的 ref 属性本身发生变化时，回调也会被立即执行，此时回调函数参数为 null，以确保内存泄露。

## 使用 React.createRef()
在 react 16.3 中，新增 React.createref() 函数使 ref 创建变得更容易。

``` js
constructor(props) {
  super(props);
  this.input = React.createRef();
  // 或者在 class 中声明：input = React.createRef();
}
componentDidMount() {
  // 获取 ref：this.input.current;
  // 获取 input 的 value：this.input.current.value;
  console.log(this.input);
}
// 在 render 函数中:
<input type="text" ref={this.input} />;
```

> 注意：ref 是不能作为 props 传递的，因为它会在组件一加载时就会触发回调函数。

## ForwardRef API 
在 react 16.3 中还提供了 ForwardRef API 来辅助简化嵌套组件、component 至 element 间的 ref 传递，避免出现 this.ref.ref.ref 的问题。

``` js
import { createRef, forwardRef } from "react";

const MyButton = forwardRef((props, ref) => (
  <button ref={ref}>
    {props.children}
  </button>
));

class App extends Component {
  constructor(props){
    super(props);
    this.realButton = createRef();
  }
  componentDidComponent{
    //直接拿到 inner element ref
    console.log(this.realButton);
  }
  render(){
    return (
    <MyButton ref={this.realButton}>
      Press here
    </MyButton>
    );
  }
}
```

## 为 类(Class) 组件添加 Ref
当 ref 属性用于类(class)声明的自定义组件时，ref 回调函数收到的参数是装载(mounted)的组件实例。


``` js
class AutoFocusTextInput extends React.Component {
  componentDidMount() {
    this.textInput.focus();
  }

  render() {
    return (
      <CustomTextInput
        ref={(input) => { this.textInput = input; }} />
    );
  }
}
```

## findDOMNode
不管 ref 设置值是回调函数还是字符串，都可以通过 `ReactDOM.findDOMNode(ref)` 来获取组件挂载后真正的 DOM 节点。