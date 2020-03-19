MySQL 是一个真正的多用户、多线程 SQL 数据库服务器，是一个为关系型数据库。

MySQL 具有运行速度很快、容易使用、支持查询语言、多个用户可同时连接客户机和服务器、可以运行在各种版本的 UNIX 以及非 UNIX 的系统上等优点。

# 环境搭建
## Linux 系统配置
### centos
第一步、由于 centos 的 yum 源中没有 mysql ，需要到 mysql 的官网下载 yum repo 配置文件：

``` bash
wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

第二步、安装 yum repo 文件：

``` bash
yum -y install mysql57-community-release-el7-11.noarch.rpm
```

执行完后会在 `/etc/yum.repos.d/` 目录下生成 `mysql-community.repo` 和 `mysql-community-source.repo` 两个文件。

查看一下安装效果：

``` bash
yum repolist enabled | grep mysql.*
```

第三步、更新 yum 缓存（可以省略）：

``` bash
yum clear all
yum makecache
```

第四步、安装 mysql：

``` bash
yum install mysql-community-server
```

第五步、启动 mysql：

``` bash
systemctl start mysqld.service
# service mysqld start # centos7 之前
```

运行一下命令查看一下运行状态： 

``` bash
systemctl status mysqld.service
```

第六步、查看初始密码：

``` bash
grep "password" /var/log/mysqld.log
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
ALTER USER 'root'@'localhost' IDENTIFIED BY '********';
```

> 注意：mysql 默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。否则会提示 ERROR 1819 (HY000): Your password does not satisfy the current policy requirements 错误。

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

6、(没有设置第4、5步的)以管理员身份运行 cmd ，输入命令并回车： 

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
#### 方法一
打开终端, 直接执行以下命令: 

``` bash
mysqladmin -u root -p password 新密码
```

执行后提示输入旧密码完成密码修改, 当旧密码为空时直接按回车键确认即可。

#### 方法二
``` bash
SET PASSWORD FOR "username"=PASSWORD("new password");
```

#### 方法三
``` bash
use mysql; # 使用命令切换到mysql数据库
UPDATE mysql.user SET authentication_string=PASSWORD("new password") WHERE user="username";

FLUSH PRIVILEGES; #刷新权限
# 然后退出，重新登录
```

## 重置密码
### window 下重置密码
第一步：切换 MySQL 安装的 bin 目录：

``` bash
cd C:\Program Files\MySQL\bin
```

第二步：停止 MySQL 服务：

``` bash
net stop mysql
```

第三步：以安全模式启动 MySQL：

``` bash
mysqld --skip-grant-tables
```

第四步：新打开一个 cmd 窗口，登陆 MySQL 服务：

``` bash
mysql -u root -p
```

提示输入密码时直接回车即可。

第五步：修改密码：

``` bash
# 切换数据库
use mysql;

# 修改密码
update user set Password=password('123456') where User='root';

# 刷新
flush privileges;
```

第六步：启动 MySQL：

``` bash
net startysql
```

第七步：使用新密码登录。

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

第六步：刷新权限表

``` bash
FLUSH PRIVILEGES;
```

第七步：退出 mysql 后，重启服务即可。

## 添加远程登录用户
mysql 默认只允许 root 帐户在本地登录，如果要在其它机器上连接 mysql，必须修改 root 允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，我添加一个新的帐户：

``` sql
GRANT ALL PRIVILEGES ON *.* TO 'user'@'%' IDENTIFIED BY 'User_123456' WITH GRANT OPTION;
```

输入后使修改生效还需要下面的语句：

``` sql
# 刷新
FLUSH PRIVILEGES;

# 退出 mysql
quit

# 重启
systemctl restart mysqld.service
# service mysqld restart
```

接着还需要在阿里云 ECS 配置安全组规则，放开 22，3306 端口。[详情](https://help.aliyun.com/document_detail/25471.html?spm=5176.100241.0.0.IneJPl)

最后，使用本地 mysql 客户端（navicat，sequel pro）通过 SSH 方式连接数据库，注意这时需要同时填写 mysql 设置的账号 和 阿里云服务器的登陆账号，mysql 的连接地址直接填 127.0.0.1 ，阿里云的连接地址是公网 ip。

## 配置默认编码为 utf8
修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置，如下所示：

```
[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
```

重新启动 mysql 服务。

## 设置自动启动
``` bash
systemctl enable mysqld

systemctl daemon-reload
```

## 卸载 MySQL
1. 查看服务中有没有 mysql,如果有将该服务停掉。

2. 在控制面板中运行添加删除程序，将 mysql 卸载。

3. 删除MySQL安装目录. 

4. 删除目录 C:\Documents and Settings\All Users\Application Data\MySQL

5. 检查C:\WINDOWS目录下是否有my.ini文件,将其删除 

6. 卸载后打开注册表（在开始---运行,输入 regedit），

查看 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services 下的键值，如果有相关 mysql 键值(mysql、mysqladmin)则删掉，即可。

7. 重启，将 mysql 的安装残余目录删掉 (查看服务，此时服务中已没有 mysql)。

8. 重新安装 mysql.安装后先不要运行 Server Instance Configuration wizard，重启后在开始菜单中运行该向导，即可。