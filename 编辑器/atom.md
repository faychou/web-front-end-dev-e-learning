# ATOM 使用指南
## 插件安装
### 方法一：
打开Atom，选择File -> Settings，点击 Install，输入插件的名字进行搜索，找到后点击Install
缺点：在Atom设置界面中点击下载插件以后因为网络原因大多数情况都会卡住/报错。

### 方法二：使用apm安装工具
apm(Atom Package Manager)是atom的包管理工具，可以方便的管理Atom的插件。
1、首先打开你的终端（cmd/bash）；

2、切换到atom的插件目录，如下（记得输入完命令后按回车）：
    cd ~/.atom/packages

3、接着输入以下命令，这里的<package-name>需要替换为你要安装的插件名字：
    apm install <package-name>

### 方法三：
1、搜索查找特定的插件

2、进入插件详情，点击Repo

3、在打开的新页面中，找到右上角绿色按钮：clone or download，点击并复制里面的链接

4、打开终端（cmd），依次运行一下命令
```
cd ~/.atom/packages

git clone https://github.com/JoelBesada/activate-power-mode.git

cd activate-power-mode

npm install
```

5、重启ATOM

## 常用插件
### 1、activate-power-mode
Atom装逼插件，装上之后使用快捷键：Ctrl + Alt + o 激活

### 2、atom
### 3、autoprefixer
自动为 CSS 属性添加特定的前缀。

### 4、atom-beautify
美化代码
### 5、atom-html-preview
html文档预览工具， 默认快捷键：CTRL-SHIFT-H

### 6、atom-material-syntax
### 7、atom-material-ui
### 8、pigments
### 9、atom-ternjs
### 10、atomatigit
### 11、atomic-chrome
配合chrome实现

### 12、color-picker
颜色拾取器，在编辑器里面挑选颜色。通过右键选择 Color picker ，或者 cmd + shift + c/ctrl + alt + c 快捷键调出颜色选择面板。

### 13、docblockr
### 14、emmet
### 15、git-plus
### 16、highlight-line
### 17、language-babel
### 18、language-diff
### 19、language-vue-component
### 20、minimap
### 21、open-in-browser
### 22、open-in-browsers
### 23、run-in-browser
### 24、open-recent
### 25、react-es6-snippets
### 26、seti-ui
### 27、 file-icons
显示文件类型对应的图标