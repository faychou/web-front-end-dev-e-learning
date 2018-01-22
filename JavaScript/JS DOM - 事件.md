# DOM - 事件
DOM分四个级别，一级，二级，三级，没有零级但是通常把DOM1规范形成之前的称为DOM0。而由于1级DOM标准中并没有定义事件相关的内容，所以DOM事件级别只包括DOM0级，DOM2级和DOM3级三种。

## 事件流
### 事件冒泡
事件从最具体开始向上传递，一直到最不具体为止。

### 事件捕获
事件从最不具体开始，一直向下传递到最具体为止。

### 标准事件流
事件流的方向为：window -> document -> html -> body -> div 目标 -> div -> body -> html -> document -> window

## DOM事件级别
### DOM0级事件
``` html
<button type="button" onclick="log()"></button>

<script>
  function log() {
    console.log('Hello World');
  }
</script>

<!-- 或者 -->
<button id="btn" type="button"></button>

<script>
  var btn = document.getElementById('btn');
  btn.onclick = function() {
    console.log('Hello World');
  }
</script>
```

> 注意：如果想解除事件，那么只要把 null 赋值给事件属性即可，DOM0 级事件无法给一个事件添加多个处理函数。

### DOM2级事件
``` html
<button id="btn" type="button"></button>

<script>
  var btn = document.getElementById('btn');
  function log() {
    console.log('Hello World');
  }
  btn.addEventListener('click', log, false);
</script>
```

DOM2 级事件使用 addEventListener，里面有三个参数：

* type：事件名，事件名前面不带 on
* func：事件处理函数
* bool：是否在事件捕获阶段执行

> 注意：使用DOM2事件可以随意添加多个处理函数，移除DOM2事件要用removeEventListener 。

#### 移除事件
移除 DOM2 事件要用 removeEventListener ，传入的三个参数与添加事件完全相同。

#### 兼容旧版浏览器
使用 attachEvent 和 detachEvent 来添加和移除事件,传入两个参数第一个是事件属性（包含on），第二个是处理函数，不支持事件捕获所以没有第三个参数。

### DOM3级事件
DOM3级事件就是在DOM2基础上增加了更多的事件类型：

* UI事件，当用户与页面上的元素交互时触发，如：load、scroll
* 焦点事件，当元素获得或失去焦点时触发，如：blur、focus
* 鼠标事件，当用户通过鼠标在页面执行操作时触发如：dbclick、mouseup
* 滚轮事件，当使用鼠标滚轮或类似设备时触发，如：mousewheel
* 文本事件，当在文档中输入文本时触发，如：textInput
* 键盘事件，当用户通过键盘在页面上执行操作时触发，如：keydown、keypress
* 合成事件，当为IME（输入法编辑器）输入字符时触发，如：compositionstart
* 变动事件，当底层DOM结构发生变化时触发，如：DOMsubtreeModified