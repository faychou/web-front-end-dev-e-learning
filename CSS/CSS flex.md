# Flex
Flex 是 Flexible Box 的缩写，意为”弹性布局”。Flex 可以简便、完整、响应式地实现多种页面布局。

## 基本概念
采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做 main start，结束位置叫做 main end；交叉轴的开始位置叫做 cross start，结束位置叫做 cross end。

Flex 项目默认沿主轴排列。单个项目占据的主轴空间叫做 main size，占据的交叉轴空间叫做 cross size。

Flex 属性分为两部分，一部分作用于容器称容器属性，另一部分作用于项目称为项目属性。

## 容器属性
### 容器定义
``` css
.box {
  display: flex; 
  /* 或者 display: inline-flex */
}
```

### flex-direction
决定主轴的方向（即项目的排列方向）。

``` css
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

* row 表示从左向右排列
* row-reverse 表示从右向左排列
* column 表示从上向下排列
* column-reverse 表示从下向上排列

### flex-wrap
缺省情况下，Flex 项目都排在一条线（又称"轴线"）上，可以通过 flex-wrap 属性让 Flex 项目换行排列。

``` css
.box {
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

* nowrap(缺省)：所有 Flex 项目单行排列
* wrap：所有 Flex 项目多行排列，按从上到下的顺序
* wrap-reverse：所有 Flex 项目多行排列，按从下到上的顺序

### flex-flow
flex-flow 属性是 flex-direction 属性和 flex-wrap 属性的简写形式，默认值为 row nowrap。

``` css
.box {
  flex-flow: <‘flex-direction’> <‘flex-wrap’>;
}
```

### justify-content
定义了项目在主轴上的对齐方式及额外空间的分配方式。

``` css
.box  {
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
}
```

* flex-start(缺省)：从启点线开始顺序排列
* flex-end：相对终点线顺序排列
* center：居中排列
* space-between：项目均匀分布，第一项在启点线，最后一项在终点线
* space-around：项目均匀分布，每一个项目两侧有相同的留白空间，相邻项目之间的距离是两个项目之间留白的和
* space-evenly：项目均匀分布，所有项目之间及项目与边框之间距离相等

### align-items
定义项目在交叉轴上的对齐方式。

``` css
.box {
  align-items: stretch | flex-start | flex-end | center | baseline;
}
```

* stretch(缺省)：交叉轴方向拉伸显示
* flex-start：项目按交叉轴起点线对齐
* flex-end：项目按交叉轴终点线对齐
* center：交叉轴方向项目中间对齐
* baseline：交叉轴方向按第一行文字基线对齐

### align-content
定义了在交叉轴方向的对齐方式及额外空间分配，类似于主轴上 justify-content 的作用。

``` css
.box {
  align-content: stretch | flex-start | flex-end | center | space-between | space-around ;
}
```

* stretch (缺省)：拉伸显示
* flex-start：从启点线开始顺序排列
* flex-end：相对终点线顺序排列
* center：居中排列
* space-between：项目均匀分布，第一项在启点线，最后一项在终点线
* space-around：项目均匀分布，每一个项目两侧有相同的留白空间，相邻项目之间的距离是两个项目之间留白的和

## 项目属性
### order
缺省情况下，Flex 项目是按照在代码中出现的先后顺序排列的。然而 order 属性可以控制项目在容器中的先后顺序。

``` css
.item {
  order: <integer>; /* 整数值 */
}
```

元素按 order 值从小到大排列，可以为负值，缺省为 0。

### flex-grow
flex-grow 属性定义项目的放大比例，flex-grow 值是一个单位的正整数，表示放大的比例。默认为 0，即如果存在额外空间，也不放大，负值无效。

如果所有项目的 flex-grow 属性都为 1，则它们将等分剩余空间（如果有的话）。如果一个项目的 flex-grow 属性为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。

``` css
.item {
  flex-grow: <number>;
}
```

### flex-shrink
定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。0 表示不缩小，负值无效。

``` css
.item {
  flex-shrink: <number>; /* 缺省 1 */
}
```

### flex-basis
定义项目在分配额外空间之前的缺省尺寸。属性值可以是长度（20%，10rem 等）,默认值为 auto，即项目的本来大小。

``` css
.item {
  flex-basis: <length> | auto; /* 缺省 auto */
}
```

### flex
该属性属性是 flex-grow, flex-shrink 和 flex-basis 的简写，默认值为 0 1 auto。后两个是可选属性。

``` css
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

### align-self
定义项目的对齐方式，可覆盖 align-items 属性。默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。

``` css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
