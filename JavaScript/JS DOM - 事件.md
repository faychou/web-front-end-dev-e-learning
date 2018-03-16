# DOM - 事件
DOM 分四个级别，一级，二级，三级，没有零级但是通常把 DOM1 规范形成之前的称为 DOM0 级。而由于1级 DOM 标准中并没有定义事件相关的内容，所以 DOM 事件级别只包括 DOM0 级，DOM2 级和 DOM3 级三种。

事件指可以被 JavaScript 侦测到的行为。即鼠标点击、页面或图像载入、鼠标悬浮、在表单中选取输入框、确认表单、键盘按键等操作。事件通常与函数配合使用，当事件发生时函数才会执行。我们把响应某个事件的函数称为事件处理程序，一般是在事件的前面加上 on ，如 onclick。

## 事件流
### 事件冒泡
事件从最具体开始向上传递，一直到最不具体为止。即从目标元素开始向上逐级传播最终至 window。

### 事件捕获
事件从最不具体开始，一直向下传递到最具体为止。即从 window 逐级向下传播到目标元素。

### 标准事件流
事件流的方向为：window -> document -> html -> body -> div 目标 -> div -> body -> html -> document -> window

## DOM 事件
### DOM0 级事件处理程序
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

如果想解除事件，那么只要把 null 赋值给事件属性即可，DOM0 级事件无法给一个事件添加多个处理函数。

``` js
btn.onclick = null; //删除指定的事件处理程序
```

> 注意：DOM0 级事件处理程序不能添加多个，也不能控制事件流到底是捕获还是冒泡。

### DOM2 级事件处理程序
#### 添加事件
``` js
addEventListener() //添加事件侦听器
```

函数有3个参数：

* 参数一是要处理的事件名(不带 on 前缀的)；
* 参数二是作为事件处理程序的函数；
* 参数三是一个 boolean 值，默认 false 表示使用冒泡机制，true 表示捕获机制。

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

#### 移除事件
``` js
removeEventListener() //删除事件侦听器
```

传入的三个参数与添加事件完全相同。

``` js
btn.removeEventListener('click',log，false);
```


> 注意：使用 DOM2 级事件处理程序可以绑定多个事件处理程序。

``` js
btn.addEventListener('click',fn1，false);
btn.addEventListener('click',fn2，false);
```

#### IE 事件处理程序
需要注意的是 IE9 以下是不支持标准的 addEventListener 方法的，需要用以下的兼容写法。

``` js
//添加事件
attachEvent(on + type,fn)

//移除事件
detachEvent(on + type,fn)
```

第一个参数是事件处理程序名称，注意的是在事件前面加 on（onclick），第二个参数是事件处理函数。

> 注意：

> 1、IE 事件处理程序只支持冒泡事件流，所以没有第三个参数。

> 2、事件触发的顺序不是添加的顺序而是添加顺序的相反顺序。

> 3、使用 attachEvent 方法 this 的值会变成 window 对象的引用而不是触发事件的元素。

#### 通用事件处理
兼容 IE 浏览器和标准的浏览器。

``` js
var EventUtil = {
  addHandler: function (element, type, handler) {
    if (element.addEventListener) {
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent) {
      element.attachEvent("on" + type, handler);
    } else {
      element["on" + type] = handler;
    }
  },
  removeHandler: function (element, type, handler) {
    if (element.removeEventListener()) {
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent) {
      element.detachEvent("on" + type, handler);
    } else {
      element["on" + type] = null;
    }
  }
};
```

### DOM3 级事件处理程序
DOM3 级事件就是在 DOM2 级事件基础上增加了更多的事件类型：

* UI 事件，当用户与页面上的元素交互时触发，如：load、scroll
* 焦点事件，当元素获得或失去焦点时触发，如：blur、focus
* 鼠标事件，当用户通过鼠标在页面执行操作时触发如：dbclick、mouseup
* 滚轮事件，当使用鼠标滚轮或类似设备时触发，如：mousewheel
* 文本事件，当在文档中输入文本时触发，如：textInput
* 键盘事件，当用户通过键盘在页面上执行操作时触发，如：keydown、keypress
* 合成事件，当为IME（输入法编辑器）输入字符时触发，如：compositionstart
* 变动事件，当底层DOM结构发生变化时触发，如：DOMsubtreeModified

## 事件对象
事件对象是事件发生时用来记录相关信息的对象，所以事件对象只有当事件发生时才会产生，并且只能在事件处理函数内部访问，在所有事件处理函数运行结束后，事件对象就被销毁。

一般事件处理函数会回调一个参数 event，代表当前事件对象，event 中有很多常用的方法和属性。

