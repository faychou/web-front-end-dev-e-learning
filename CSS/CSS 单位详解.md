# CSS 中的单位
# 像素（px）

# 百分数（%）

# em
em 为字体而生的，作为 font-size 的单位时，其代表父元素的字体大小，em 作为其他属性单位时，代表自身字体大小。

# rem
rem 是指相对于根元素（html）的字体大小的单位。

``` css
/* 作用于根元素，相对于原始大小（16px），所以 html 的 font-size 为 32px */
html {font-size: 2rem;}

/* 作用于非根元素，相对于根元素字体大小，所以为64px */
p {font-size: 2rem;}
```

如果让 html 元素字体的大小，恒等于屏幕宽度的 1/100：

``` css
html {fons-size: width / 100}
p {width: 50rem} /* 50rem = 屏幕宽度的50% */
```

如何让 html 字体大小一直等于屏幕宽度的百分之一呢？ 可以通过 js 来设置，一般需要在页面 DOM ready、resize 和 屏幕旋转 中设置：

``` js
document.documentElement.style.fontSize = document.documentElement.clientWidth / 100 + 'px';
```

那么如何把 UI 图中的获取的像素单位的值，转换为已 rem 为单位的值呢？公式是元素宽度 / UE图宽度 * 100，让我们举个例子，假设 UI 图尺寸是 640px，UI 图中的一个元素宽度是 100px，根据公式 100/640*100 = 15.625 ：

``` css
p {width: 15.625rem}
```

上面的计算过程通过 sass 的 function 来简化过程，less 类似：

``` sass
$ue-width: 640; /* ue图的宽度 */

@function px2rem($px) {
  @return #{$px/$ue-width*100}rem;
}

p {
  width: px2rem(100);
}
```

# vw & vh
vw —— 视口宽度的 1/100；vh —— 视口高度的 1/100 。

``` css
/* rem方案 */
html {fons-size: width / 100}
p {width: 15.625rem}

/* vw方案 */
p {width: 15.625vw}
```

vw还可以和rem方案结合，这样计算html字体大小就不需要用js了：

``` css
html {fons-size: 1vw} /* 1vw = width / 100 */
p {width: 15.625rem}
```

> 注意：在使用弹性布局时，一般会限制最大宽度，比如在 pc 端查看我们的页面，此时 vw 就无法力不从心了，因为除了 width 有 max-width ，其他单位都没有，而 rem 可以通过控制 html 根元素的 font-size 最大值，而轻松解决这个问题。

# 布局方案
通过上面可以得出最好的弹性布局方案是，rem+js 方案，同时还要解决 noscript 问题，解决字体问题，解决屏幕过宽问题。但是上面的方案还有个问题，就是分成 100 份的话，假设屏幕宽度 320px，此时 html 大小是 3.2px，但浏览器支持最小字体大小是 12px，怎么办？那就把上面的 100 都换成 10 份：

html代码如下：

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">

  <title>rem布局</title>
</head>
<body>
  <noscript>开启JavaScript，获得更好的体验</noscript>

  <div class="p1">
    宽度为屏幕宽度的50%，字体大小1.2em
    <div class="s1">
      字体大小1.2.em
    </div>
  </div>

  <div class="p2">
    宽度为屏幕宽度的40%，字体大小默认
    <div class="s2">
      字体大小1.2em
    </div>
  </div>
</body>
</html>
```

css代码如下

``` css
html {
    font-size: 32px; /* 320/10 */
}
body {
    font-size: 16px; /* 修正字体大小 */
    /* 防止页面过宽 */
    margin: auto;
    padding: 0;
    width: 10rem;
    /* 防止页面过宽 */
    outline: 1px dashed green;
}

/* js被禁止的回退方案 */
@media screen and (min-width: 320px) {
    html {font-size: 32px}
    body {font-size: 16px;}
}
@media screen and (min-width: 481px) and (max-width:640px) {
    html {font-size: 48px}
    body {font-size: 18px;}
}
@media screen and (min-width: 641px) {
    html {font-size: 64px}
    body {font-size: 20px;}
}

noscript {
    display: block;
    border: 1px solid #d6e9c6;
    padding: 3px 5px;
    background: #dff0d8;
    color: #3c763d;
}
/* js被禁止的回退方案 */

.p1, .p2 {
    border: 1px solid red;
    margin: 10px 0;
}

.p1 {
    width: 5rem;
    height: 5rem;

    font-size: 1.2em; /* 字体使用em */
}

.s1 {
    font-size: 1.2em; /* 字体使用em */
}

.p2 {
    width: 4rem;
    height: 4rem;
}
.s2 {
    font-size: 1.2em /* 字体使用em */
}
```

js代码如下：

``` js
var documentElement = document.documentElement;

function callback() {
    var clientWidth = documentElement.clientWidth;
    // 屏幕宽度大于780，不在放大
    clientWidth = clientWidth < 780 ? clientWidth : 780;
    documentElement.style.fontSize = clientWidth / 10 + 'px';
}

document.addEventListener('DOMContentLoaded', callback);
window.addEventListener('orientationchange' in window ? 'orientationchange' : 'resize', callback);
```