# JSX
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