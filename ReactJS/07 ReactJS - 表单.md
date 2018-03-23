# 表单
和普通 HTML 中的 onChange 事件不同， 在原生组件中，只有 input 元素失去焦点才会触发 onChange 事件， 在 React 中，只要元素的值被修改就会触发 onChange 事件。

``` js
var MyComponent = React.createClass({
  getInitialState: function() {
    return {
      value: ''
    }
  },
  render: function() {
    return (  
    	<div onChange={this.onChangeBubble}>
        <input value={this.state.value} onChange={this.onChange}/>
      </div>
    )
  },
  onChange: function(ev) {
    console.log('change: ' + ev.target.value);
    this.setState({
      value: ev.target.value
    });
  },
  // onChange 事件支持所有组件，可以被用于监听冒泡事件
  onChangeBubble: function(ev) {
    console.log('bubble onChange event', + ev.target.value);
  }
});
```

在 React 中， TextArea 的使用方式同 input 组件，使用 value 来设置值

``` js
var MyComponent = function() {
  render: function() {
    return (
    	<div>
        <textarea value={...} onChange={...}/>
      </div>
    )
  }
}
```

在 React 中 select 组件支持 value 值，value 值还支持多选

``` html
<select value="B">
  <option value="A">Apple</option>
  <option value="B">Banana</option>
  <option value="C">Cranberry</option>
</select>

<select multiple={true} value={['B', 'C']}>
  <option value="A">Apple</option>
  <option value="B">Banana</option>
  <option value="C">Cranberry</option>
</select>
```

## ref
用来获取实际 DOM 节点。

### 方式一：字符串定义
这个方案已经不被 react 官方推荐，而且会在未来的版本中移除。

``` js
// 在 render 函数里面
<input type="text" ref="user" />

// 获取 DOM
this.refs.user;
```

### 方式二：回调函数
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

### 使用 React.createRef()
在 react 16.3 中，新增 React.createref() 函数使 ref 创建变得更容易。

``` js
// 在 constructor 中声明
this.third = React.createRef();

// 在 render 函数中:
<input type="text" ref={this.third} />;

// 获取 ref
this.third.current;

// 获取 input 的 value 
this.third.current.value;
```
