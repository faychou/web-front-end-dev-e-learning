# WEEX开发入门
## 开发环境
### 快速上手
1、 打开 [dotWe](http://dotwe.org/vue) ，以`.vue`文件的格式：`<template>`, `<style>`, `<script>` 编写应用，然后点击`run`，即可预览；

2、 手机上下载安装 [Weex Playground](https://weex.apache.org/cn/playground.html) App ，然后用 Playground 扫码即可在手机端预览。

### weex-toolkit
Weex 官方提供了 weex-toolkit 的脚手架工具来辅助开发和调试。

``` bash
# 安装
npm install -g weex-toolkit

# 检查版本
weex -v

# 项目初始化
weex init project-name

# 安装项目依赖
npm install

# 开启watch 模式
npm run dev

# 静态服务器
npm run serve
```

然后我们打开浏览器，进入 `http://localhost:8080` 即可看到 weex h5 页面。

## 基本语法
通过 <foo append="tree|node"> 的方式定义页面首次渲染时的渲染颗粒度，这让开发者有机会根据界面的复杂度和业务需求对首次渲染过程进行定制。append="tree" 表示整个结点包括其所有子结点全部生成完毕之后，才会一次性渲染到界面上；而 append="node" 则表示该结点会先渲染在界面上作为一个容器，其子结点会稍后做进一步渲染。

Weex的布局单位有且只有px，默认的显示宽度 (viewport) 是 750px，组件都会以 750px 作为满屏宽度，但可以通过 meta.setViewport() 手动指定页面的显示宽度.

## 组件

## 布局系统

## 生命周期