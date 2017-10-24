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

## 二、JSX
HTML 语言直接写在 JavaScript 语言之中，不加任何引号。

``` js
//...
render:function() {
  var msg = 'hello react !';
  return (
    <div>
      <h1> { msg } </h1>
    </div>
  )
}
//...
```

> 注意：

* HTML元素必须用一个元素包裹；

* {} 包裹 js 表达式：简单变量、数组、函数执行、预定义变量、三元表达式、自执行函数；

* 不能使用 if else 语句，如果要使用，必须放在自执行函数里面；

* 数组循环使用 `array.map(function() {})` ；

* 表达式插入不仅仅可以用在标签内部，也可以用在标签的属性上；

* 标签必须严格闭合；

* 为了避免和 js 中的关键字冲突，在 jsx 中 class 要写成 className ，for 要写成 htmlFor。

``` js
render() {
  return <img alt={this.props.caption} src={this.props.src} />;
}

<h1> { 1 + 1 * 4 } </h1>

//注释需要写在花括号中
{/*注释...*/}
```

## 三、组件

### 组件声明
``` js
//创建一个组件
var MyComponent = React.createClass({
  render: function() {
    return <h1>hello world!</h1>;
  }
});

//把组件渲染到页面body中
React.render(
  <MyComponent />,
  document.body
);
```

> 注意：

* 每个组件都必须有 render 方法，定义输出的样式；

* React 规定，自定义组件的第一个字母必须大写，比如 MyComponent 不能写成 myComponent ，以便与内置的原生类相区分；

* `<MyComponent/>` 表示生成一个组件类的实例，每个实例一定要有闭合标签，写成 `<MyComponent></MyComponent>` 也可。


### 属性
`getDefaultProps` 初始化属性，只调用一次，实例之间共享引用。



### 状态
`getInitialState` 初始化每个实例特有的状态。

``` js
var MyComponent = React.createClass({
  getInitialState: function() {
    return {
      msg:"hello world!"
    }
  },
  render: function() {
    return <h1>{this.state.msg}</h1>;
  }
});
```

#### setState 接受一个函数作为参数
从性能的角度考虑，React 可能会在某次单独的更新中批量执行多个`setState()`。
由于`this.props`和`this.state`可能会异步更新，我们不应该依靠他们的值来计算下一步的状态。

``` js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为避免这个问题，可以使用setState()的另一种形式-接受函数而不是对象。传递给setState的函数将之前的状态作为第一个参数，需增加的属性作为第二个参数：

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

### 函数组件
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

### 组件生命周期
#### componentWillMount
该方法在整个组件生命周期只会被调用一次，所以可以利用该方法做一些组件内部的初始化工作。
#### componentDidMount
这个阶段表示组件对应的 DOM 已经存在，我们可以在这个时候做一些依赖 DOM 的操作或者其他的一些如请求数据，和第三方库整合的操作。如果嵌套了子组件，子组件会比父组件优先渲染，所以这个时候可以获取子组件对应的 DOM。
#### componentWillReceiveProps(newProps)
当组件获取新属性的时候，可以根据新的属性来修改组件状态，注意首次渲染组件时不会调用。
#### shouldComponentUpdate(nextProps, nextState)
接收到新属性或者新状态的时候在 render 前会被调用，该方法让我们有机会决定是否重渲染组件，如果返回 false，那么不会重渲染组件，借此可以优化应用性能。
#### componentWillUpdate(nextProps, nextState)
当组件确定要更新，在 render 之前调用，(?方法中不能使用 setState ，setState 的操作应该在 componentWillReceiveProps 方法中调用)。
#### componentDidUpdate(prevProps,prevState)
更新被应用到 DOM 之后，可以执行组件更新过后的操作。

#### componentWillUnmount
组件将要卸载时调用，一些事件监听和定时器需要解除。

### 组件通信

## 四、事件
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

## 五、表单
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

### ref
``` js
export default class AutoFocus extends Component{
  constructor() {
    super()
  }
  /*组件渲染完毕后textarea自动获得焦点*/ 
  componentDidMount () {
    this.textarea.focus()
  }
  render() {
    return (
      <textarea ref={(textarea) => this.textarea = textarea} />
    )
  }
}
```

## 六、css
React 推荐使用内联样式。

### style
在 React 中，可以直接设置 style 属性来控制样式，不过与 HTML 不同的是， 传入的 style 值为一个对象， 对象的所有 key 都是驼峰式命名：

``` js
render: function() {
  var style = {
    backgroundColor: 'red',
    height: 100,
    width: 100,
    WebkitTransition: 'all', // 如果需要添加浏览器前缀瑞 -webkit-、-ms- 大驼峰(除了 ms )
    msTransition: 'all'      // 'ms' 为小写
  }
  return <div style={style}></div>
}
```

### import css
在 React 中，我们可以为每一个 React 组件引入相应的 CSS 文件：

``` js
import './styles/index.css'
```

## 七、react in ES6、ES7
之前学习的 react 写法是基于 ES5 的，如果使用最新的 ES6 的语法，则有些地方需要做细微的调整：

``` js
import React from 'react'

