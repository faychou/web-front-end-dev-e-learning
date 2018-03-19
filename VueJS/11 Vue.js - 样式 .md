# Vue 样式
操作元素的 class 列表和它的内联样式可以用 `v-bind` 处理。`v-bind:class` 指令可以与普通的 class 属性共存。

``` html
<div v-bind:class="{ active: isActive }"></div>

<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>

<!-- 动态地切换 class：-->
<div v-bind:class="[isActive ? activeClass : '', errorClass]">

<!-- style 就是一个对象。 -->
<!-- CSS 属性名可以用驼峰式（camelCase）或短横分隔命名 -->
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

<!-- 将多个样式对象应用到一个元素上 -->
<div v-bind:style="[baseStyles, overridingStyles]">
<div :class="[commonClass,item.active ? activeClass : '']">
```

## CSS 模块化功能
实现 css 的模块化，避免全局冲突，在单文件组件上，给 style 标签添加 scoped 即可：

``` html
<style lang="scss" scoped></style>
```
 
这样打包后每个类或者 id 乃至标签都会给自动在 css 后面添加 hash !


