# 变形
## 2D
### 位移 translate()
可以将元素向指定的方向移动，而不影响在X、Y轴上的任何Web组件。

### 旋转 rotate()
通过指定的角度参数使元素相对原点进行旋转。

``` css
transform: rotate(45deg);
```

### 扭曲 skew()
能够让元素倾斜显示。skew(x,y)使元素在水平和垂直方向同时扭曲（X轴和Y轴同时按一定的角度值进行扭曲变形）；如果第二个参数未提供，则值为0，也就是Y轴方向上无斜切。

### 缩放 scale()
让元素根据中心原点对对象进行缩放。scale(X,Y)使元素水平方向和垂直方向同时缩放（也就是X轴和Y轴同时缩放）;如果没有设置Y值，则表示X，Y两个方向的缩放倍数是一样的。

### 矩阵 matrix()
是一个含六个值的(a,b,c,d,e,f)变换矩阵，用来指定一个2D变换，相当于直接应用一个[a b c d e f]变换矩阵。就是基于水平方向（X轴）和垂直方向（Y轴）重新定位元素。

### 原点 transform-origin
任何一个元素都有一个中心点，默认情况之下，其中心点是居于元素X轴和Y轴的50%处。

``` css
transform-origin: left top;
```
# 过渡
## 过渡属性 transition
通过鼠标的单击、获得焦点，被点击或对元素任何改变中触发，并平滑地以动画效果改变CSS的属性值。 

### transition-property
用来指定过渡或动态模拟的 CSS 属性名称。

> 当 "transition-property" 属性设置为 all 时，表示的是所有中点值的属性。

> 有时我们想改变两个或者多个css属性的transition效果时，只要把几个transition的声明串在一起，用逗号（“，”）隔开，然后各自可以有各自不同的延续时间和其时间的速率变换方式。

### transition-duration
指定完成过渡所需的时间。

### transition-timing-function
指定过渡函数。

* ease：逐渐变慢，默认值；
* linear：匀速；
* ease-in：逐渐加速；
* ease-out：逐渐减速；
* ease-in-out：先加速再减速；

### transition-delay
指定开始出现的延迟时间。

