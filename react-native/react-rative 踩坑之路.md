# React Native 踩坑之路
## 环境搭建
### 安装 Python
安装 2.7.x 版本，版本不能高于 3.0.0

### 错误信息：MSBUILD : error MSB3428: 未能加载 Visual C++ 组件“VCBuild.exe”。
1. 安装 .NET Framework 2.0 SDK； 
2. 安装 Microsoft Visual Studio 2005 或 2013 或 2015; 
3. 如果将该组件安装到了 其他位置，请将其位置添加到系统 路径中。

### 执行安装命令：react-native run-android 出现错误：
```
problem occurred configuring project ‘:app’. 
Could not resolve all dependencies for configuration ‘:app:_debugCompile’. 
Could not find com.android.support:appcompat-v7:23.1.1.
```

解决方案：安装Android Support Repository与Android Support Library

### react-native 运行环境一定要装c++的环境，不一定需要装vs