// 注意加上 export default 是指该组件能够被引用
export default class Person extends React.Component {

  // 构造函数
  constructor (props) {
    super(props)
    
    // 定义 initialState
    this.state = { smiling: false }
    
    // 定义 eventHandler
    this.handleClick = this.handleClick.bind(this)
  }

  // 生命周期方法
  componentWillMount () {}
  componentDidMount () {}
  componentWillUnmount () {}
  

  // getters and setters
  get attr() {}

  // handlers
  handleClick() {}

  // render
  render () {}

}

Person.defaultProps = {
  name: 'Guest'
}

/**
 * 统一都要定义 propTypes
 */
Person.propTypes = {
  name: React.PropTypes.string
}
```

注意以下两种组件写法的区别：

``` js
// 方式一：
import React from 'react'
class Person extends React.Component {
  // ...
}

// 方式二：
import React, { Component } from 'react'
class Person extends Component {
  // ...
}
```

state 、 propTypes 和 defaultProps 的 ES7 写法：

``` js
export default class Person extends Component {
  state = { expanded: false }
    
  static propTypes = {
    model: React.PropTypes.object.isRequired,
    title: React.PropTypes.string
  }

  static defaultProps = {
    model: {
      id: 0
    },
    title: 'Your Name'
  }
  
  constructor(props) {
    super(props);
  }
  
  // ……
}
```

> 注意：propTypes 和 defaultProps 都是组件的静态属性。在组件的代码中，这两个属性的设定位置越高越好。因为这样方便其他阅读代码者或者开发者自己 review，一眼就能看到这些信息。这些信息就如同组件文档一样，对于理解或熟悉当前组件非常重要。

### prop-types
可能大家在使用 propTypes 设置属性类型时会发现控制台有以下警告信息：

``` bash
Accessing PropTypes via the main React package is deprecated, and will be removed in  React v16.0. Use the latest available v15.* prop-types package from npm instead.
```

大致意思是不要在 react 里直接使用 propTypes，而是安装一个 prop-types 包。所以对之前代码稍微修改下：

``` js
//第一步、终端安装：
npm install --save prop-types

//第二步、在需要设置 propTypes 的 js 文件中添加以下代码：
import React,{Component} from 'react'
import PropTypes from 'prop-types'  //引入 prop-types

class MyComponent extends Component {
  static propTypes = {
    title:PropTypes.string  //设置类型
  }
  static defaultProps = {
    title:'使用 prop-types 包'
  }
  constructor() {
    super()
  }
  render() {
    // ... do things with the props
  }
}
```

## 八、高阶组件
高阶组件就是一个函数，它接受一个组件作为参数，返回一个新的组件。这个新的组件会使用传给它的组件作为子组件。

``` javascript
import React, { Component } from 'react'

