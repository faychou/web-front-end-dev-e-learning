# 水平居中
## 行内元素居中
父元素设置 `text-align: center;`

## 块状元素居中
### 宽度固定
对该元素设置 `margin: auto` 或 `margin: 0 auto`;

### 宽度不固定
#### 加table标签设置 margin:0 auto

缺点是加了不少的无用标签，代码看起来比较臃肿。

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

# 垂直居中
## 单行文本居中
设置 `line-height` = `height` 即可，但是无法实现两行文本的垂直居中对齐。

## 块级元素高度固定
### 方法一：定位
``` html
<style>
.main {
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -150px;
  margin-top: -150px;
  width: 300px;
  height: 300px;
}
</style>

<div class="main"></div>
```

缺点是无法自适应内容的高度。

### 方法二：calc
``` html
<style>
.main {
  position: absolute;
  left: calc(50% - 150px);
  top: calc(50% - 150px);
  width: 300px;
  height: 300px;
  border: 1px solid red;
}
</style>

<div class="main"></div>
```

### 方法三：空标签 + float
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

缺点：元素脱离文档流。如果需要居中的元素已经在高度上超过了视口，那它的顶部会被视口裁切掉。

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
