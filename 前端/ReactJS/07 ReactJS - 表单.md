# 表单
## 处理用户输入
和普通 HTML 中的 onChange 事件不同， 在原生组件中，只有 input 元素失去焦点才会触发 onChange 事件， 在 React 中，只要元素的值被修改就会触发 onChange 事件。

``` js
class Input extends Component{
  constructor(props) {
    super(props)
    this.state = {
      value: ''
    }
    this.onChangeBubble = this.onChangeBubble.bind(this)
  }
  
  // onChange 事件支持所有组件，可以被用于监听冒泡事件
  onChangeBubble: function(ev) {
    console.log('bubble onChange event', + ev.target.value);
  }
  
  handleChange: function(ev) {
    this.setState({
      value: ev.target.value
    });
  }
  
  render: function() {
    return (
      <p>{ this.state.value }</p>
      <div onChange={this.onChangeBubble}>
        <input value={this.state.value} onChange={(e) => this. handleChange(e)}/>
      </div>
    )
  }
}
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