export default (WrappedComponent) => {
  class NewComponent extends Component {
    // 可以做很多自定义逻辑
    render () {
      return <WrappedComponent />
    }
  }
  return NewComponent
}
```

做为一个高阶组件，可以在原有组件的基础上，对其增加新的功能和行为。我们一般希望编写的组件尽量纯净或者说其中的业务逻辑尽量单一。但是如果各种组件间又需要增加新功能，如打印日志，获取数据和校验数据等和展示无关的逻辑的时候，这些公共的代码就会被重复写很多遍。因此，我们可以抽象出一个高阶组件，用以给基础的组件增加这些功能，类似于插件的效果。

假设我有一个组件，需要从LocalStorage中获取数据，然后渲染出来。于是我们可以这样写组件代码：

``` js
import React, { Component } from 'react'

class MyComponent extends Component {

  componentWillMount() {
    let data = localStorage.getItem('data');
    this.setState({data});
  }

  render() {
    return <div>{this.state.data}</div>
  }
}
```

代码很简单，但当我有其他组件也需要从LocalStorage中获取同样的数据展示出来时，我需要在每个组件都重复componentWillMount中的代码，这显然是很冗余的。下面让我们来看看使用高阶组件可以怎么改写这部分代码：

``` js
import React, { Component } from 'react'

function withPersistentData(WrappedComponent) {
  return class extends Component {
    componentWillMount() {
      let data = localStorage.getItem('data');
        this.setState({data});
    }

    render() {
      // 通过{...this.props} 把传递给当前组件的属性继续传递给被包装的组件WrappedComponent
      return <WrappedComponent data={this.state.data} {...this.props} />
    }
  }
}

class MyComponent2 extends Component {  
  render() {
    return <div>{this.props.data}</div>
  }
}

const MyComponentWithPersistentData = withPersistentData(MyComponent2)
```

## 九、搭建开发环境
### 手动搭建环境

### 快速环境搭建
``` bash
# 环境安装
npm install -g create-react-app

# 创建项目
create-react-app my-app cd my-app

# 运行项目
npm start || npm test || npm run build
```

## 十、路由
### （一）、环境安装
```
npm install react-router-dom --save
```

### （二）、引入
``` javascript
import {
  BrowserRouter as Router,
  Route,
  Link,
  NavLink,
  Switch,
  Redirect
} from 'react-router-dom'
```

### （三）、API
``` js
import { BrowserRouter, Route } from 'react-router-dom'

const PrimaryLayout = () => (
  <div className="primary-layout">
    <header>
      Our React Router 4 App
    </header>
    <main>
      <Route path="/" exact component={HomePage} />
      <Route path="/users" component={UsersPage} />
    </main>
  </div>
)

const HomePage =() => <div>Home Page</div>
const UsersPage = () => <div>Users Page</div>

const App = () => (
  <BrowserRouter>
    <PrimaryLayout />
  </BrowserRouter>
)

render(<App />, document.getElementById('root'))
```

#### BrowserRouter
使用 HTML5 提供的 history API (pushState, replaceState 和 popstate 事件) 来保持 UI 和 URL 的同步。

#### Route
Route组件主要的作用就是当一个location匹配路由的path时，渲染某些UI。

```
<Route exact path="/" component={Home}/>
// or
<Route path="/home" render={() => <div>Home</div>}/>
```

#### exact
包容性路由。V4 的路由默认为“包含”的，这意味着多个 <Route> 可以同时进行匹配和渲染。所以需要加上 exact 那就只渲染一个。所以使用 <Route exact> 可以达到默认路由的效果。

#### Link
为您的应用提供声明式的、无障碍导航。

``` javascript
<Link to="/about">关于</Link>
```

#### NavLink
<NavLink>是 <Link> 的一个特定版本, 会在匹配上当前 URL 的时候会给已经渲染的元素添加样式参数。

```
<NavLink
  to="/faq"
  activeClassName="selected"
