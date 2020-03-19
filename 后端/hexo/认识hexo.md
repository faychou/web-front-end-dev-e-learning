Hexo 是一款基于 Node.js 的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在 GitHub 和 Coding 上，是搭建博客的首选框架。

## 一、环境安静

使用 Hexo 必须保证电脑中已经安装了 Git、Node.js(6.9以上)。

然后开始安装 Hexo：

``` bash
npm install -g hexo-cli
```

初始化博客项目：

``` bash
$ hexo init <项目名> #（最新版本这一步会直接安装依赖）
$ cd <项目名> # 进入博客目录
$ npm install # 安装依赖
```

创建完毕后，文件夹说明：

```bash
|-- my-blog # 项目跟目录名
    |-- node_modules # nodejs 的依赖包文件夹
    |-- public # 存放生成页面的文件夹
    |-- scaffolds # 模板文件夹，生成文章的一些模板
    |   |-- draft.md
    |   |-- page.md
    |   |-- post.md # 这里是博文模板
    |-- source # 资源文件夹，存放用户资源
    |   |-- _posts # 博文目录
    |       |-- hello-world.md # 博文
    |-- themes # 主题文件夹，Hexo 会根据主题来生成静态页面
        |-- landscape. # 默认主题
    |-- .gitignore # git 忽略规则的配置文件
    |-- package-lock.json
    |-- package.json # 应用程序的信息
    |-- _config.yml # 网站的配置信息
            ...
```

##### 配置：

注意：配置值与配置名需要隔一个空格，否则会编译报错



这里使用`---`分割的区域叫做“Front-matter”，用于指定这篇博文的变量。可手动修改`Front-matter`：

```css
---
layout:
title: my first blog
date: 2019-05-11 16:20:56
updated:
comments:
tags:
- introduction
- hexo
categories:
- Diary
---
```

推送之前，在库设置（Repository Settings）中将默认分支设置为_config.yml配置中的分支名称。稍等片刻，站点就会显示Github Pages中

可设置两个分支（根据自己的习惯自行配置）：

- master：存放源代码
- public：存放编译部署后的站点文件