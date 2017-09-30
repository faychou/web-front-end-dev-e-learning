# canvas教程
`<canvas>`是一个可以使用脚本(通常为JavaScript)在其中绘制图形的 HTML 元素。
Internet Explorer 9+ 支持。

## 基本结构
``` html
<canvas id="mycanvas" width="200" height="300">
   您的浏览器不支持canvas
</canvas>

<script>
    var cc=document.getElementById("mycanvas");
    var cxt=cc.getContext("2d");
</script>

<!-- 检测 -->
var canvas = document.getElementById('mycanvas');
if (canvas.getContext) {
  var ctx = canvas.getContext('2d'); // 支持
} else { 
  //不支持 
}
```

* getContext()：这个方法是用来获得渲染上下文和它的绘画功能。

canvas是一个二维网格，以左上角坐标为(0,0)。

canvas 的默认大小为300像素×150像素（宽×高，像素的单位是px），但是我们也可以设置 width 和 height 来自定义尺寸。

canvas元素有两套尺寸：一个是元素本身的大小(通过CSS设置)，另一个是元素绘图表面的大小(通过canvas自身的width和height属性设置)。

通过CSS修改width和height，只是改变了元素本身大小，对元素绘图表面的大小并无影响;而通过修改属性width和height，则会同时改变元素本身大小和绘图表面大小。

## 基础绘制
### 绘制样式与颜色
ctx.fill()

ctx.stroke()

### 矩形
#### 1、绘制一个填充的矩形
``` js
fillRect( x ,y ,width, height)
```

* x：矩形起始点的 x 轴坐标。
* y：矩形起始点的 y 轴坐标。
* width：矩形的宽度。
* height：矩形的高度。

案例：

``` js
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
ctx.fillRect(10, 10, 80, 80);
```
#### 2、绘制一个矩形的边框
``` js
strokeRect( x ,y ,width, height)
```

案例：

``` js
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
ctx.strokeRect(10, 10, 80, 80);
```

#### 3、绘制矩形路径：
``` js
rect(x, y, width, height)
```

案例：

``` js
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
ctx.rect(10, 10, 80, 80);
ctx.fill();

ctx.rect(100, 100, 80, 80);
ctx.stroke();
```

#### 4、清除指定矩形区域
``` js
clearRect( x ,y ,width, height)
```

案例：

``` js
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');
ctx.fillRect(10, 10, 80, 80);

ctx.clearRect(20, 20, 30, 30);
ctx.clearRect(60, 60, 10, 10);
```

## Canvas动画
绘制图像动画效果，需要绘制出每一帧的图像，然后在一个极短的时间内从一帧过渡到下一帧，形成动画效果。

### 制作canvas动画的基本步骤
1. 清空canvas：除了背景图像之外，需要清空之前绘制的所有图形；

2. 保存canvas的状态：如果在这一步中你使用了不同的绘图状态（例如描边大小和填充色等），并且你想在绘制每一帧时使用相同的原始状态，你需要保存这些原始状态；

3. 绘制动画图形：这一步中你需要绘制那些动画的图形元素；

4. 恢复canvas状态：如果你之前保存过canvas的状态，在这一步中将它们恢复。

### 控制canvas动画
需要一种方法来在指定时间内执行我们的绘制图形函数。有两种方式可以控制动画的执行。

方法一是使用下面的三个window对象上的方法：

* setInterval(function, delay)：在每delay毫秒时间内反复执行function指定的函数；

* setTimeout(function, delay)：在delay毫秒内执行function指定的函数；

* requestAnimationFrame(callback)：通知浏览器你需要执行一个动画，并请求浏览器调用指定的函数来在下一次重绘前更新动画。

方法二是使用事件监听。例如你需要做一个小游戏，你可以监听键盘和鼠标的事件，然后在捕获相应的事件时使用setTimeout()方法来制作动画效果。

为了获得更好的动画性能，我们通常使用requestAnimationFrame()方法。当浏览器装备好绘制下一帧动画时，我们可以将绘制函数作为参数传入这个方法中。

一个简单的例子：

``` js
function animate() {
 
    reqAnimFrame = window.mozRequestAnimationFrame    ||
                   window.webkitRequestAnimationFrame ||
                   window.msRequestAnimationFrame     ||
                   window.oRequestAnimationFrame
                   ;
 
    reqAnimFrame(animate);
 
    draw();
}
```
* `animate()` 函数首先会获取 `requestAnimationFrame()` 函数的一个引用。注意在不同的浏览器中兼容写法；

* 然后 `reqAnimFrame()` 方法被调用，并将 `animate()` 函数作为参数传入。当浏览器准备好绘制下一帧动画时，`animate()` 函数就会被调用；

* 最后，`animate()` 函数会调用 `draw()` 方法。`draw()` 方法就是前面提到的绘制一个动画帧的4个步骤：清空canvas，保存状态，绘制图形，恢复状态；

* 还有一件需要注意的事情是 `animate()` 函数必须被调用一次来启动动画，否则 `requestAnimationFrame()` 函数将永远不会被调用，动画也不会被正常执行。

完整代码：

``` js
var x =  0;
var y = 15;
var speed = 5;
function animate() {
    reqAnimFrame = window.mozRequestAnimationFrame ||
                window.webkitRequestAnimationFrame ||
                window.msRequestAnimationFrame ||
                window.oRequestAnimationFrame
                ;
    reqAnimFrame(animate);
    x += speed;
    if(x <= 0 || x >= 475){
        speed = -speed;
    }
    draw();
}
function draw() {
    var canvas  = document.getElementById("canvas");
    var context = canvas.getContext("2d");
    context.clearRect(0, 0, 500, 170);
    context.fillStyle = "#ff00ff";
    context.fillRect(x, y, 25, 25);
}
animate();
```

## 粒子动画
