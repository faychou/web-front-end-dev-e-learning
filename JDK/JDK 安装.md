# JDK 安装
JDK 的全称是 Java SE Development Kit，也就是 Java 开发工具箱。

[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

JDK 默认安装成功后，会在系统目录下出现两个文件夹，一个代表 jdk，一个代表 jre。

为了配置 JDK 的系统变量环境，我们需要设置三个系统变量，分别是 JAVA_HOME ，Path 和 CLASSPATH 。下面是这三个变量的设置方法：

#### JAVA_HOME
先设置这个系统变量名称，变量值为JDK在你电脑上的安装路径：C:\Program Files\Java\jdk1.8.0_131。创建好后则可以利用 `%JAVA_HOME%` 作为 JDK 安装目录的统一引用路径。
 
#### Path
PATH 属性已存在，可直接编辑，在原来变量后追加：`;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin` 。
 
#### CLASSPATH 
设置系统变量名为：CLASSPATH  变量值为：`.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar` 。
注意变量值字符串前面有一个"."表示当前目录，设置 CLASSPATH 的目的，在于告诉 Java 执行环境，在哪些目录下可以找到您所要执行的Java程序所需要的类或者包。