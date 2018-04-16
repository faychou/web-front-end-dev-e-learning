# 盒模型
HTML文档中的每个元素在渲染的时候都会被描述成一个矩形盒子，而盒模型正是用来表示每个元素盒子所占用空间大小的模型，CSS盒模型分为W3C标准盒模型和IE盒模型，IE盒模型就是在IE6以下版本的怪异模式下的盒模型，IE6以及更高版本都遵循标准盒模型。在CSS中主要通过四个部分来描述，分别为margin、border、padding、content。一般来说，IE盒模型的宽高计算方式为：width/height = content + padding + border，W3C标准盒模型的宽高计算方式为：width/height = content。

W3C标准盒模型宽高的计算模式在对于非px为单位的宽高时，会带来非常大的计算困扰，如下：

``` css
.box {
    width:50%;
    border:1px solid #ccc;
}
```

这个地方想要的肯定是50%，但是实际的大小趣事50% + 2px，这多出的2px就很让人无奈，于是为了不破坏这个50%的宽度，往往得再内嵌一层元素用来设置border，就成了下面这样：

``` css
.box {
    width:50%;
}
.box .box-inner {
    border:1px solid #ccc;
}
```

这种解决方式显然不太科学，至少导致了HTML结构的臃肿，而box-sizing属性的出现就解决了这个问题，它就是用来改变元素宽高的计算方式。box-sizing属性有两个常用的取值content-box和border-box，如果值为content-box(默认)，则实际宽度为上面所说的计算方式：实际宽高 = border + padding + width/height。如为 border-box 则是另一种计算方式，其实际宽高就是设置的width/height。

浏览器选择哪个盒模型，主要是看浏览器处于标准模式还是怪异模式，在 `<DOCTYPE DTD="">` 标签里有DTD声明，如果有DTD声明，浏览器处于标准模式，没有DTD声明，浏览器处于怪异模式，处于怪异模式的浏览器按照自身的解析方式去解析，IE6会选择IE盒模型，其他现代浏览器都会采用W3C标准盒模型。IE6以下版本的浏览器没有遵循W3C标准，无论页面有没有DTD声明，它都是按照IE盒模型解析代码。

## margin属性
margin:% | px，margin 的值如果为 %，其是根据父元素的宽度来计算的，包括 margin-top 和 margin-bottom，其值也是相对于父元素的宽度。并且内联元素的 margin-top/bottom 是不允许设置的。

位于普通文档流中元素，margin 负值相当于将元素向负值方向移动覆盖，但是只会覆盖颜色，不会覆盖文字。

### 外边距重叠
在同一 BFC 内，两个或多个毗邻（没有空隙 或者 中间没有 border 或 padding）的普通流中的块元素垂直方向上的 margin 会折叠。包括相邻元素、嵌套元素。

其结果是 margin 在垂直方向会合并，其值为两者最大值。

#### 如何解决
只要让它们不在同一个 BFC 内就行。

* 脱离普通文档流：设置 float，absolute。

* 不是块级元素：display: inline-block。

* 创建了块级格式化上下文的元素，如：float（除了 none）、overflow（除了 visible）、display（table-cell/table-caption/inline-block）、position（除了 static/relative） 

针对相邻元素：只要给它们加上 BFC 的外壳，就能使它们的 margin 
不重叠；

针对嵌套元素：只要让父级元素触发 BFC，就能使父级 margin 和当前元素的 margin 不重叠。

这些方法都很麻烦，所以最好开始写的时候就用同一个方向的 margin。

> 注意：外边距重叠是指垂直方向上，margin 在水平方向上是不会合并的。

## padding属性
值如果为%，也是根据父元素宽度来计算的

padding不存在合并的情况

padding也不存在负值情况