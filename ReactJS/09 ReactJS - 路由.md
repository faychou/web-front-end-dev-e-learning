# React-Router V4
控制不同的 url 渲染不同的组件。

## 一、环境安装
react 路由提供了 react-router 和 react-router-dom 两个包，但是我们在使用的时候两个只要引用一个就行了，不同之处就是后者比前者多出了 `<Link>`、 `<BrowserRouter>` 这样的 DOM 类组件。因此我们只需引用 react-router-dom 这个包。如果搭配 redux，还需要使用 react-router-redux。

```
npm install react-router-dom --save
```

V4 中可以大致分为三类组件:

* router 组件（BrowserRouter，HashRouter）
* route matching 组件（Route，Switch）
* navigation 组件（Link）

## 二、引入方法
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

## 三、组件
简单例子：

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

### Router
Router是所有路由组件共用的底层接口，一般我们的应用并不会使用这个接口，而是使用高级的路由：

* BrowserRouter：使用 HTML5 提供的 history API 来保持 UI 和 URL 的同步。

* HashRouter：使用 URL 的 hash (例如：window.location.hash) 来保持 UI 和 URL 的同步。
* MemoryRouter：能在内存保存你 “URL” 的历史纪录(并没有对地址栏读写)。

* StaticRouter：从不会改变地址。

``` jsx
import { BrowserRouter } from "react-router-dom";
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

> 注意：这里的 `<Router>` 组件下只允许存在一个子元素，如存在多个则会报错。

### Route
Route 组件主要的作用就是负责匹配 url 和对应要展示的组件。

``` jsx
import { Route } from "react-router-dom";

/* 地址匹配的时候渲染该组件，否则渲染 null */
<Rout path="/home" component={Home}/>

/* 行内渲染 */
<Route path="/home" render={() => <div>Home</div>}/>

/* or */
<Route children>
```

有三种渲染的组件的方式：

* component(对应的组件)；
* render(是一个函数，函数里渲染组件)；
* children(不管地址匹配与否都会被调用)。

#### path 属性
路由匹配路径。如果没有设置 path 属性，该 Route 总是会渲染对应的组件。

#### exact 属性
包容性路由。V4 的路由默认为“包含”的，这意味着多个 `<Route>` 可以同时进行匹配和渲染。所以 exact 设置为 true 时，则要求路径与 `location.pathname` 必须完全匹配。

|   路径	 | location.pathname | exact	 | 是否匹配 |
|   :--:   |        :--:       |  :--:  | :--: |
|   /one	 |      /one  	    |  false	 | 是 |
|   /one	 |      /one/two	    |  false	 | 是 |
|   /one	 |       /one/two	 |  true	 | 否 |

``` jsx
<Route exact path="/" component={Home}/>
```

#### strict
值为 true 的时候，有结尾斜线的路径只能匹配有斜线的 `location.pathname`。

| 路径	 | location.pathname | 	strict | 	是否匹配 | 
|   :--:   |        :--:       |  :--:  | :--: |
| /one/	 | /one | 	false | 	是 | 
| /one/	 | /one | 	true | 	否 | 
| /one/	 | /one/ | 	true | 	是 | 
| /one/	 | /one/two | 	true | 	是 | 

### Switch
可以用来将多个 `<Route>` 包裹在一起，只渲染第一个路径匹配的 `<Route>` 或者 `<Redirect>`。比如以下场景：

``` jsx
import { Route, Switch } from "react-router-dom";

<Route path="/about" component={About}/>
<Route path="/:user" component={User}/>
<Route component={NoMatch}/>
```

如果现在的 URL 是 /about，那么 `<About>`, `<User>`, 还有 `<NoMatch>` 都会被渲染，因为它们都与路径(path)匹配。这种设计，允许我们以多种方式将多个 `<Route>` 组合到我们的应用程序中，例如侧栏(sidebars)，面包屑(breadcrumbs)，tabs 等等。 然而，偶尔我们只想选择一个 `<Route>` 来渲染。如果我们现在处于 /about，我们也不希望匹配 /:user（或者显示我们的 “404” 页面 ）。则应该使用 Switch 的方法来实现：

``` jsx
<Switch>
  <Route exact path="/" component={Home}/>
  <Route path="/about" component={About}/>
  <Route path="/:user" component={User}/>
  
  {/* 如果上面的 Route 的路径都没有匹配上，则 <NoMatch> 被渲染 */}
  <Route component={NoMatch}/>
</Switch>
```

如果我们处于 /about，`<Switch>` 将开始寻找匹配的 `<Route>`。`<Route path="/about"/>` 将被匹配， `<Switch>` 将停止寻找匹配并渲染 `<About>`。同样，如果我们处于 /fay，`<User>` 组件将被渲染。

### Redirect
渲染`<Redirect>` 的时候将会导航到一个新的地址（location）。这个新的地址（location）将会覆盖在访问历史记录里面的原地址，就像服务端的重定向（HTTP 3XX）一样。

``` jsx
<Redirect from="/old-match" to="/will-match"/>

<Route exact path="/" render={() => (
  isLogin ? (
    <HomePage/>
  ) : (
    <Redirect to="/home"/>
  )
)}/>
```

### Link
跳转路由时的组件，调用 `history.push` 把改变 url，最终渲染为 a 标签。

``` html
<!-- to（string/object）：要跳转的路径或地址； -->
<Link to="/about">关于</Link>

<!-- to 为 object -->
<Link to={{
  pathname: '/courses',
  search: '?sort=name',
  hash: '#the-hash',
  state: { fromDashboard: true }
}}/>

<!-- 点击链接后将使用新地址替换掉访问历史记录里面的原地址 -->
<Link to="/courses" replace />
```

### NavLink
`<NavLink>` 是 `<Link>` 的一个特定版本, 会在匹配上当前 URL 的时候会给已经渲染的元素添加自定义样式。

``` jsx
/* activeClassName 选中时 class 名字 */
<NavLink
  to="/login"
  activeClassName="selected"
>login</NavLink>

/* 选中时样式为 activeStyle 的设置 */
<NavLink
  to="/login"
  activeStyle={{
    fontWeight: 'bold',
    color: 'red'
   }}
>login</NavLink>

/* 当 event id 为奇数的时候，激活链接 */
const oddEvent = (match, location) => {
  if (!match) {
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink
  to="/events/123"
  isActive={oddEvent}
>Event 123</NavLink>
```

## 四、API
### match
通过 `this.props.match` 获取 URL 参数。

## 五、案例
### 嵌套布局
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
// 简单
<Route path="parent" component={Parent} />;

const Parent = ()=> {
  return (
    <div>
      <Route path="child" component={Child} />
      <Route path="other" component={Other} />
    </div>
  );
}

```

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

### 简单示例
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

## 动态加载组件
``` js
import React, { Suspense } from 'react';
const Description = React.lazy(() => import('./Description'));

function App() {
  return (
    <div>
      <h1>My Movie</h1>
      <Suspense fallback="Loading...">
        <Description />
      </Suspense>
    </div>
  );
}
```

``` js
const Sub1= React.lazy(() => import('./Sub1'))
const Sub2 = React.lazy(() => import('./Sub2'))

class App extends Component {
    render () {
      return (
        <div>
          <React.Suspense fallback={<div>Loading</div>}>
            <Router>
              <Switch>
                <Route path='/sub1' component={Sub1} />
                <Route path='/sub2' component={Sub2} />
              </Switch>
            </Router>
          </React.Suspense>
        </div>
      )
    }
}
```
