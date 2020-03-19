# HTML5+ APP开发手册
## MUI框架

## HTML5+ API
### 调用HTML5+ API
``` js
// 扩展API是否准备好，如果没有则监听“plusready"事件
if(window.plus){
  plusReady();
}else{ 
  document.addEventListener( "plusready", plusReady, false );
}

// 扩展API准备完成后要执行的操作
function plusReady(){
  var ws = plus.webview.currentWebview(); //pw回车可输出plus.webview
  // ... code
}
```
### 页面跳转
当浏览器加载一个新页面时，若页面DOM尚未渲染完毕，页面会先显示空白，然后等DOM渲染完毕后，再显示具体内容，这是WEB浏览器技术无法逾越的体验障碍；为解决这个问题，建议使用mui.openWindow方法打开一个新的webview，mui会自动监听新页面的loaded事件，若加载完毕，再自动显示新页面。

mui.openWindow

### 跨域
通过 plus.net 对象避开跨域

## 常见错误
### Uncaught ReferenceError: plus is not defined
在app开发中，若要使用HTML5+扩展api，必须等plusready事件发生后才能正常使用，否则可能会报“plus is not defined”的错误；

mui为简化开发，将plusReady事件封装成了mui.plusReady()方法，凡涉及到HTML5+的api，建议都写在mui.plusReady方法中；