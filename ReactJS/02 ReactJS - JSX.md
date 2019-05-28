# JSX
React 提出一种叫 JSX 的语法，这应该是最开始接触 React 最不能接受的设定之一，因为一直都是“表现和逻辑层分离”这种思想为主导。React 发明了 JSX 让 JS 支持嵌入 HTML 之中，不加任何引号。

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

// 变量
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

// 调用函数
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

// if 语句
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
// or
{ isLoading && <Loading /> }
// or
{ isLoading ? <Loading /> : ''}
// or
{isLoggedIn ? (
  <LogoutButton onClick={this.handleLogoutClick} />
) : (
  <LoginButton onClick={this.handleLoginClick} />
)}

// 在属性值中插入一个 JavaScript 表达式
const element = <img src={user.avatarUrl}></img>;

// for 循环
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
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

// 注意的是在一个组件的子元素位置使用注释要用 {} 包起来
{/*注释...*/}
```

### HTML 转义
React 会将所有要显示到 DOM 的字符串转义，防止 XSS。所以如果 JSX 中含有转义后的实体字符比如 &copy; (©) 最后显示到 DOM 中不会正确显示，因为 React 自动把 &copy; 中的特殊字符转义了。有几种解决办法：

* 直接使用 UTF-8 字符 ©
* 使用对应字符的 Unicode 编码，查询编码
* 使用数组组装 <div>{['cc ', <span>&copy;</span>, ' 2015']}</div>
* 直接插入原始的 HTML

``` html
<div dangerouslySetInnerHTML={{__html: 'cc &copy; 2015'}} />
```
