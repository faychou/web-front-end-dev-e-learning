# wxss
## 样式导入
使用 `@import` 语句可以导入外联样式表，`@import` 后跟需要导入的外联样式表的相对路径，用 `;` 表示语句结束。

``` css
@import "common.wxss";
view {
  padding: 10px;
}
```

## 内联样式
框架组件上支持使用 style、class 属性来控制组件的样式。而静态的样式统一写到 class 中，style 接收动态的样式，在运行时会进行解析，请尽量避免将静态的样式写进 style 中，以免影响渲染速度。

``` html
<view style="color:{{color}};" />
```

## 选择器
目前支持的选择器有：

|    选择器   |    样例    |           样例描述           |
|:-----------:|:--------:|:--------------------------:|
|.class|.intro|选择所有 class="intro" 的组件|
|#id|#firstname|选择拥有 id="firstname" 的组件|
|element|view	|选择所有 view 组件|
|element, element|view, checkbox|选择所有文档的 view 组件和所有的 checkbox 组件|
|::after|view::after|在 view 组件后边插入内容|
|::before|view::before|在 view 组件前边插入内容|

## 单位
### rpx 
rpx 单位是微信小程序中新的尺寸单位，可以根据屏幕宽度进行自适应。规定屏幕宽度为 750rpx。

如在 iPhone6 上，屏幕宽度为 375px，共有 750 个物理像素，则：

```
750rpx = 375px = 750 物理像素，1rpx = 0.5px
```

示例：在 iPhone6 设计稿上，一个元素距离顶部为 50px，则 css 代码中设置值的大小就是 50rpx。

### rem
微信小程序也支持 rem 尺寸单位，规定屏幕宽度为 20rem。

```
1rem = (750 / 20)rpx = 37.5rpx;
```

> 注意：开发微信小程序时设计师可以用 iPhone6 作为视觉稿的标准，这样 ps 上设计的多少 px 你就定义多少 rpx。
 
## 资源引入
本地资源无法通过 WXSS 获取，background-image 可以使用网络图片，或者 base64，或者使用 `<image/>` 标签。

## 全局样式与局部样式
定义在 app.wxss 中的样式为全局样式，作用于每一个页面。在 page 的 wxss 文件中定义的样式为局部样式，只作用在对应的页面，并会覆盖 app.wxss 中相同的选择器。


