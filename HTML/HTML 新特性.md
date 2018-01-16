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
在 HTML 5.2 中新增加一个 `<dialog>` 标签。

``` html
<dialog>  
  <h2>Dialog Title</h2>
  <p>Dialog content and other stuff will go here</p>
</dialog>
```

在默认情况下 dialog 是隐藏了的，除非设置 open 属性。

``` html
<dialog open>
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

