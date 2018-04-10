# BOM
BOM 是指浏览器对象模型，提供了很多对象，用于访问浏览器的功能，这些功能与任何网页内容无关。主要描述了与浏览器进行交互的方法和接口。

## url
window.url 对象包含整个页面的地址，可以简写为 url。

### hash
url 中的哈希值，也就是路径中的 `#` 之后的内容。

#### hashchange 事件
window 对象中的一个事件，以下情况都会触发该事件：

* 直接更改浏览器地址，在最后面增加或改变 `#hash`；
* 通过改变 `location.href` 或 `location.hash` 的值；
* 通过触发点击带锚点的链接；
* 浏览器前进后退可能导致 hash 的变化，前提是两个网页地址中的 hash 值不同。

通过该事件可以实现前端基于 hash 的 SPA 路由：

``` html
<a class="api a">a.html</a>
<a class="api b">b.html</a>
```

``` js
// 注册路由
document.querySelectorAll('.api').forEach(item => {
  item.addEventListener('click', e => {
    e.preventDefault();
    let link = item.textContent;
    location.hash = link;
  }, false)
});

// 监听路由
window.addEventListener('hashchange', e => {
  console.log({
    location: location.href,
    hash: location.hash
  })
}, false)
```

## history
window.history 对象包含浏览器的历史记录，在编写时可省略 window 这个前缀。history 对象是实现 SPA 前端路由的主流方法。

### history.back()
与在浏览器点击后退按钮相同。

### history.forward()
与在浏览器中点击按钮向前相同。

### history.go(n)
接受一个整数作为参数，表示移动到指定的页面，比如 go(1) 表示前进一页，相当于 forward() ，go(-1) 表示后退一页，相当于 back()，go(0) 相当于刷新当前页面。
如果移动的位置超出了访问历史的边界，该方法并不报错，而是静默失败。

### pushState(stateObj, title, url)
HTML5 新增方法，可以用来向历史栈中添加数据，现在的前端路由也是基于这个原理实现的。

* stateObj ：一个与指定网址相关的状态对象，popstate 事件触发时，该对象会传入回调函数。如果不需要这个对象，此处可以填 null。

* title：新页面的标题，但是所有浏览器目前都忽略这个值，因此这里可以填 null。

* url：新的网址(相对路径)，必须与当前页面处在同一个域，浏览器的地址栏将显示这个网址。

> 注意：

> pushState 方法不会触发页面刷新，只是导致 history 对象发生变化，地址栏会有反应,只有当触发前进后退等事件 back() 和 forward() 等，浏览器才会刷新。

> 这里的 url 是受到同源策略限制的，防止恶意脚本模仿其他网站 url 用来欺骗用户，所以当违背同源策略时将会报错

### replaceState(stateObj, title, url)
HTML5 新增方法，和 pushState 的区别就在于它不是写入而是替换修改浏览历史中当前纪录，其余和 pushState 一模一样。

### popstate 事件
每当同一个文档的浏览历史（即 history 对象）出现变化时，就会触发 popstate 事件。

使用方法：可以为 popstate 事件指定回调函数。这个回调函数的参数是一个 event 事件对象，它的 state 属性指向 pushState 和 replaceState 方法为当前 URL 所提供的状态对象（即这两个方法的第一个参数）。

> 注意：仅仅调用 pushState 方法或 replaceState 方法 ，并不会触发该事件，只有用户点击浏览器倒退按钮和前进按钮，或者调用 back()、forward()、go() 时才会触发。另外，该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。

### 案例：history 实现 spa 前端路由
``` html
<a class="api a">a.html</a>
<a class="api b">b.html</a>
```

``` js
// 注册路由
document.querySelectorAll('.api').forEach(item => {
  item.addEventListener('click', e => {
    e.preventDefault();
    let link = item.textContent;
    if (!!(window.history && history.pushState)) {
      // 支持History API
      window.history.pushState({name: 'api'}, link, link);
    } else {
      // 不支持,可使用一些 Polyfill 库来实现
    }
  }, false)
});

// 监听路由
window.addEventListener('popstate', e => {
  console.log({
    location: location.href,
    state: e.state
  })
}, false)
```
