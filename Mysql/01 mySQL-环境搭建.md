MySQL 是一个真正的多用户、多线程 SQL 数据库服务器，是一个为关系型数据库。

MySQL 具有运行速度很快、容易使用、支持查询语言、多个用户可同时连接客户机和服务器、可以运行在各种版本的 UNIX 以及非 UNIX 的系统上等优点。

# 环境搭建
## Linux 系统配置
### centos
第一步、由于 centos 的 yum 源中没有 mysql ，需要到 mysql 的官网下载 yum repo 配置文件：

``` bash
wget http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

第二步、安装 yum repo 文件：

``` bash
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
```

执行完后会在 `/etc/yum.repos.d/` 目录下生成 `mysql-community.repo` 和 `mysql-community-source.repo` 两个文件。

第三步、更新 yum 缓存：

``` bash
yum clear all
yum makecache
```

第四步、安装 mysql：

``` bash
yum install mysql-community-client.x86_64 mysql-community-common.x86_64 mysql-community-devel.x86_64 mysql-community-libs.x86_64 mysql-community-server.x86_64

# 或者
rpm install mysql-server
```

第五步、启动 mysql：

``` bash
service mysqld start
```

第六步、查看初始密码：

``` bash
grep 'temporary password' /var/log/mysqld.log
```

得到类似以下内容：

```
a temporary password is generated for root@localhost:50azqgpiat
```

最后面的 `50azqgpiat` 就是初始密码。

第七步、使用初始密码登陆：

``` bash
mysql -u root -p
```

第八步、更改初始密码：

``` bash
alter user 'root'@'localhost' identifide by '新密码'
```

## Windows 系统
1、 直接官网下载 mysql.zip [下载地址](https://dev.mysql.com/downloads/mysql/)，注意 MySQL Community Server 表示的是个人免费版，其他版本都是要收费的；

2、 将下载的 mysql.zip 解压到相应的位置, 如: C:\Program Files;

3、 在安装目录（如: C:\Program Files\mysql-8.0.11）下找到 my-default.ini 配置文件, 复制该文件并将其重命名为 my.ini , 打开并编辑 my.ini, 如果不存在 my-default.ini 文件则直接创建 my.ini 并编辑以下代码即可；

``` bash
[mysql]
# 设置 mysql 客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port=3306

# 设置mysql的安装目录
basedir=C:\Program Files\mysql-8.0.11

# 设置 mysql 数据库的数据的存放目录
datadir=C:\Program Files\mysql-8.0.11\data

# 允许最大连接数
max_connections=200

# 允许连接失败的次数，为了避免有人从该主机试图攻击数据库系统
max_connect_errors=10

# 服务端使用的字符集默认为 utf8 
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

4、 打开 我的电脑 -> 属性 -> 高级 ->  环境变量, 在系统变量里选择 Path ,新建变量名 `MYSQL_HOME` , 变量值为 `MySQL` 安装目录路径, 如： `C:\Program Files\mysql-8.0.11`；

5、 在 环境变量 的 Path 变量中添加 `;%MYSQL_HOME%\bin;`；

6、以管理员身份运行 cmd ，输入命令并回车： 

```
cd C:\Program Files\mysql-8.0.11\bin
```

7、然后输入以下命令初始化 data 目录：

``` bash
mysqld --initialize-insecure
```

8、 然后执行以下命令安装 MySQL 服务: 

``` bash
# mysqld -install 服务名 --defaults-file="my.ini"
mysqld -install

# 后面的服务名字可以不写，默认为 mysql
``` 

提示 "Service successfully installed." 则表示安装成功。

### MySQL服务的启动、停止与卸载
``` bash
# 安装完毕后，执行以下命令启动 MySQL 服务
net start MySQL

# 停止
net stop MySQL

# 删除服务，MySQL 为服务的名字
sc delete MySQL
```

## MacOS 系统
官网下载 mysql 的 dmg 版本傻瓜式安装即可。安装完成后在系统偏好设置的其他下，点击 ‘mysql’ 的图标，然后点击 start mysql server 按钮，启动 mysql 。

## 登录 MySQL
当 MySQL 服务已经运行时, 此时 mysql 还没有密码，我们可以通过以下命令设置新密码：

``` bash
mysqladmin -u root password 新密码
```

这样就可以登录到 MySQL 数据库中了:

``` bash
mysql -h 主机名 -u 用户名 -p
```

* -h : 该命令用于指定客户端所要登录的 MySQL 主机名, 登录当前机器该参数可以省略;
* -u : 所要登录的用户名;
* -p : 告诉服务器将会使用一个密码来登录, 如果所要登录的用户名密码为空, 可以忽略此选项。

然后，会出现输入密码的提示，若设置了密码, 输入密码并回车登录, 若之前没有设置密码则直接按回车登录。登录成功后你将会看到 Welecome to the MySQL monitor... 的提示语。

最后，输入 exit 或 quit 退出登录。

如果遗忘了密码，可以直接编辑 my.ini 文件来跳过密码认证过程，步骤如下：

  1、在 [mysqld]字段下添加一行 `skip-grant-tables`，然后保存退出即可；
  
  2、接着重启 MySQL 即可。
  
### 修改用户密码
打开终端, 直接执行以下命令: 

``` bash
mysqladmin -u root -p password 新密码
```

执行后提示输入旧密码完成密码修改, 当旧密码为空时直接按回车键确认即可。

## 重置密码

### mac 下重置密码
比较靠谱的重置密码方法：

第一步：在系统偏好里关闭 MySQL ，或者使用以下命令关闭；

``` bash
sudo /usr/local/mysql/support-files/mysql.server stop
```

第二步：进入以下目录：

``` bash
cd/usr/local/mysql/bin
```

第三步：获取权限：

``` bash
sudo su
```

第四步：重启服务：

``` bash
./mysqld_safe --skip-grant-tables &
```

第五步：重新打开一个终端，配置短命令：

``` bash
alias mysql=/usr/local/mysql/bin/mysql
```

第六步：进入 mysql 命令模式：

``` bash
mysql
```

第七步：进入 mysql 数据库：

``` bash
use mysql
```

第八步：获取权限，否则无法修改：

``` bash
flush privileges;
```

第九步：设置新密码：

``` bash
set password for 'root'@'localhost'=password('新密码');
```

#### 方法二：
第一步：在系统偏好中,中止 MYSQL 服务；

第二步：

``` bash
cd/usr/local/mysql/bin
```

第三步：

``` bash
sudo ./mysqld_safe--skip-grant-tables &
```

第四步：登录 MySQL：

``` bash
mysql
```

第五步；置空 root 用户的密码：

``` bash
update mysql.user set password='' where user='root';

# 或者，密码字段名 5.7 版本的是 authentication_string，之前的为 password
update mysql.user set authentication_string=password('123456') where user='root';
```

第六步：

``` bash
flush privileges; 
```

第七步：退出 mysql 后，重启服务即可。
