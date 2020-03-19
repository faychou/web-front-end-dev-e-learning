# Ionic
## 环境搭建
### window 系统搭建 Android
Ionic 2 应用使用 Ionic CLI (Ionic command line) 创建，使用 Cordova 打包成本地应用。在安装 Ionic CLI 之前，需要先安装 Node.js。

#### 第一步：安装 node.js ；

#### 第二步：安装 JDK ;
为了编译到 Android平台，还需要安装JDK。
[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

> 注意：设置系统变量。

#### 第三步：安装 android SDK ；

#### 第四步：安装ionic cordova ：

``` bash
npm install -g cordova ionic

# 查看版本号
ionic -version
cordova -version
```

> 命令前面可能需要添加sudo提权来进行全局安装。

#### 第五步：创建项目 ：

``` bash
ionic start myApp tabs

cd myApp
ionic serve
```

* ionic start myApp blank（空项目） 
* ionic start myApp tabs（带导航条） 
* ionic start myApp sidemenu（带侧滑菜单）

#### 第六步：编译项目：

``` bash
# 添加android平台
ionic platform add android

# 移除android平台
ionic platform remove android

# 编译项目apk
ionic build android

# 在android模拟器或真机中模拟
ionic emulate android

# 以上两步可以合并为
ionic run android
```

### MAC 系统 ios
``` bash
sudo npm install -g cordova ionic   
sudo npm install -g ios-sim  
ionic platform add ios  # 添加ios平台
ionic build ios # 构建ios项目  
ionic emulate ios   # 模拟器运行  
ionic run ios   # 连接真机后直接运行
```