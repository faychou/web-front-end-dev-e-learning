# redis
Redis 是使用 c 语言开发的在内存中进行数据结构存储的系统，可以用作数据库、缓存和消息中间件，是 `key-value` 的形式来进行数据的读写。 键值类型： `String` 字符类型 `map` 散列类型 `list` 列表类型 `set` 集合类型 `sortedset` 有序集合类型。简单理解就是，我们可以利用 Redis 加缓存减轻 db 的压力，提升用户读写的速度。

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