>FAQs</NavLink>
```

#### match
通过`this.props.match` 获取URL参数。

#### Switch
如果你只需要在路由列表里匹配一个路由，则使用 <Switch> 来启用排他路由：

``` javascript
const PrimaryLayout = () => (
  <div className="primary-layout">
    <PrimaryHeader />
    <main>
      <Switch>
        <Route path="/" exact component={HomePage} />
        <Route path="/users/add" component={UserAddPage} />
        <Route path="/users" component={UsersPage} />
        <Redirect to="/" />
      </Switch>
    </main>
  </div>
)
```

#### Redirect
渲染`<Redirect>` 的时候将会导航到一个新的地址（location）。这个新的地址（location）将会覆盖在访问历史记录里面的原地址，就像服务端的重定向（HTTP 3XX）一样。

```
<Redirect from="/old-match" to="/will-match"/>
```

#### 嵌套布局
方法一：在技术上可行的，但是会存在重复的问题：

``` js
const PrimaryLayout = props => {
  return (
    <div className="primary-layout">
      <PrimaryHeader />
      <main>
        <Switch>
          <Route path="/" exact component={HomePage} />
          <Route path="/users" exact component={BrowseUsersPage} />
          <Route path="/users/:userId" component={UserProfilePage} />
          <Route path="/products" exact component={BrowseProductsPage} />
          <Route path="/products/:productId" component={ProductProfilePage} />
          <Redirect to="/" />
        </Switch>
      </main>
    </div>
  )
}

const BrowseUsersPage = () => (
  <div className="user-sub-layout">
    <aside>
      <UserNav />
    </aside>
    <div className="primary-content">
      <BrowseUserTable />
    </div>
  </div>
)

const UserProfilePage = props => (
  <div className="user-sub-layout">
    <aside>
      <UserNav />
    </aside>
    <div className="primary-content">
      <UserProfile userId={props.match.params.userId} />
    </div>
  </div>
)
```

另一种更好的方法：

``` js
const PrimaryLayout = props => {
  return (
    <div className="primary-layout">
      <PrimaryHeader />
      <main>
        <Switch>
          <Route path="/" exact component={HomePage} />
          <Route path="/users" component={UserSubLayout} />
          <Route path="/products" component={ProductSubLayout} />
          <Redirect to="/" />
        </Switch>
      </main>
    </div>
  )
}

const UserSubLayout = () => (
  <div className="user-sub-layout">
    <aside>
      <UserNav />
    </aside>
    <div className="primary-content">
      <Switch>
        <Route path="/users" exact component={BrowseUsersPage} />
        <Route path="/users/:userId" component={UserProfilePage} />
      </Switch>
    </div>
  </div>
)

//为了节省重复输入，将 UserSubLayout 改用 props.match.path：
const UserSubLayout = props => (
  <div className="user-sub-layout">
    <aside>
      <UserNav />
    </aside>
    <div className="primary-content">
      <Switch>
        <Route path={props.match.path} exact component={BrowseUsersPage} />
        <Route path={`${props.match.path}/:userId`} component={UserProfilePage} />
      </Switch>
    </div>
  </div>
)
```

#### 简单示例
``` js
import React from 'react'
import {
  BrowserRouter as Router,
  Route,
  Link
} from 'react-router-dom'

const BasicExample = () => (
  <Router>
    <div>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About</Link></li>
        <li><Link to="/topics">Topics</Link></li>
      </ul>

      <hr/>

      <Route exact path="/" component={Home}/>
      <Route path="/about" component={About}/>
      <Route path="/topics" component={Topics}/>
    </div>
  </Router>
)

const Home = () => (
  <div>
    <h2>Home</h2>
  </div>
)

const About = () => (
  <div>
    <h2>About</h2>
  </div>
)

const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>
          Rendering with React
        </Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>
          Components
        </Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>
          Props v. State
        </Link>
      </li>
    </ul>

    <Route path={`${match.url}/:topicId`} component={Topic}/>
    <Route exact path={match.url} render={() => (
      <h3>Please select a topic.</h3>
    )}/>
  </div>
)

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div>
)

export default BasicExample
```

## 十一、数据请求
我们使用 axios 来请求服务器端的数据， axios 是基于 Promise 的 HTTP 请求客户端，可同时在浏览器和 node.js 中使用。

### 安装方法
```
npm install --save axios
```

### 引入
``` js
  import axios from 'axios'
```

### axios
``` js
// 发送 GET 请求 (默认)
axios('/user/12345');

