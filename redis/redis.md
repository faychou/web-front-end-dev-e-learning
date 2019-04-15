# redis
Redis是使用c语言开发的一个高性能键值数据库。Redis可以通过一些键值类型来存储数据。 键值类型： String字符类型 map散列类型 list列表类型 set集合类型 sortedset有序集合类型。

## 环境搭建
[官网地址下载地址](http://redis.io/)

安装：

``` bash
#sftp 上传安装包到linux
#解压
tar -zxvf redis.3.0.0.tar.gz
#安装c语言环境
sudo apt-get install gcc-c++
#编译
cd redis-3.0.0
make
#安装
make install PREFIX = /usr/local/redis
# 查看
cd /usr/local/redis
ls

```