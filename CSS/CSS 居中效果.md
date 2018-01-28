# 水平居中
## 行内元素居中
``` css
/* 父元素设置 */
.father {
  text-align: center;
}
```

## 块状元素居中
### 宽度固定
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
``` css
.parent {
  text-align: center;
}
.child {
  display: inline-block;
}
```

> 优点：兼容性好。缺点：需要同时设置子元素和父元素。

#### 使用 table 实现 
``` css
div {
  display: table; 
  margin: 0 auto;
}

/* 或者给该 div 嵌套表格标签*/
```

> 优点:只需要对自身进行设置。缺点：IE6,7 需要调整结构。

#### 定位
``` html
<style>
  .wrap {
    position: absolute;
    left:50%;
  }
  .main {
    position: relative;
    left:-50%;
  }
</style>

<div class="wrap">
  <div class="main"></div>
</div>
```

#### 使用位移
``` css
div {
  position: absolute; 
  left: 50%; 
  transform: translate(-50%,0);
  /*或者实用margin-left的负值为盒子宽度的一半也可以实现，不过这样就必须知道盒子的宽度，但兼容性好*/
}
```

#### 使用 flex
``` css
div {
  display: flex; 
  justify-content: center;
}
```

# 垂直居中
## 单行文本居中
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

### 定位
``` html
<style>
.main {
  position: absolute;
  top: 50%;
  margin-top: -150px;
  width: 300px;
  height: 300px;
}
</style>

<div class="main"></div>
```

> 缺点：无法自适应内容的高度。

### 使用 calc
``` html
<style>
.main {
  position: absolute;
  top: calc(50% - 150px);
  width: 300px;
  height: 300px;
}
</style>

<div class="main"></div>
```

### 空标签 + float
``` html
<style>
.space {
  float: left;
  height: 50%;
  margin-top: -150px;
}
.main {
  position: relative;
  clear: left;
  height: 300px;
}
</style>

<div class="space"></div>
<div class="main">
  <div class="inner"></div>
</div>
```

## 块级元素高度自适应
### 使用 css3 中的 translate
``` html
<style>
.main {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
<div class="main"></div>
```

> 缺点：元素脱离文档流。如果需要居中的元素已经在高度上超过了视口，那它的顶部会被视口裁切掉。

### 结合 vh 和 transform
``` html
<style>
.main {
  width: 300px;
  margin: 50vh auto 0;
  transform: translateY(-50%);
}
</style>

<div class="main"></div>
```

### flex
``` html
<style>
.main {
  display: flex;
  height: 100vh;
}
.inner {
  margin: auto;
  /* 或者
  justify-content: center;
  align-items: center;
  */
}
</style>

<div class="main">
  <div class="inner"></div>
</div>
```

当我们使父元素display: flex时，margin: auto不仅可以水平居中，也能够实现垂直居中。这是因为auto外边距会平分水平或垂直方向上的额外空间。

### 表格
``` html
<style>
.container {
  display: table;/* 让div以表格的形式渲染 */
  width: 100%;
}
.inner {
  display: table-cell;/* 让子元素以表格的单元格形式渲染 */
  text-align: center;
  vertical-align: middle;
}
</style>

<div class="main">
  <div class="inner"></div>
</div>
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
``` css
div {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}
```

### 使用 flex
``` css
div {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