// 发送 POST 请求
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```

### 其它 API
* axios.request(config)
* axios.get(url[, config])
* axios.delete(url[, config])
* axios.head(url[, config])
* axios.options(url[, config])
* axios.post(url[, data[, config]])
* axios.put(url[, data[, config]])
* axios.patch(url[, data[, config]])

### config配置
``` js
{
  // 向服务器发送请求的url
  url: '/user',

  // 请求方法，默认是GET方法
  method: 'get',

  // 基础URL路径，假如url不是绝对路径，如https://some-domain.com/api/v1/login?name=jack,那么向服务器发送请求的URL将会是baseURL + url
  baseURL: 'https://some-domain.com/api/',

  // 允许在请求发送到服务器之前修改该请求，此方法只适用于PUT、POST和PATCH方法中。而且，此方法最后必须返回一个string、ArrayBuffer或者Stream。
  transformRequest: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],

  // 允许在数据传递到then/catch之前修改response数据。此方法最后也要返回数据。
  transformResponse: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],

  // 发送自定义Headers头文件，头文件中包含了http请求的各种信息。
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // 发送请求的查询参数对象，对象中的数据会被拼接成url?param1=value1&param2=value2。
  params: {
    ID: 12345
  },

  // params参数序列化器
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // 发送POST、PUT或者PATCH请求的数据对象。
  data: {
    firstName: 'Fred'
  },

  // 请求超时设置，单位为毫秒。
  timeout: 1000,

  // 表明是否有跨域请求需要用到证书
  withCredentials: false, // default

  // 允许用户处理更易于测试的请求。返回一个Promise和一个有效的response。
  adapter: function (resolve, reject, config) {
    /* ... */
  },

  // 表明提供凭证用于完成http的身份验证。这将会在headers中设置一个Authorization授权信息。自定义Authorization授权要设置在headers中。
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  }

  // 表示服务器将返回响应的数据类型，
  // 有arraybuffer、blob、document、json、text、stream这6个类型，默认是json类似数据。
  responseType: 'json',

  // 用作 xsrf token 值的 cookie 名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // 带有 xsrf token 值 http head 名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

  // `progress` allows handling of progress events for 'POST' and 'PUT uploads'
  // as well as 'GET' downloads
  progress: function(progressEvent) {
    // Do whatever you want with the native progress event
  }
}
```

### 简单案例
GET 请求：

``` js
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (err) {
    console.log(err);
  });

// or
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (err) {
    console.log(err);
  });
```

POST 请求：

``` js
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (err) {
    console.log(err);
  });
```

发送多个并发请求：

``` js
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // Both requests are now complete
  }));
```

### 拦截器
可以在处理 then 或 catch 之前拦截请求和响应。

``` js
// 添加一个请求拦截器
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// 添加一个响应拦截器
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });

// 移除一个拦截器
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

## 十二、redux（状态管理）
容器组件:

UI组件:

### （一）、原则
* 单一数据源、
* state 是只读的
* 使用纯函数进行更改

### （二）、环境安装

```
npm install --save redux
```

### （三）、概念
#### Store
Store 就是保存数据的地方，用来存放整个应用的 state，换句话说 Store 是应用状态 state 的管理者，相当于一个容器，整个应用只能有一个 Store。用来将 action 和 reducer 联系起来。

``` javascript
import { createStore } from 'redux'
const store = createStore(reducer)
```
这里的 createStore 函数只能被调用一次，createStore 方法还可以接受第二个参数，表示 State 的最初状态。注意，如果提供了这个参数，它会覆盖 Reducer 函数的默认初始值。

#### State
当前时刻的 State，可以通过store.getState()拿到：

``` javascript
import { createStore } from 'redux'
const store = createStore(fn)
const state = store.getState()
```

#### Action
是一个描述事件的简单对象，它是改变 store 中 state 的唯一方法。其中的type属性是必须的，表示将要执行的动作。其他属性可以自由设置。

``` javascript
const action = {
    type: 'ADD_TODO',
    payload: 'Learn Redux'
}
```

在实际项目中，action 的值很少会是一个常量，所以可以为每个 action 创建一个工厂方法来返回一个 action 对象，这个函数就叫 Action Creator：

