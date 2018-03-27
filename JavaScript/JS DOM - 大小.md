# DOM 大小
scrollHeight：即真实内容的高度，包含滚动条高度；

clientHeight：视窗的高度，就是我们在浏览器中所能看到内容的高度；

scrollTop：视窗上面隐藏掉的部分。

``` js
// 页面内容高度
var pageHeight = Math.max(document.body.scrollHeight,document.body.offsetHeight);

// 视窗高度
var viewportHeight = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight || 0;

// 滚动条高度
var scrollHeight = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0;
```