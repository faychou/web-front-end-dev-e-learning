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

``` css
.index {
  background: red;
}
```

``` js
import './styles/index.css'

class Input extends Component {
  render() {
    return <div className="index" />;
  }
}
```

### 使用 CSS Modules
``` css
<!-- Button.module.css -->
.error {
  background-color: red;
}
```

``` js
// Button.js
import React, { Component } from 'react';
import styles from './Button.module.css';

class Button extends Component {
  render() {
    // reference as a js object
    return <button className={styles.error}>Error Button</button>;
  }
}
```

### 添加 CSS Reset
内置 PostCSS Normalize 作为 css 重置样式。只需要在任意的一个 css 文件中引入一次，推荐 index.css 或者 App.css 引入：

``` css
@import-normalize;
```

