# css
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