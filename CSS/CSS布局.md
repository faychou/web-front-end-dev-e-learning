# CSS 布局

## 1 两列布局
### 左边固定、右边自适应
#### 方法一：左边左浮动，右边宽度100%
``` html
<style>
  .left {
    width:300px;
    float: left;
  }
  .right {
    width: 100%;
    margin-left:300px;
  }
</style>

<div class="left">左边固定</div>
<div class="right">右边自适应</div>
```

#### 方法二：右边宽度使用 calc
``` html
<style>
  .left {
    width:300px;
    float: left;
  }
  .right {
    float: left;
    width: calc(100vw - 200px);
  }
</style>

<div class="left">左边固定</div>
<div class="right">右边自适应</div>
```

#### 方法三:父容器设置 display：flex；right 设置 flex：1
``` html
<style>
.father {
  display: flex;
}
.left {
  width:300px;
}
.right {
  flex:1;
}
</style>

<div class="wrap">
  <div class="left">左边固定</div>
  <div class="right">右边自适应</div>
</div>
```

### 左边自适应、右边固定
``` html
<style>
.wrap {
  width:100%;
  float:left;
}
.left {
  margin-right:300px;
}
.right {
  float:left;
  width:300px;
  margin-left:-300px;
}
</style>

<div class="wrap">
  <div class="left"></div>
</div>
<div class="right"></div>
```

## 2 三列布局
以下是我们常见的三列布局：

![三列布局](mdImgs/three.jpg)

一般情况下，我们的布局框架使用以下写法，从上到下，从左到右。

``` html
<header>header</header>
<section>
  <aside>left</aside>
  <section>main</section>
  <aside>right</aside>
</section>
<footer>footer</footer>
```

然而，如果我们希望中部 main 部分优先显示的话，是可以做布局优化的。将 `<section>main</section>` 部分提前即可优先渲染。

``` html
<header>header</header>
<section>
    <section>main</section>
    <aside>left</aside>
    <aside>right</aside>
</section>
<footer>footer</footer>
```

### 2.1 绝对定位
``` html
<style>
.center {
  margin:0 100px;
}
.left, .right{
  width: 100px;
  position: absolute;
  top: 0;
}
.left{ left: 0;}
.right{ right: 0;}
</style>

<div class="center">center</div>
<div class="left">left</div>
<div class="right">right</div>
```
绝对定位是最简单的一种三列布局方式，但是也存在一定的问题。那就是高度不可控，如果 left 部分的高度高于 center ，left 就没有能力撑起整个 wrap 。而以下「圣杯」和「双飞翼」两种方式就能解决这个问题。

### 2.2 浮动
``` html
<style>
.left,.right{
  width: 200px;
}
.left{
  float: left;
}
.right{
  float: right;
}
.center{
  margin: 0 210px;
  overflow: hidden;
}
</style>

<div class='left'>left</div>
<div class='right'>right</div>
<div class='center'>center</div>
```
注意：center 的 div 需要放在最后面。

### 2.3 圣杯
``` html
<style>
.center {
  position: relative;
  float: left;
  width: 100%;
}
.left {
  position: relative;
  float: left;
  width: 100px;
  margin-left: -100%;
  left: -100px;
}
.right {
  position: relative;
  float: left;
  width: 100px;
  margin-left: -100px;
  right: -100px;
}
</style>

<div class="center">main</div>
<div class="left">left</div>
<div class="right">right</div>
```

使用了 relative 相对定位、float 和 负值 margin ，将 left 和 right 部分「安装」到 wrap 的两侧，顾名「圣杯」。

圣杯布局在正常情况下是没有问题的，但是特殊情况下就会暴露此方案的弊端，如果将浏览器无线变窄，「圣杯」将会「破碎」掉。如图，当 center 部分的宽小于 left 部分时就会发生布局混乱。

![圣杯布局缺点](mdImgs/shengbeiwenti.jpg)

于是，淘宝针对「圣杯」的缺点做了优化，并提出「双飞翼」布局。

### 2.4 双飞翼
``` html
<style>
.wrap {
  width: 100%;
  float: left;
}
.center {
  margin: 0 100px;
}
.left {
  float: left;
  width: 100px;
  margin-left: -100%;
}
.right {
  float: left;
  width: 100px;
  margin-left: -100px;
}
</style>
<div class="wrap">
  <div class="center">center</div>
</div>
<div class="left">left</div>
<div class="right">right</div>
```

同样使用了 float 和 负值 margin,不同的是，并没有使用 relative 相对定位 而是增加了 dom 结构，增加了一个层级。确实解决了圣杯布局的缺陷。当然双飞翼布局存在 dom 结构多余，增加渲染树生成的计算量。

### 2.5 flex布局
``` html
<style>
.wrap{
  display: flex;
}
.center{
  flex:1;
  margin: 0 10px;
}
.left{
  width: 200px;
}
.right{
  width: 200px;
}
</style>

<div class='wrap'>
  <div class='left'>left</div>
  <div class='center'>center</div>
  <div class='right'>right</div>
</div>
```

所以最后总结一下，那就是对症下药，没有最好的方案，只有最适合的。
