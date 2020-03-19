# vertical-align
vertical-align用于对齐行内元素。所谓行内元素，即display属性值为下列之一的元素：

* inline

* inline-block

* inline-table

### vertical-align 的值
* baseline：元素基线与行盒子基线重合。

* sub：元素基线移动至行盒子基线下方。

* super：元素基线移动至行盒子基线上方。

* <百分比值>：元素基线相对于行盒子基线向上或向下移动，移动距离等于 line-height 的百分比。

* <长度值>：元素基线相对于行盒子基线向上或向下移动指定的距离。

* middle：元素上、下边的中点与行盒子基线加上x-height的一半对齐。

* text-top：元素的顶边与行盒子的文本盒子的顶边对齐。

* text-bottom：元素的底边与行盒子的文本盒子的底边对齐。

* top：元素的顶边与行盒子的顶边对齐。

* bottom：元素的底边与行盒子的底边对齐。

### 案例
#### 1、居中图标与文字
一个图标与一行文本垂直居中：

``` html
<style type="text/css">
.icon { 
  display: inline-block;
}

.middle { 
  vertical-align: middle; 
}
</style>

<span class="icon middle"></span>
<span class="middle">文字</span>
```
