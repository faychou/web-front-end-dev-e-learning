# 水平居中
## 行内元素水平居中
``` css
/* 父元素设置 */
.father {
  text-align: center;
}
```

## 块状元素水平居中
### 宽度固定
``` html
<div>DEMO</div>
```

``` css
/* 一般写法 */
div {
  margin: 0 auto;
  /* 也可以这样写 margin: auto; */
}

/* 最佳写法 */
div {
  margin-left: auto;
  margin-right: auto;
}
```

> 优点：兼容性好。缺点: 需要指定宽度。

### 宽度不固定
#### 使用 inline-block
``` html
<div class="parent">
  <div class="child">DEMO</div>
</div>
```
``` css
.parent {
  text-align:center;
}
.child {
  display:inline-block;
}
```

* 优点：兼容性好，甚至可以兼容 ie6、ie7；
* 缺点：child 里的文字也会水平居中，可以在 .child 添加 `text-align:left;` 还原。

#### 使用 table 实现
``` html
<div class="parent">
  <div class="child">DEMO</div>
</div>
```

``` css
.child {
  display: table; 
  margin: 0 auto;
}

/* 或者给该 div 嵌套表格标签*/
```

> 优点:只需要对自身进行设置。缺点：不支持 IE6、7 ，需要调整结构。

#### 定位
``` html
<div class="parent">
  <div class="child">DEMO</div>
</div>
```

``` css
.parent {
  position: absolute;
  left: 50%;
}
.child {
  position: relative;
  left: -50%;
}
```

#### 使用位移
``` html
<div class="parent">
  <div class="child">DEMO</div>
</div>
```

``` css
.parent {
  position:relative;
}
.child {
  position: absolute; 
  left: 50%; 
  transform: translateX(-50%);
  /*或者实用margin-left的负值为盒子宽度的一半也可以实现，不过这样就必须知道盒子的宽度，但兼容性好*/
}
```

* 优点：居中元素不会对其他的产生影响；
* 缺点：transform 属于 css3 内容，兼容性存在一定问题。

#### 使用 flex
``` css
.parent {
  display: flex; 
  justify-content: center;
}
```

* 优点：设置 parent 即可；
* 缺点：低版本浏览器(ie6 ie7 ie8)不支持。

# 垂直居中
## 单行文本垂直居中
设置 `line-height` 和 `height` 相等即可，但是无法实现两行文本的垂直居中对齐。

``` css
div {
  height: 200px;
  line-height: 200px;
}
```

## 块级元素高度固定
### vertical-align
``` html
<div class="parent">
  <div class="child">demo</div>
</div>
```

``` css
.parent {
  display: table-cell;
  vertical-align: middle;
}
```

* 优点：兼容性较好，ie8 以上均支持。

### 定位
``` html
<div>DEMO</div>
```

``` css
div {
  position: absolute;
  top: 50%;
  margin-top: -150px;
  width: 300px;
  height: 300px;
}
```

> 缺点：无法自适应内容的高度。

### 使用 calc
``` html
<div>DEMO</div>
```

``` css
div {
  position: absolute;
  top: calc(50% - 150px);
  width: 300px;
  height: 300px;
}
```

### 空标签 + float
``` html
<div class="space"></div>
<div class="parent">
  <div class="child">DEMO</div>
</div>
```

``` css
.space {
  float: left;
  height: 50%;
  margin-top: -150px;
}
.parent {
  position: relative;
  clear: left;
  height: 300px;
}
```

* 产生多余的空标签。

## 块级元素高度自适应
### 使用 css3 中的 translate
``` html
<div class="parent">
  <div class="child">DEMO</div>
</div>
```

``` css
.parent {
  position:relative;
}
.child {
  position:absolute;
  top:50%;
  transform:translateY(-50%);
}
```

* 优点：居中元素不会对其他的产生影响
* 缺点：元素脱离文档流。如果需要居中的元素已经在高度上超过了视口，那它的顶部会被视口裁切掉，其次是兼容性存在一定问题。

### 结合 vh 和 transform
``` html
<div>DEMO</div>
```

``` css
div {
  width: 300px;
  margin: 50vh auto 0;
  transform: translateY(-50%);
}
```

### flex
``` html
<div class="parent">
  <div class="child"></div>
</div>
```

``` css
.parent {
  position:flex;
  align-items:center;
}
```

或者使用下面这个方法：

``` css
.parent {
  display: flex;
  height: 100vh;
}
.child {
  margin: auto;
}
```

当我们使父元素 `display: flex` 时，`margin: auto` 不仅可以水平居中，也能够实现垂直居中，这是因为 auto 外边距会平分水平或垂直方向上的额外空间。

* 缺点：兼容性存在一定问题。

### 表格
``` html
<div class="parent">
  <div class="child"></div>
</div>
```

``` css
.parent {
  display: table; /* 让div以表格的形式渲染 */
  width: 100%;
}
.child {
  display: table-cell; /* 让子元素以表格的单元格形式渲染 */
  text-align: center;
  vertical-align: middle;
}
```

# 水平垂直居中
### 使用 vertical-align
``` html
<div class="parent">
  <div class="child">demo</div>
</div>
```

``` css
.parent {
  display: table-cell; 
  vertical-align: middle; 
  text-align: center;
}
.child {
  display: inline-block;
}
```

* 优点：兼容性较好。

### 绝对定位
``` css
/* 方法一 */
div {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 200px;
  height: 200px;
  margin-left: -100px;
  margin-top: -100px;
}

/* 方法二 */
div {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  margin: auto;
}
```

### 使用位移
``` html
<div class="parent">
  <div class="child>DEMO</div>
</div>
```

``` css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}
```

* 优点：child 元素不会对其他元素产生影响；
* 缺点：兼容性存在一定问题。

### 使用 flex
``` html
<div class="parent">
  <div class="child">DEMO</div>
</div>
```

``` css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

* 优点：只设置了 parent；
* 缺点：兼容性存在一定问题。

