# 组件
React 通过组件的思想，将界面拆分成一个个可以复用的模块，每一个模块就是一个 React 组件。一个 React 应用由若干组件组合而成，一个复杂组件也可以由若干简单组件组合而成。

## 组件声明
``` js
//React v16 之前的版本创建组件的方法
var MyComponent = React.createClass({
  render: function() {
    return <h1>hello world!</h1>;
  }
});

//React v16 推荐创建组件方式
class MyComponent extends React.Component {
  render() {
    return <h1>hello world!</h1>;
  }
}

//把组件渲染到页面 body 中
React.render(
  <MyComponent />,
  document.body
);
```

> 注意：

* 每个组件都必须有 render 方法，定义输出的样式；

* React 规定，自定义组件的第一个字母必须大写，比如 MyComponent 不能写成 myComponent ，以便与内置的原生类相区分；

* `<MyComponent/>` 表示生成一个组件类的实例，每个实例一定要有闭合标签，写成 `<MyComponent></MyComponent>` 也可。


## 属性
props 是组件对外的接口，是一个只读属性，上层组件通过 props 向下传递数据，组件内部不能直接修改 props，要想修改 props，只能在该组件的上层组件中修改。

## 状态
每个组件 都有 state 属性，代表当前组件的状态。

``` js

```

### 修改 State
不能直接修改 State，否则组件并不会重新重发 render。

``` js
// 错误
this.state.count = 1;

// 正确
this.setState({
  count:1
});
```

### setState 是异步更新的
调用 setState()，组件的 state 并不会立即改变，因为只是把要修改的状态放入一个队列中，React 出于性能的考虑，可能会将多次 setState 的状态修改合并成一次状态修改。所以不能依赖当前的 state，计算下个 state。因为依赖的 this.state 并不能保证是最新的 state，另外需要注意的是，同样不能依赖当前的 props 计算下个 state，因为 props 的更新也是异步的。

``` js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为避免这个问题，可以使用 setState() 的另一种形式 -- 接受函数而不是对象。该函数有两个参数，第一个参数是组件之前的 state，第二个参数是组件当前最新的 props：

``` js
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

``` js
// ES5
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```

### 状态的类型是数组
如有一个数组类型的状态 books，当向 books 中增加一本书时，使用数组的 concat 方法或 ES6 的数组扩展语法（spread syntax）即可。

``` js
// 方法一：将state先赋值给另外的变量，然后使用concat创建新数组
var books = this.state.books; 
this.setState({
  books: books.concat(['React Guide']);
});

// 方法二：使用preState、concat创建新数组
this.setState(preState => ({
  books: preState.books.concat(['React Guide']);
}));

// 方法三：ES6 spread syntax
this.setState(preState => ({
  books: [...preState.books, 'React Guide'];
}));
```

当需要从 books 中截取部分元素作为新状态时，使用数组的 slice 方法。

``` js
// 方法一：将state先赋值给另外的变量，然后使用slice创建新数组
var books = this.state.books; 
this.setState({
  books: books.slice(1,3);
});

// 方法二：使用preState、slice创建新数组
this.setState(preState => ({
  books: preState.books.slice(1,3);
}));
```

当从 books 中过滤部分元素后，作为新状态时，使用数组的 filter 方法。

``` js
// 方法一：将state先赋值给另外的变量，然后使用filter创建新数组
var books = this.state.books; 
this.setState({  
books: books.filter(item => {   
 return item != 'React'; 
  });
});

// 方法二：使用preState、filter创建新数组
this.setState(preState => ({ 
 books: preState.books.filter(item => {    
 return item != 'React'; 
  });
}));
```

> 注意：不要使用 push、pop、shift、unshift、splice 等方法修改数组类型的状态，因为这些方法都是在原数组的基础上修改，而 concat、slice、filter 会返回一个新的数组。

### 状态的类型是普通对象
使用 ES6 的 Object.assgin 方法。

``` js
// 方法一：将state先赋值给另外的变量，然后使用 Object.assign 创建新对象
var owner = this.state.owner;
this.setState({
  owner: Object.assign({}, owner, {name: 'Jason'});
});

// 方法二：使用 preState、Object.assign 创建新对象
this.setState(preState => ({
  owner: Object.assign({}, preState.owner, {name: 'Jason'});
}))
```

使用对象扩展语法：

``` js
// 方法一：将 state 先赋值给另外的变量，然后使用对象扩展语法创建新对象
var owner = this.state.owner;
this.setState({
  owner: {...owner, name: 'Jason'};
});

// 方法二：使用preState、对象扩展语法创建新对象
this.setState(preState => ({
  owner: {...preState.owner, name: 'Jason'};
}));
```

## 函数组件
函数组件是指没有状态、没有方法的纯组件，也称为无状态组件，是最基础的组件形式，由于没有状态的影响所以就是纯静态展示的作用。如按钮、标签、输入框等。我们应该最大限度地编写和使用这一类组件。编写这类组件的方法通常是使用 ES6 的箭头函数，将 props 作为函数的参数。

``` js
const PureComponent = (props) => (
  <div>
    //use props
  </div>
)

//当然也可以使用 es5 的函数，只是没有 es6 更加直观
function ExpandableForm({ onExpand, expanded = false, children, onSubmit }) {
    const formStyle = expanded ? {height: 'auto'} : {height: 0}
    return (
        <form style={formStyle} onSubmit={onSubmit}>
            {children}
        <button onClick={onExpand}>Expand</button>
        </form>
    )
}
```

### 添加 prop-types
``` js
PureComponent.propTypes = {
  onSubmit: func.isRequired,
  expanded: bool,
  onExpand: func.isRequired
}

const PureComponent = (props) => (
  <div>
    //use props
  </div>
)
```

> 函数声明组件存在一些问题:

* 没有this
* 没有状态，state无法进行动态更改
* 没有生命周期

## 组件嵌套
``` js
// 子组件
const TodoItem = ({text}) => <li>{ text }</li>

// 父组件
class App extends Component {
  constructor(props) {
    super(props)
    this.state = {
      bookList: [
        { id: 0, text: 'javascript' },
        { id: 1, text: 'html' },
        { id: 2, text: 'css' }
      ]
    }
  }

  render() {
    return (
      <div id="app">
        <ol>
		{
          this.state.bookList.map(({ id, text })=>
            <TodoItem key={ id } text={ text }></TodoItem>
          )
        }
		</ol>
      </div>
    )
  }
}
```