``` javascript
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

#### store.dispatch()
store.dispatch() 将 action 发送到 reducer 函数中，进行状态的更新，这是触发 state 改变的唯一途径。

``` javascript
import { createStore } from 'redux'

const store = createStore(reducer)
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
store.dispatch(addTodo('Learn Redux'))
```

#### Reducer
Action 只是一个描述事件的简单对象，并没有告诉应用该如何更新 state，而这正是 reducer 的工作，负责更新并返回一个新的 state。

用户每次 dispatch(action) 后，都会触发 reducer 的执行。Reducer 是一个纯函数，根据 action.type 来更新 state 并返回 nextState
最后会用 reducer 的返回值 nextState 完全替换掉原来的 state。

``` javascript
var defaultState = 'JavaScript'
function reducer(previousState = defaultState, action) {
  switch (action.type) {
      case 'IOS':
          return action.language;
      case 'WEB':
          return action.language;
      case 'SNACK':
          return action.language;
      default:
          return previousState;
  }
};
```

纯函数：
* 不要修改传入的参数
* 不要执行有副作用的操作，如 API 请求或路由跳转
* 不要调用非纯函数，如 Date.now() 或 Math.random() 等。

``` javascript
const initialState = {
  todos: []
}

function reducer(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      }
    case TOGGLE_TODO:
      return {
        ...state,
        todos: state.todos.map((todo, index) => {
          if (index === action.index) {
            return {
              ...todo,
              completed: !todo.completed
            } // 时刻谨记不要修改 state，保证 reducer 是纯函数
          }
          return todo
        })
      }
    default:
      return state
  }
}
```

#### store.subscribe()
Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。

``` javascript
// 注册监听器，在每次 state 更新时，打印日志
const unsubscribe = store.subscribe(() =>
    console.log(store.getState())
    // 或则state更新时，重新渲染视图
)
```

#### 分散Reducer
随着业务量的增加，Reducer必定也会越来越大，就需要按模块的不同来拆分Reducer，通过combineReducers方法将多个Reducer合并。

需要特别注意，使用combineReducers来合并reducer，需要子reducer的名字跟对应要接收的state的key一致。

``` javascript
import { combineReducers, createStore } from 'redux'
const mainReducer = combineReducers({
    homeReducer,
    profileReducer
})
let store = createStore(mainReducer);
```

#### 简单案例：
``` javascript
import { createStore } from 'redux'
/** 生成 Action */
function inc() {
  return { type: 'INCREMENT' };
}
function dec() {
  return { type: 'DECREMENT' };
}

// reducer 函数
function reducer(state, action) {
  // 设置初始 state
  state = state || { counter: 0 };

  switch (action.type) {
    case 'INCREMENT':
      return { counter: state.counter + 1 };
    case 'DECREMENT':
      return { counter: state.counter - 1 };
    default:
      return state; // 无论如何都返回一个 state
  }
}

var store = createStore(reducer);

console.log( store.getState() ); // { counter: 0 }

store.dispatch(inc());
console.log( store.getState() ); // { counter: 1 }

store.dispatch(inc());
console.log( store.getState() ); // { counter: 2 }

store.dispatch(dec());
console.log( store.getState() ); // { counter: 1 }
```

#### 中间件 applyMiddleware（异步操作）
createStore()方法包含了参数applyMiddleware(),它是Redux的原生方法，作用是 将所有的中间件组成一个数组，依次执行。

操作流程：
* 操作开始时，dispatch action，触发State更新为正在操作状态；
* 操作结束后 再次 dispatch action,获取结果。

解决方案：
写出一个返回函数的 Action Creator，然后使用redux-thunk中间件改造store.dispatch。

``` javascript
import {createStore, applyMiddleware} from 'redux'
import thunk from 'react-thunk'

const store = createStore(
  rootReducer,
  applyMiddleware(thunk)
)

export function fetchPost() {

}
export function requestPost(){}

export function receivePost(){}

export function handleError(err) {

}

