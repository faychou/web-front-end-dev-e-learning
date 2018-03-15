# HTML 术语

## 浏览器渲染过程
大致过程如下：

* 浏览器解析 HTML 文档的源码，然后构造出一个 DOM 树，DOM 树的构建过程是一个深度遍历的过程，当前节点的所有子节点都构建好以后才会去构建当前节点的下一个兄弟节点。

* 接下来，浏览器开始对 CSS 文件内容进行解析，一般来说，浏览器会先查找内联样式，然后是 CSS 文件中定义的样式，最后再是浏览器默认的样式，构建 CSS Rule Tree。

* 接着根据 CSS Rule Tree 和 DOM Tree 构建出 Render Tree，DOM 树的节点并不是所有的都会放进 Render Tree 中，比如 header 标签、display:none 的标签等。

* 构建出 Render Tree 后，浏览器已经能知道页面中有哪些节点、各节点的 CSS 样式以及它们的从属关系，从而去计算出每个节点在屏幕中的位置。最后按照计算出的位置，调用系统的 API，把各节点绘制到屏幕上。

## reflow 和 repaint
### 回流(reflow)
如果改变了影响元素布局信息的 CSS 样式，比如 width、height、left、top 等，该元素的位置信息就会发生变化，也可能会导致整个页面其他元素的位置信息都发生变化，所以渲染引擎需要重新执行 layout 过程，重新计算每个元素的位置。reflow 是在浏览器下一帧绘制的时候，进行重新布局，如果修改了元素的布局样式后，立马去获取 offsetTop、scrollTop 等属性，那么渲染引擎就会强制进行重新布局过程，以保证在 JS 中获取到正确的 offsetTop、scrollTop 等属性值。

### 重绘(repaint)
改变某个元素的背景色、文字颜色、边框颜色等等不影响它周围或内部布局的属性时，屏幕的一部分需要重绘，但是元素的几何尺寸不会变化。

`display:none` 的标签不会被加入 Render Tree，也不会触发 reflow，`visibility:hidden` 的标签会被加入到 Render Tree，不会触发 reflow，只会触发 repaint。

#### 减少 reflow/repaint
* 如果需要频繁的修改 DOM 样式，尽量通过预先定义好的 css 的 clsss，然后修改 DOM 的 className；
* 不要把 DOM 节点的属性值放在一个循环里当成循环里的变量；
* 为需要添加动画的 HTML 元素，添加上 `position:absolute/fixed` 属性值，这样修改该元素的 css 是不会引起 reflow；
* 不要使用 table 布局，因为可能一个很小的改动就会引起整个 table 的重新布局。

## BFC
BFC 即 块级格式化上下文，它属于普通文档流，具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。通俗来说，可以把 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海都不会影响到外部。

### 触发 BFC 的条件
只要元素满足下面任一条件即可触发 BFC 特性：

* body 根元素；
* 浮动元素：float 除 none 以外的值；
* 绝对定位元素：position(absolute、fixed)；
* display 为 line-block、table-ceils、flex；
* overflow 除了 visible 以外的值 (hidden、scroll、auto)。

### BFC特性以及应用
* 不同的 BFC 容器下边距不会发生重叠；
* BFC 可以包含浮动元素(清除浮动)；
* BFC 可以阻止元素被浮动元素覆盖，可以用来实现两列自适应布局。
