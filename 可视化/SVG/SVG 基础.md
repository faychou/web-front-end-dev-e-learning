# SVG
SVG 是一种基于 XML 语法的图像格式，全称是可缩放矢量图（Scalable Vector Graphics）。本质上是文本文件，体积较小，且不管放大多少倍都不会失真。

SVG 提供了一些元素，用于定义圆形、矩形、简单或复杂的曲线，以及其他形状。一个简单的 SVG 文档由 `<svg>` 根元素和基本的形状元素构成。另外还有一个 g 元素，它用来把若干个基本形状编成一个组。

``` html
<!-- 实例 -->
<svg version="1.1"
  baseProfile="full"
  width="300" height="200"
  xmlns="http://www.w3.org/2000/svg">
  <rect width="100%" height="100%" fill="red" />
  <circle cx="150" cy="100" r="80" fill="green" />
  <text x="150" y="125" font-size="60" text-anchor="middle" fill="white">SVG</text>
</svg>
```

SVG 支持渐变、旋转、滤镜效果、JavaScript 接口等等功能。

## 引入 SVG 方式
* 在 HTML5 标准中可以直接嵌入 SVG。然而为了符合 HTML5 标准，可能需要做一些语法调整。

* 可以通过 object 元素引用 SVG 文件：
`<object data="image.svg" type="image/svg+xml" />`

* 也可以使用 iframe 元素引用 SVG 文件：
`<iframe src="image.svg"></iframe>`

* 同样可以使用 img 元素，但是在低于 4.0 版本的 Firefox 中不起作用。

* SVG 也可以通过 JavaScript 动态创建并注入到 HTML DOM 中。 

## 语法
属性 version 和属性 baseProfile 属性是必不可少的，供其它类型的验证方式确定 SVG 版本。

stroke 属性 : 定义了给定图形元素的外轮廓的颜色。它的默认值是 none。

stroke-width 属性 : 指定了当前对象的轮廓的宽度。它的默认值是 1

stroke-dasharray 属性 : 就是虚线的长度要默认为 none,该值由两个值合写，使用英文逗号(,)分隔，第一个值是画出的每段实线线段的长度，第二个值是各段之间空隙的长度。如果无分隔，则说明两个值都是一样大小的。

stroke-dashoffset 属性 : 表示虚线的起始偏移

fill 属性 : 图形元素的填充色

``` html
<!-- 圆 -->
<svg width="100%" height="100%">
  <circle id="mycircle" cx="50" cy="50" r="50" />
</svg>

```