export function requstAync() {
  return function(dispatch){
    // 请求发起时 dispatch action
    dispatch(requestPost())
    // 这里的fetchPost 是一个Promise
    return fetchPost()
    .then(response => response.json())
    .then(json =>
     // 请求成功时 dispatch action
      dispatch(receivePost())
    )
    .catch(err =>
     // 请求错误时 dispatch action
      dispatch(handleError(err))
    )
  }
}
```

### （四）、react-redux（react绑定库）
React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。也就是说 UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

#### UI组件特点
* 只负责UI显示，不带任何逻辑
* 无状态组件
* 所有数据都是通过 props 提供
* 不使用任何 Redux API

#### 容器组件特点
* 负责管理数据和业务逻辑，不负责UI显示
* 带有内部状态
* 使用 Redux API

如果遇到一个组件既有UI和业务逻辑时，需要拆分成下面的结构：
外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

#### 安装
``` bash
npm install --save react-redux
```

#### 引入
``` javascript
  import { Provider, connect } from 'react-redux'
```

#### Provider
这个 Provider 其实就是一个中间件，他是在原有 App Container 上面再包一层，他的作用就是接收 store 里面的 store 作为 props，这样一来，App的所有子组件就默认都可以拿到state了。

``` javascript
import { createStore } from 'redux'
import { Provider } from 'react-redux'
const store = createStore(reducer)

<Provider store={store}>
  <App />
</Provider>
```

Provider 是一个 react 组件，提供了一个参数 store，然后渲染了一个子组件，我们通常把路由渲染成子组件：

``` html
<!-- react-router4 -->
<Provider store={store}>
  <Router history={hashHistory}>
    {routes}
  </Router>
</Provider>
```

#### connect()
React-Redux 提供connect方法，用于从 UI 组件生成容器组件。通俗的讲就是 connect 将 state 上的 props（属性）和 方法（dispatch）添加到对应的 UI组件上。

``` javascript
import {connect} from 'react-redux'
import {TodoList} from './TodoList'

function mapStateToProps(state){
  return{
    // do something
  }
}

function mapDispatchToProps(dispatch){
  return{
    // do something
  }
}
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```
说明：

TodoList是 UI 组件，通过 React-Redux 的 connect 方法自动生成为容器组件。

##### connect方法接受两个参数，定义了 UI 组件的业务逻辑：

1、 mapStateToProps（输入逻辑）：外部的数据（即state对象）如何转换为 UI 组件的 props；

2、 mapDispatchToProps（输出逻辑）：用户发出的动作如何变为 Action 对象，从 UI 组件传出去。

#### 案例
``` javascript
import React, { Component } from 'react'
import PropTypes from 'prop-types'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider, connect } from 'react-redux'

// 计数器组件，它是一个纯的 UI 组件
// value 需要从 state 计算得到
// onIncreaseClick 向外发出的 Action
class Counter extends Component {
  render() {
    const { value, onIncreaseClick } = this.props
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncreaseClick}>Increase</button>
      </div>
    )
  }
}

Counter.propTypes = {
  value: PropTypes.number.isRequired,
  onIncreaseClick: PropTypes.func.isRequired
}

// Store
const store = createStore(counter)

// Action
const increaseAction = { type: 'increase' }

// Reducer
function counter(state = { count: 0 }, action) {
  const count = state.count
  switch (action.type) {
    case 'increase':
      return { count: count + 1 }
    default:
      return state
  }
}

// 定义 value 到 state 的映射
function mapStateToProps(state) {
  return {
    value: state.count
  }
}

// onIncreaseClick 到 dispatch 的映射
function mapDispatchToProps(dispatch) {
  return {
    onIncreaseClick: () => dispatch(increaseAction)
  }
}

