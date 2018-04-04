# CSS 技巧篇
## 文本字体
### vertical-align
vertival-align 代表垂直方向的对齐方式，默认值对齐的是字母的基线，也就是字母 a 落在的那个底线，所以它跟父元素的 font-size 和 line-height 都有关系。
他的取值如下：

* 长度  通过距离升高（正值）或降低（负值）元素。'0cm'等同于'baseline'
* 百分值  通过距离（相对于1line-height1值的百分大小）升高（正值）或降低（负值）元素。'0%'等同于'baseline'
* baseline  默认。元素的基线与父元素的基线对齐。
* sub  降低元素的基线到父元素合适的下标位置。
* super  升高元素的基线到父元素合适的上标位置。
* top  把对齐的子元素的顶端与line box顶端对齐。
* text-top  把元素的顶端与父元素内容区域的顶端对齐。
* middle  元素的中垂点与 父元素的基线加1/2父元素中字母x的高度 对齐。
* bottom  把对齐的子元素的底端与line box底端对齐。
* text-bottom	  把元素的底端与父元素内容区域的底端对齐。
* inherit  采用父元素相关属性的相同的指定值。

vertival-align 只对 inline 元素和 inline-block 元素（或者变种）有效。

#### 案例1
logo + 标题，或者头像 + 名字，使它们垂直居中。

``` html
<div>
  <img src="logo.png">
  <span>faychou</span>
</div>
```

解决方案：是同时对 img 和 span 设置：`vertical-align: middle;`。原因在于每个元素都要分别设置才能统一实现垂直居中。

当然也可以在父元素上设置 `vertical-align: middle;`，然后在每一个行内元素上写 `vertical-align: inherit;`，因为 vertical-align 默认是不继承的。

需要注意的是父元素上不能设置高度（或者说父元素的高度高于任何一个子元素的高度），那么以上方法也会失效的。解决办法是直接在父级上设置高度等于行高，或者再增加一个子元素 span ，给该 span 设置高度等于父元素高度，宽度为 1 。

### 文本截断
#### 1、text-overflow: ellipsis
单行文本截断。

``` css
overflow: hidden;
white-space:nowrap;
text-overflow: ellipsis;
```

#### 2、 line-clamp
css3 中用于控制一段文字的行数显示，只适用于WebKit浏览器及移动端。

``` html
<div class="line-clam">
    Chasing this arbitrary measure only gets in the way. Dedicate yourself to something greater and more concrete and let success come as a byproduct.
</div>
```
``` css
.line-clam {
  overflow: hidden;  
  display: -webkit-box;  
  text-overflow: ellipsis;  
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
```

说明：

* `-webkit-line-clamp`：`<number>`
* `display: -webkit-box`: 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
* `-webkit-box-orient`: 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。
* `text-overflow`: 可以用来多行文本的情况下，用省略号“...”隐藏超出范围的文本 。

#### 3、Element.getClientRects()
根据测试，在 IE8+ 及其他现代浏览器下这个方法对于 `display: inline` 的元素有一个特性：调用结果返回的 `DOMRectList` 对象的 `length` 等于元素渲染后的行数。这样，我们可以把需要计算行数的内容放在一个 `display: inline` 的容器内（比如原来是 `<p> `元素内的文本，现在更改为 `p > span` 这种结构），对该 `<span>` 元素调用 `elem.getClientRects().length` 即可获得行数。

可是目前在 WebKit 下，有一个疑似的 bug：当这个 `display: inline` 的容器内有子元素，`getClientRects` 的结果会包含这些子元素的轮廓，导致计数错误。既然规范并没有详细描述这个方法的计算逻辑，为什么说是一个疑似 bug 呢？因为当给容器加上一些特定的样式，计算结果又会和我们预期的结果相符了。

[demo](http://jsbin.com/juxihisisu/edit?html,css,js,output)

## 表单
### 解决截断非直接输入
``` js
var inputLock = false;

// 限制输入特殊字符
function do(inputElement) {
    var regex = /[^1-9a-zA-Z]/g;
    inputElement.value = inputElement.value.replace(regex, '');
}

inputElement.addEventListener('compositionstart', function() {
  inputLock = true;
});


inputElement.addEventListener('compositionend', function(event) {
  inputLock = false;
  do(event.target);
})

// 在 Web 开发中，经常要对表单元素的输入进行限制，比如说不允许输入特殊字符，标点。通常我们会监听 input 事件
inputElement.addEventListener('input', function(event) {
  if (!inputLock) {
    do(event.target);
    event.returnValue = false;
  }
});
```

## 滚动
### 滚动缓冲
``` css
-webkit-overflow-scrolling: auto  |  touch;

/* 兼容写法 */
over-flow: auto;     /* winphone8和android4+ */
-webkit-overflow-scrolling: touch;    /* ios5+ */
```

* auto： 普通滚动，当手指从触摸屏上移开，滚动立即停止
* touch：滚动回弹效果，当手指从触摸屏上移开，内容会保持一段时间的滚动效果，继续滚动的速度和持续的时间和滚动手势的强烈程度成正比。同时也会创建一个新的堆栈上下文。

> 可能会出现的bug：

* 父级元素加上定位 `position: absolute|relative`，滑动几次后可滚动区域会卡主，不能在滑动；
* 快速滑动页面会出现空白，滑动停止后内容才显示

``` css
/* 给父级元素设置 */
//解决第一个bug
z-index:1;    

//解决第二个bug
-webkit-backface-visibility: hidden;    
-webkit-transform: translate3d(0,0,0);
```

## 线条
### 0.5px 的边