事件对象也存在兼容性问题，在 IE8 及以前本版之中，事件处理函数不会回掉参数 event，这时需要通过全局对象 window.event 来获取。

``` js
btn.ddEventListener('click', func, true);

function func(event) {
  //获取事件对象
  var event = event || window.event;
  
  //获取当前事件触发的div
  var target = event.currentTarget;
}
```

### currentTarget 
此属性返回当前事件所绑定的对象。

### target 
此属性返回当前触发事件的对象，注意 target 是触发事件的对象，是真正的事件源，例：给 div 绑定一个事件，点击带文字的 span 后，target 是 span，而 currentTarget 是 div。

``` html
<div id="ct">
  <span>点我</span>
</div>
```

target 存在兼容性，在 IE 浏览器中用 event.srcElement。

``` js
var target = event.target || event.srcElement;
```

### type
返回当前事件的名称。

### keyCode
获取按下的键盘按键所对应的代码。

``` js
document.onkeydown = function(e) {
  console.log(e.keyCode + "  " + String.fromCharCode(e.keyCode))
}
```

`String.fromCharCode(e.keyCode)` 方法可以获取按键名字，但是只适用于字母键和数字键，其他地方经常错乱。

### eventPhase
返回事件传播的当前阶段。

``` js
//1：捕获阶段；2：正常事件派发；3：冒泡阶段。
event.eventPhase == 1 ? '捕获阶段：' : '冒泡阶段：';
```

### timeStamp
返回事件生成的日期和时间。

### bubbles
返回布尔值，代表事件是否是冒泡事件类型。

### cancelable
返回布尔值，事件是否可取消默认动作。

### preventDefault
阻止默认行为，可以采用此方法阻止链接跳转或者表单提交等。

``` js
event.preventDefault();

//IE 浏览器上没有 preventDefault() 取而代之的是 returnValue
window.event.returnValue = false;
```

### stopPropagation
停止事件进一步往上冒泡，需要防止事件冒泡带来的负面影响的时候就要使用该方法。

``` js
event.stopPropagation();

//IE 下也没有 stopPropagation，需要设置 cancelBubble，值为 true 能阻止事件
event.cancelBubble=true
```

### stopImmediatePropagation 
阻止后续事件，该方法除了阻止事件冒泡外在当前事件被绑定多个处理程序的时候，后续的处理程序也会被阻止。

## 自定义事件
除了系统内置的事件外，我们还可以自定义事件。

``` js
var myEvent = new Event('myEvent');
document.addEventListener('myEvent', log, false);
function log() {
  console.log('hello event');
}
document.dispatchEvent(myEvent);
```

通过创建 Event 对象来创建事件，通过 dispatchEvent 函数派发事件。自定义事件可以绑定到任意 DOM 元素上。

## 事件委托
事件委托就是利用事件冒泡的原理，在需要添加事件的父级上绑定事件，这样子元素发生事件的时候就会触发父级绑定的事件，从而提高性能。

如下：为 ul 添加新的 li，并对 li 标签元素绑定 click 事件，但是发现，后增加的元素没有办法触发 click 事件。

``` html
<button id="btnAdd">添加</button>
<ul id="ulList">
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>
<script>
    var btnAdd = document.getElementById('btnAdd');
    var ulList = document.getElementById('ulList');
    var list = document.getElementsByTagName('li');
    var num = 3;
    btnAdd.onclick = function () {
        num++;
        var li = document.createElement('li');
        li.innerHTML = num;
        ulList.appendChild(li)
    }
    for (i = 0; i < list.length; i++) {
        list[i].onclick = function(){
            alert(this.innerHTML);
        }
    }
</script>
```

这是因为如果事件涉及到更新 HTML 节点或者添加 HTML 节点时，新添加的节点无法绑定事件，更新的节点也是无法绑定事件，表现的行为是无法触发事件。而事件委托就能解决这个问题。

* 首先多次操作 DOM 获取元素，这样势必会降低浏览器处理性能；
* 事件不具有继承性，如果我们动态在页面中添加了一个元素，那么还需要重新走一遍上述程序为其添加监听事件。

``` js
<button id="btnAdd">添加</button>
<ul id="ulList">
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>
<script>
    var btnAdd = document.getElementById('btnAdd');
    var ulList = document.getElementById('ulList');
    var num = 3;

    ulList.onclick = function(event){
        var event = event || window.event;
        var target = event.target || event.srcElement;
        if(target.nodeName.toLowerCase() == 'li'){
            alert(target.innerHTML);
        }
    }

    btnAdd.onclick = function () {
        num++;
        var li = document.createElement('li');
        li.innerHTML = num;
        ulList.appendChild(li);
        doclick();
    }
</script>
```
