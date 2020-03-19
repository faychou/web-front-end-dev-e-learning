# HTML 新特性
## Sizes for Apple Icons
可以直接在头部定义不同尺寸的网站 icon ，而在 HTML 5.2 之前，我们只能使用 appple-touch-icon 。

``` html
<link rel="icon" sizes="16x16" href="path/to/icon16.png">  
<link rel="icon" sizes="32x32" href="path/to/icon32.png">
```

## Styles in the body
在 HTML 5.2 中，可以在 `<body>` 中的任意位置嵌入 `<style>` 。

``` html
<body>  
  <p>I’m cornflowerblue!</p>
  <style>
    p { color: cornflowerblue; }
  </style>
  <p>I’m cornflowerblue!</p>
</body> 
```

## legend
在 HTML 5.2 之前，在 `<legend>` 中只能嵌套文本，而现在可以嵌套块级元素了。

``` html
<fieldset>  
  <legend><h2>Basic Information</h2></legend>
</fieldset>  
<fieldset>  
  <legend><h2>Contact Information</h2></legend>
</fieldset>
```

## dialog
在 HTML 5.2 中新增加一个 `<dialog>` 标签，来帮助我们快速实现一个对话框。

``` html
<dialog>  
  <h2>Dialog Title</h2>
  <p>Dialog content and other stuff will go here</p>
</dialog>
```

在默认情况下 dialog 是隐藏了的，除非设置 open 属性。

``` html
<dialog open>
  <h2>Dialog Title</h2>
  <p>Dialog content and other stuff will go here</p>
</dialog>
```

使用 js 中的 show() 和 close() 方法，可以显示与隐藏 dialog ：

``` html
<button id="open">Open Dialog</button>  
<button id="close">Close Dialog</button>

<dialog id="dialog">  
  <h2>Dialog Title</h2>
  <p>Dialog content and other stuff will go here</p>
</dialog>

<script>  
const dialog = document.getElementById("dialog");

document.getElementById("open").addEventListener("click", () => {  
  dialog.show();
});

document.getElementById("close").addEventListener("click", () => {  
  dialog.close();
});
</script> 
```

当你想要在显示对话框的同时，有一个遮罩层的话。可以使用 `showModal()` 这个方法。另外要注意的是 `open()` 方法和 `showModal()` 不能同时使用，否则会报：Untitled-1.html:28 Uncaught DOMException: Failed to execute 'showModal' on 'HTMLDialogElement': The element already has an 'open' attribute, and therefore cannot be opened modally.这个错误。

## 支付请求API --- allowpaymentrequest
在 HTML5.2 之前，对于支付请求的 API 是不能在 iframe 中来完成的。所以每次在进行移动支付时都需要跳转到另外一个支付页面才能完成付款。而现在，在 iframe 上使用 allowpaymentrequest 属性就可以让 iframe 使用 Payment Request API 。从而让嵌入了第三方内容的页面能够控制该第三方内容是否可向用户请求获取支付凭证，进而让可嵌入的购物车工具可以利用 Payment Request API。

``` html
<iframe allowpaymentrequest>
```

## 使用多个main标签
在 HTML5.2 之前，一个页面中只允许出现一个 `<main>` 标签，因为他是代表一个应用的主体部分，应该独一无二。但是随着单页应用的普及，这一特性不再适用，所以可以允许一个页面出现多个 `<main>` 标签。但是在同一时间内，只有一个 `<main>` 标签是显示的，其他所有都是被 hidden 属性隐藏。

``` html
<main>...</main>
<main hidden>...</main>
<main hidden>...</main>
```

> 注意：隐藏 `<main>` 标签的方法必须是使用 hidden 属性才行。其它的如:display: none、透明的为 0 等方法不行，这会让 `<main>` 标签失去自己的语义和作用。