// 使用 connect 方法生成容器组件
const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('app')
)
```

## react16 版本变化
### render 方法能够返回数组了
在以前，一个组件的 render 方法中如果要返回多个 element，必须使用一个元素将它们包裹起来。这样可能会导致很多不必要的嵌套。现在你可以这样写了：

``` js
render() {
  // 不需要再包裹一层了！
  return [
    // 但是要使用 key
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

还支持了只返回一个字符串：

``` js
render() {
  return 'Look ma, no spans!';
}
```

### 新增了一个顶级 API: ReactDOM.createPortal
在一般的 React 结构中，组件的嵌套关系和渲染出来的 DOM 的嵌套关系是一致的（子组件渲染出的 DOM 一定是在父组件渲染出的 DOM 的内部的）。

但某些情况下，这样的限制会导致问题，例如实现一个模态框（Modal），虽然模态框所在的组件在它的父组件内部，但是通常需要被渲染在 body 元素下。 新的 API 使用方式如下：

``` js
render() {
  // React 会在你提供的 domNode 下渲染，而不是在当前组件所在的 DOM
  return ReactDOM.createPortal(
    this.props.children,
    domNode,
  );
}
```

### 部分非核心模块被移出
`React.createClass`、`React.PropTypes` 等模块被移出了 react 包，现在你必须从单独的包里引入。

### 增加了 componentDidCatch(error, info) 的生命周期函数
可以定义组件的错误处理函数，这样组件在遇到运行时错误时会像增加了 try...catch 一样，不会将错误直接抛出了，错误被局部组件 catch 住，保证了整个应用的可用性。

## react优化
### this 绑定
传统的 `React.createClass` 创建的组件，可以自动绑定 this，但是最新的 class 情况下，this 无法自动绑定。一个常见的解决方案便是：

``` js
onChange = {this.handleChange.bind(this)}
```

这种方法简明扼要，但是有一个潜在的性能问题：当组件每次重新渲染时，都会有一个新的函数创建。

#### 箭头函数绑定
通过箭头函数可以隐式绑定 this ，但是它同样存在潜在的性能问题：

``` js
onChange = {e => this.handleChange(e)}
```

#### Constructor 内绑定
这是一种最佳实践方案，但是 constructor 内绑定在可读性和可维护性上也许有些欠缺。

``` js
constructor(props) {
  super(props);
  this.handleChange = this.handleChange.bind(this);
}
```

#### Class 属性中使用 = 和箭头函数
这个方法依赖于 ES next 的新特性，要求能够使用 stage-2 的特性：

``` js
handleChange = () => {
  // call this function from render 
  // and this.whatever in here works fine.
}
```

### 合理利用解构
``` js
export default class ProfileContainer extends Component {
  // ……
  render() {
    const {model, title} = this.props

    return ( 
      // ……
    )
  }
}
```

如果使用的是函数组件，正常 props 应该是作为函数的参数：

``` js
function MyComponent(props) {
  return (
    <div style={props.style}>{props.children}</div>
  )
}
```

如果使用解构可以这样修改：

``` js
function MyComponent({children, style}) {
  return (
    <div style={style}>{children}</div>
  )
}
```

### 使用展开符
一般写法：

``` js
handleCommentSubmit(comment) {
  let {comments} = this.state
  let newComment = comment

  newComment.id = Date.now()

  let newComments = comments.concat([newComment])

  // ...
}
```

使用展开符后，可以重构为：

``` js
handleCommentSubmit(comment) {
  let {comments} = this.state
  let newComment = {...comment, id: Date.now()}
  let newComments = [...comments, newComment]
  // ...
}
```

### 切割组件
当一个 React 组件功能复杂且臃肿的时候，就需要考虑对该组件进行优化了，因为组件的原则是保持单一性，其中一个优化方法就是对组件的 render 方法进行切割：

``` js
class Panel extends React.Component {
  renderHeading() {
    // ...
  }

  renderBody() {
    // ...
  }

  render() {
    return (
      <div>
        {this.renderHeading()}
        {this.renderBody()}
      </div>
    )
  }
}
```

这种方法虽然对组件进行了 sub-render ，但是该组件仍然保持有原先的 state, props, 以及 methods。而要真正做到减少复杂度，我们可以通过创建函数式子组件的方法直接分离组件：

``` js
const PanelHeader = (props) => (
  // ...
)

const PanelBody = (props) => (
  // ...
)

class Panel extends React.Component {
  render() {
    return (
      <div>
        <PanelHeader title={this.props.title}/>
        <PanelBody content={this.props.content}/>
      </div>
    )
  }
}
```
