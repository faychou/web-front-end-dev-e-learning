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

### offsetHeight || offsetWidth
offsetHeight || offsetWidth = boder + padding + content；两者都是只读属性。

### offsetLeft || offsetTop
一般是相对于offsetParent计算的，也就是第一个定位的父级，两者都是只读属性。

#### offsetTop 与 style.top 的差别
* offsetTop 返回的是数字，而 style.top 返回的是字符串，除了数字外还带有单位：px。
* offsetTop 只读，而 style.top 可读写。
* 若是没有给 HTML 元素指定过 top 样式，则 style.top 返回的是空字符串。

offsetLeft 与 style.left、offsetWidth 与 style.width、offsetHeight 与 style.height 也是同样事理。
