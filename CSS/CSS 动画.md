# CSS 动画
animation 是 CSS3 中新增加属性，用来实现动画效果的，IE10+、Firefox 以及 Opera 支持 animation 属性。Safari 和 Chrome 支持替代的 -webkit-animation 属性。

### animation
所有动画属性的简写，除了 animation-play-state 属性。

### animation-name
指定 @keyframes 后面紧跟的动画的名字，css 加载的时候会应用该名字的 @keyframes 规则来实现动画。默认值为 none。

### animation-duration
规定动画完成一个周期的时间，默认为 0，单位可以为 ms 或者 s。

### animation-timing-function
规定动画的速度曲线，默认是 ease。

* linear：匀速
* ease：缓慢开始，缓慢结束
* ease-in：缓慢开始
* ease-out：缓慢结束
* ease-in-out：缓慢开始，缓慢结束（和ease稍有区别，差别并不大）
* cubic-bezier(n,n,n)：
* steps

### animation-delay
指定动画开始播放的延迟时间，默认是0 。

### animation-iteration-count
规定动画播放的次数，默认是 1。除了指定数字，也可以设置关键字 infinite，表示无限循环播放。

### animation-direction
规定动画播放的方向，默认值为 normal，表示正常播放动画。

* alternate：在奇数次（1,3,5...）正常播放，而偶数次（2,4,6...）反向播放；
* alternate-reverse：与 alternate 相反。

### animation-play-state
规定动画是否正在运行或者暂停，默认是 running，表示动画正在播放中。

* paused：表示暂停播放。

可以在 Javascript 中使用该属性 `object.style.animationPlayState="paused"` 来暂停动画。

### animation-fill-mode
规定动画时间之外的状态。

* none：默认值，表示动画播放完成后，恢复到初始的状态；
* forwards：表示动画播放完成后，保持 @keyframes 里最后一帧的属性；
* backwards：表示开始播放动画的时候，应用 @keyframes 里第一帧的属性，播放完成的时候，恢复到初始状态，通常设置 animation-delay 后，才能看出效果；
* both：表示 forwards 和 backwards 都应用。

### @keyframes
用来定义动画的各个关键帧。基本写法如下：

``` css
@keyframes animation-name {
  0% {
    background-color: #cd0000;
  }
  50% {
    background-color: deeppink;
  }
  100% {
    background-color: blue;
  }
}
```

也可以使用 from 代替 0%，使用 to 代替 100%。

