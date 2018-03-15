# Float属性
float元素会脱离正常文档流，然后向左或向右平移，一直平移到碰到容器边框或者另一个float元素。
浮动元素会根据上一个元素的类型判断位置，如果上一个是float元素，则跟随他浮动，放置不下就挤到下一行展示
如果上一个是标准流元素，则浮动元素的相对垂直高度不变，顶部和上一个底部对齐
清除浮动
父元素添加 overflow:hidden，会隐藏子元素超出容器部分，且IE6不支持
浮动元素后面添加clear:both，会添加额外的无意义的标签
父元素变成float元素
使用伪类:after，代码如下：

``` css
.wrap:after {
    content:' ',
    display:block;
    height:0;
    clear:both;
}
.wrap {
    zoom:1;
}
```

content是在父容器的后面添加一个空白字符，height:0是让这个空白字符不显示出来，display:block;clear:both是确保这个空白字符是非浮动的独立区块。zoom:1是IE6独有的属性，作用是激活父元素的hasLayout属性，让父元素拥有自己的布局，其他浏览器会直接忽略该属性。