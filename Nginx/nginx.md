# nginx
nginx ("engine x") 是一个高性能的 HTTP 和反向代理服务器，也是一个 IMAP/POP3/SMTP 服务器。采用事件驱动的异步非阻塞处理方式框架，这让其具有极好的 IO 性能，时常用于服务端的反向代理和负载均衡。Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的 Rambler.ru 站点开发的，第一个公开版本 0.1.0 发布于 2004年10月4日。

擅长于底层服务器端资源的处理（静态资源处理转发、反向代理，负载均衡等）。

## 配置
nginx 的配置都写在 nginx.conf 的文件里,从 nginx 的配置指令作用域来讲，分为 5 个作用域块：

* 全局作用域块
* event 作用域块
* http 指令作用域块
* server 指令作用域块
* location 指令作用域块

### 基本命令
运行可执行文件就可以开启 nginx：

``` bash
nginx -c /usr/local/nginx/conf/nginx.conf
```

如果，nginx 已经开启，那么它就可以通过使用 -s 参数的可执行命令控制:

``` bash
nginx -s [signal]
```

signal 可以为下列命令之一：

* stop — 直接关闭 nginx
* quit — 会在处理完当前正在的请求后退出，也叫优雅关闭
* reload — 重新加载配置文件，相当于重启
* reopen — 重新打开日志文件

### centOS 系统安装
nginx 依赖以下模块:

* gzip 模块需要 zlib 库 及其开发环境
* rewrite 模块需要 pcre 库及开发环境
* ssl 功能需要 openssl 库及开发环境以及 yum install -y gcc-c++ 环境

#### 源码安装：
1、使用以下几个命令安装必备的几个库：

``` bash
# 所有操作的基础路径在 /usr/local/src ，请先进入该目录
yum -y install make zlib zlib-devel gcc gcc-c++ libtool openssl openssl-devel

# 紧接着安装 PCRE，这家伙提供了 Rewrite 功能：
## 先下载二进制包
wget http://downloads.sourceforge.net/project/pcre/pcre/8.41/pcre-8.41.tar.gz
## 解压
tar zxvf pcre-8.41.tar.gz
## 进入安装目录编译安装
cd pcre-8.41
./configure
make && make install
```

2、之后安装 nginx ：

``` bash
## 先返回上一级目录
cd ..
## 下载二进制包
wget http://nginx.org/download/nginx-1.10.3.tar.gz
## 解压
tar -zxvf nginx-1.10.3.tar.gz
## 进入安装目录编译安装
cd nginx-1.10.3

# ./configure 是用来检查本机的的安装环境
./configure --prefix=/usr/local/nginx --with-http_stub_status_module  --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.41

# 编译安装
make && make install

# 安装成功之后可以执行 /usr/local/nginx/sbin/nginx -v查看版本。

## 配置文件检查
/usr/local/nginx/sbin/nginx -t

## 上面检查没问题之后启动
/usr/local/nginx/sbin/nginx

## 参数说明：
#--prefix=PATH ： 指定 nginx 的安装目录，默认 /usr/local/nginx。

#--conf-path=PATH ： 设置 nginx.conf 配置文件的路径。nginx 允许使用不同的配置文件启动，通过命令行中的-c选项。默认为 conf/nginx.conf。

#--user=name ： 设置 nginx 工作进程的用户。安装完成后，可以随时在 nginx.conf 配置文件更改 user 指令。默认的用户名是 nobody。

#--group=name 类似 --user=name 。

#--with-pcre=PATH，如果是从 PCRE 网站下载 pcre 库的源码并解压，需要指定 pcre 的源码路径。而如果是通过 yum 方式安装，直接使用 --with-pcre 自动找到库文件。

#--with-zlib=PATH ： 指定 zlib（版本1.1.3 - 1.2.5）的源码解压目录。

#--with-http_ssl_module ： 使用https协议模块。默认情况下，该模块没有被构建。前提是openssl与openssl-devel已安装。

#--with-http_stub_status_module ： 用来监控 Nginx 的当前状态。

#--with-http_realip_module ： 通过这个模块允许我们改变客户端请求头中客户端IP地址值(例如X-Real-IP 或 X-Forwarded-For)，意义在于能够使得后台服务器记录原始客户端的IP地址。

#--add-module=PATH ： 添加第三方外部模块，如nginx-sticky-module-ng或缓存模块。每次添加新的模块都要重新编译（Tengine可以在新加入module时无需重新编译）。
```

之后直接访问 `http://127.0.0.1`。

为了方便在任何目录下运行 nginx 命令，我们需要设置环境变量：

``` bash
vim /etc/profile

# 然后加入以下代码
export NGINX_HOME=/usr/local/nginx
export PATH=$NGINX_HOME/sbin:$PATH

# 保存退出后，执行以下命令使配置文件生效
source /etc/profile
```
接下来就可以直接运行：`nginx`命令了。

##### 设置 nginx 为系统服务：
Nginx安装完成后默认不会注册为系统服务，所以需要手工添加系统服务脚本。在/etc/init.d目录下新建nginx文件，并更改权限其即可。

1、新建文件：

``` bash
vi /etc/init.d/nginx

# 添加以下内容
#!/bin/sh 
# 
# nginx - this script starts and stops the nginx daemon 
# 
# chkconfig:   - 85 15 
# description: Nginx is an HTTP(S) server, HTTP(S) reverse \ 
#               proxy and IMAP/POP3 proxy server 
# processname: nginx 
# config:      /etc/nginx/nginx.conf 
# config:      /etc/sysconfig/nginx 
# pidfile:     /var/run/nginx.pid 

# Source function library. 
. /etc/rc.d/init.d/functions 

# Source networking configuration. 
. /etc/sysconfig/network 

# Check that networking is up. 
[ "$NETWORKING" = "no" ] && exit 0

# 这里要根据实际情况修改
nginx="/usr/local/nginx/sbin/nginx" 
prog=$(basename $nginx) 

# 这里要根据实际情况修改
NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf" 

[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx 

lockfile=/var/lock/subsys/nginx 

start() { 
    [ -x $nginx ] || exit 5 
    [ -f $NGINX_CONF_FILE ] || exit 6 
    echo -n $"Starting $prog: " 
    daemon $nginx -c $NGINX_CONF_FILE 
    retval=$? 
    echo 
    [ $retval -eq 0 ] && touch $lockfile 
    return $retval 
} 

stop() { 
    echo -n $"Stopping $prog: " 
    killproc $prog -QUIT 
    retval=$? 
    echo 
    [ $retval -eq 0 ] && rm -f $lockfile 
    return $retval 
killall -9 nginx 
} 

restart() { 
    configtest || return $? 
    stop 
    sleep 1 
    start 
} 

reload() { 
    configtest || return $? 
    echo -n $"Reloading $prog: " 
    killproc $nginx -HUP 
RETVAL=$? 
    echo 
} 

force_reload() { 
    restart 
} 

configtest() { 
$nginx -t -c $NGINX_CONF_FILE 
} 

rh_status() { 
    status $prog 
} 

rh_status_q() { 
    rh_status >/dev/null 2>&1 
} 

case "$1" in 
    start) 
        rh_status_q && exit 0 
    $1 
        ;; 
    stop) 
        rh_status_q || exit 0 
        $1 
        ;; 
    restart|configtest) 
        $1 
        ;; 
    reload) 
        rh_status_q || exit 7 
        $1 
        ;; 
    force-reload) 
        force_reload 
        ;; 
    status) 
        rh_status 
        ;; 
    condrestart|try-restart) 
        rh_status_q || exit 0 
            ;; 
    *)    
      echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}" 
        exit 2 

esac
```

2、修改其权限并开机启动

``` bash
#修改权限：
chmod 755 /etc/init.d/nginx

#开机启动：
chkconfig nginx on

#查看开机启动的服务：
chkconfig --list
#centos7方式：systemctl list-units --type=service
```

3、使用服务

``` bash
#启动服务：
service nginx start

#停止服务：
service nginx stop

#重启服务：
systemctl reload nginx.service
# service nginx reload
```


#### 方法二、通过 yum 安装：
1、 安装过程中需要管理员权限，及root权限:

``` bash
# su 切换超级用户,sudo 以超级用户权限访问
su root
# 回车后提示输入 password

# 升级所有软件，包括内核，-y 自动填写yes
yum -y update
```
2、添加 Nginx 到 YUM 源：

``` bash
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

3、 使用 yum 命令从 Nginx 源服务器中获取来安装 Nginx：

``` bash
yum install nginx # 或者 yum -y install nginx
# 当需要确认时输入”y“确认
```

4、 测试 nginx 服务：

``` bash
# 测试 nginx
nginx -t

# 启动
service nginx start
# sudo systemctl start nginx.service
# syetemclt就是service和chkconfig这两个命令的整合，在CentOS 7就开始被使用了

#查看nginx是否启动
ps aux|grep nginx

# 启动后直接浏览器访问 http://127.0.0.1，或者以下方式：
wget http://127.0.0.1
```

5、如果使用 centos7 以上，需要做以下配置：

``` bash
#配置服务
vim /usr/lib/systemd/system/nginx.service

#输入以下内容
[Unit]
Description=nginx
After=network.target
  
[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true
  
[Install]
WantedBy=multi-user.target

#完成后运行以下命令：
systemctl daemon-reload
```

6、 配置 nginx.conf

``` bash
# nginx.conf 是 nginx 配置文件，一般放置在 /usr/local/nginx/conf，/etc/nginx，或者 /usr/local/etc/nginx；Windows下路径就是你安装Nginx目录下；Mac系统配置文件路径在: /usr/local/etc/nginx/nginx.conf

cd /etc/nginx

# 列出文件
ls

vim nginx.conf

# 类似以下结构
server {
    location / {
        root /var/www;
    }

    location /images/ {
        root /var/images;
    }
}

#文件修改后，需要重启 nginx 服务
service nginx restart
# systemctl restart nginx.service

#查看服务当前状态
systemctl status nginx.service

#停止 nginx 服务
service nginx stop
# systemctl stop nginx.service
```

7、开机启动 Nginx：

``` bash
sudo systemctl enable nginx.service

#停止开机自启动
systemctl disable nginx.service
```

> yum 安装与源码安装的区别：yum 安装的可能不是最新版本，且文件位置也与源码安装的不同，启动方式也不一样，yum 安装的 nginx 可直接作为系统服务启动，源码安装还需要自己设置环境变量和系统服务。

### Mac 系统安装
``` bash
# 安装nginx
brew install nginx

# 显示版本号
nginx -v

# 进入 nginx
cd /usr/local/etc/nginx

# 启动 nginx
nginx

# 停止
nginx -s stop

# 重启
nginx -s reload #每次修改完.conf文件就需要重启nginx

# 检查配置
nginx -t  #如果出现 ok 和 successfull 就代表正确了
```

### nginx 配置信息
#### 配置说明
``` bash
# 网站文件默认存放目录
/var/www

# 网站默认站点配置
/usr/local/nginx/conf/nginx.conf

# 自定义 Nginx 站点配置文件存放目录
/usr/local/nginx/conf.d/nginx.conf

# Nginx 自定义配置文件启动
nginx -c /usr/local/nginx/conf.d/nginx.conf
```

#### 详细配置文件指南：
nginx 是由模块组成的，这些模块在配置文件中又有指定的指令。 指令被分成简单指令和块指令。简单指令包括名称和用空格分割的参数以及用来结尾的分号(;)。 一个块指令和简单指令有相同的结构，但是它使用大括号({and})来包围一系列说明来替代使用分号作为结尾。

``` bash
### 每个指令必须有分号结束。###

# 表明 Nginx 服务是由哪个用户哪个群组来负责维护进程的，默认是 nobody
# user nobody; 

# 工作的子进程数量（通常等于CPU数量或者2倍于CPU），默认1
worker_processes  2; 

# 错误日志存放路径，级别
# 级别：debug/info/notice/warn/error/crit；从左到右记录的信息从最详细到最少
# error_log  logs/error.log;
error_log  logs/error.log  info;

# 指定 nginx 进程运行文件存放地址,默认配置文件是注释了
# 如果上面 worker_processes 的数量大于 1 那 Nginx 就会启动多个进程
# 而发信号的时候需要知道要向哪个进程发信息，不同进程有不同的 pid，所以写进文件发信号比较简单
# pid logs/nginx.pid;

# 简化调试 此指令不得用于生产环境
# master_process  off;

# 简化调试 此指令可以用到生产环境
# daemon off;

# 最大文件描述符
worker_rlimit_nofile 51200;

# 配置影响 nginx 服务器或与用户的网络连接
events {
  # 设置网路连接序列化，防止惊群现象发生，默认为 on
  # accept_mutex on;
  
  # 设置一个进程是否同时接受多个网络连接，默认为 off
  # multi_accept on;
  
  # 事件驱动模型，使用网络 IO 模型 linux 建议 epoll，FreeBSD 建议采用 kqueue，window 下不指定
  # epoll 是多路复用 IO(I/O Multiplexing) 中的一种方式
  # use epoll;
  
  # 每一个 worker 进程能并发处理的最大连接数
  worker_connections  2048;
  
  # 并发总数是 worker_processes 和 worker_connections 的乘积
  # 即 max_clients = worker_processes * worker_connections
  # 在设置了反向代理的情况下，max_clients = worker_processes * worker_connections / 4  为什么
  # 为什么上面反向代理要除以4，应该说是一个经验值
  # 根据以上条件，正常情况下的Nginx Server可以应付的最大连接数为：4 * 8000 = 32000
  # worker_connections 值的设置跟物理内存大小有关
  # 因为并发受IO约束，max_clients的值须小于系统可以打开的最大文件数
  # 而系统可以打开的最大文件数和内存大小成正比，一般1GB内存的机器上可以打开的文件数大约是10万左右
  # 我们来看看360M内存的VPS可以打开的文件句柄数是多少：
  # $ cat /proc/sys/fs/file-max
  # 输出 34336
  # 32000 < 34336，即并发连接总数小于系统可以打开的文件句柄总数，这样就在操作系统可以承受的范围之内
  # 所以，worker_connections 的值需根据 worker_processes 进程数目和系统可以打开的最大文件总数进行适当地进行设置
  # 使得并发总数小于操作系统可以打开的最大文件数目
  # 其实质也就是根据主机的物理CPU和内存进行配置
  # 当然，理论上的并发总数可能会和实际有所偏差，因为主机还有其他的工作进程需要消耗系统资源。
  # ulimit -SHn 65535
}

http {
  # 关闭错误页面的 nginx 版本数字，提高安全性
  server_tokens off;
    
  # 设定 mime 类型,类型由 mime.type 文件定义
  include mime.types;
  
  # 加载以下文件到当前的配置里，如将 server 部分分离出去，配置反向代理
  # include    proxy.conf;
  # include    gzip.conf;
  
  # 文件类型，默认为 text/plain
  default_type  application/octet-stream;
  
  # 关闭 access_log 可以让读取磁盘 IO 操作更快
  # 当然如果你在学习的过程中可以打开方便查看 Nginx 的访问日志
  # access_log logs/access.log main;
  
  # 定义日志记录格式，如果 access_log 设置为 off 可以注释掉这段
  # log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    # '$status $body_bytes_sent "$http_referer" '
                    # '"$http_user_agent" "$http_x_forwarded_for"';
  
  client_header_timeout  3m;  
  client_body_timeout    3m;  
  send_timeout           3m;

  # 设定请求缓冲
  client_header_buffer_size    128k;  
  large_client_header_buffers  4 128k; 
  
  # 允许客户端请求的最大单文件字节数
  client_max_body_size 10m; 

  # 缓冲区代理缓冲用户端请求的最大字节数，
  client_body_buffer_size 128k;
  
  client_body_in_single_buffer on;
  
  # 允许 sendfile 方式传输文件，对于普通应用，必须设为 on,如果用来进行下载等应用磁盘 IO 重负载应用，可设置为 off
  sendfile on;
  
  # 每个进程每次调用传输数量不能大于设定的值，默认为 0，即不设上限
  # sendfile_max_chunk 100k;  
  
  # 在一个数据包里发送所有头文件，而不是一个接一个的发送
  # tcp_nopush     on;
  
  # 不要缓存
  # tcp_nodelay     on; 
  
  # 连接超时时间，默认为 75s
  keepalive_timeout  65;
  
  # 开启 gzip 压缩
  # gzip on;
  # gzip_disable "MSIE [1-6].";
  
  # 负载均衡配置，可以配置多个
  upstream crossover_main {
    # ip_hash; # 设置负载均衡策略为 ip_hash，会根据请求来源 ip 做 hash，同一个 C 类地址网段 hash 值相同
    server 127.0.0.1:8080; # 反向代理到后台应用服务器节点上
  }
  
  # 配置虚拟主机的相关参数，一个 http 中可以有多个 server
  server {
    # 单连接请求上限次数
    # keepalive_requests 120; 
    
    # 监听端口
    listen 80;
    
    # 服务器地址，默认为 localhost
    # server_name www.faychou.com faychou.com;
    server_name localhost;
    
    # 代码放置的根目录
    root /usr/www/;
    
    # 编码
    charset utf-8; #koi8-r;
    
    # 设定本虚拟主机的访问日志
    # access_log  logs/host.access.log  main;
    
    # 如果访问时没有加 www，则跳转至 www.faychou.com
    if ($host !~* www.faychou.com) { 
      rewrite ^(.*)$ http://www.faychou.com/$1 permanent;
    }

    # 配置默认请求的路由，以及各种页面的处理情况
    location / {
      # 自动索引
      autoindex  on;
      
      # 显示文件大概大小
      # autoindex_exact_size  off;
      
      # 显示的文件时间为文件的服务器时间，off 则为 GMT 时间
      # autoindex_localtime on;
      
      # 10m之后下载速度为 10k
      # limit_rate_after 10m; 
      # limit_rate 10k;
      
      # 该'/'请求映射到本地文件系统 /data/www 目录
      #root /data/www;
      
      # 默认页及先后顺序
      index  index.html index.htm;
    }
    
    location /proxy {
      # 配置所有请求都会分发到这个负载均衡器上
      proxy_pass http://upstream_test;
      
      proxy_set_header Host $http_host;
      proxy_set_header X-Forwarded-For $remote_addr;
    }
    
    # 静态文件
    location ~* ^/static/.*\.(jpg|jpeg|gif|png|html|htm|js|css|swf)$ {
      root /data/static/;
      access_log  off;
      
      # 过期30天，静态文件不怎么更新，过期可以设大一点，
      # 如果频繁更新，则可以设置得小一点。
      expires  30d;
    }

    # 404 页面跳转到 404.html，相对于上面的 root 目录
    # error_page 404 /404.html;
    
    # 403 页面跳转到 403.html，相对于上面的 root 目录
    # error_page  403 /403.html;
    
    # 50x 页面跳转到 50x.html
    # error_page 500 502 503 504 /50x.html;
    
    # redirect server error pages to the static page /50x.html
    location = /50x.html {
      root   html;
    }
    
    location /nginx_status {
      stub_status on;
      access_log off;
      allow 127.0.0.1;#设置为可访问该状态信息的ip
      deny all;
    }
    
    # PHP 脚本请求全部转发到 FastCGI 处理. 使用 FastCGI 默认配置.
    location ~ .php$ {
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_index index.php;
      fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
      include fastcgi_params;
    }
    
    #禁止访问 .htxxx 文件
      location ~ /.ht {
      deny all;
    }
  } 

  # HTTPS server
  # server {
    # listen 443;
    # server_name  localhost;
    
    # 为一个server{......}开启ssl支持
    # ssl on;
    
    # 指定PEM格式的证书文件 
    # ssl_certificate cert.pem;
    
    # 指定PEM格式的私钥文件
    # ssl_certificate_key cert.key;
    # ssl_session_cache    shared:SSL:1m;
    # ssl_session_timeout 5m;
    # ssl_protocols  SSLv2 SSLv3 TLSv1;
    # ssl_ciphers  HIGH:!aNULL:!MD5;
    # ssl_prefer_server_ciphers   on;
    
    # location / {
      # root html;
      # index index.html index.htm;
    # }
  # }
}
```

#### 配置说明：
#### 全局变量
``` bash
$args # 这个变量等于请求行中的参数，同$query_string。

$body_bytes_s ent #记录发送给客户端文件主体内容大小。


$content_length # 请求头中的Content-length字段。

$content_type # 请求头中的Content-Type字段。

$document_root # 当前请求在root指令中指定的值。

$document_uri # 与$uri相同。

$host # 请求主机头字段，否则为服务器名称。

$http_user_agent # 记录客户端浏览器的相关信息。

$http_cookie # 客户端cookie信息。

$http_referer #用来记录从那个页面链接访问过来的。

$limit_rate # 这个变量可以限制连接速率。

$remote_addr # 记录客户端的IP地址。

$remote_port # 记录客户端的端口。

$remote_user # 记录客户端用户名称。

$request # 用来记录请求的url与http协议。

$request_method # 客户端请求的动作，通常为GET或POST。

$request_uri # 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。

$request_filename # 当前请求的文件路径，由root或alias指令与URI请求生成。

$scheme # HTTP方法（如http，https）。

$server_protocol # 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。

$server_addr # 服务器地址，在完成一次系统调用后可以确定这个值。

$server_name # 服务器名称。

$server_port # 请求到达服务器的端口号。

$status #用来记录请求状态；成功是200。

$time_local # 用来记录访问时间与时区。

$uri # 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
```

##### server
值得注意的是server字段里面的root字段，这个字段需要跟alias字段区分开来，通过下面两段配置解释一下：

``` bash
# 当用root配置的时候，root后面指定的目录是上级目录
# 并且该上级目录必须含有和location后指定的名称的同名目录，否则404
# root末尾的"/"加不加无所谓
# 下面的配置如果访问站点http://localhost/test1访问的就是/usr/www/test1目录下的站点信息
location /test1/ {
    root /usr/www/;
}
# 如果用alias配置，其后面跟的指定目录是准确的，并且末尾必须加"/"，否则404
# 下面的配置如果访问站点http://localhost/test2访问的就是/usr/www/目录下的站点信息
location /test2/ {
    alias /usr/www/;
}
```
大家在实践过程中注意区分即可，配置之后要是碰到404可以先考虑是否是这个原因。

##### localtion
``` js
localtion / {
  // 所有请求都匹配以下规则
  // 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
}

location = / {
  // 精确匹配 / ，后面带任何字符串的地址都不符合
}

localtion /api {
  // 匹配任何 /api 开头的URL，包括 /api 后面任意的, 比如 /api/getList
  // 匹配符合以后，还要继续往下搜索
  // 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
}

localtion ~ /api/abc {
  // 匹配任何 /api/abc 开头的URL，包括 /api/abc 后面任意的, 比如 /api/abc/getList
  // 匹配符合以后，还要继续往下搜索
  // 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  // ~ 开头表示区分大小写
}

localtion ^~ /images/ {
  // 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条
}

location ~* \.(gif|jpg|jpeg)$ {
  // 匹配所有以 gif,jpg或jpeg 结尾的请求
  // ~* 开头表示不区分大小写
}
```

顺序优先级：

(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)

实际使用建议：

``` js
// 第一个必选规则，直接匹配网站根
location = / {
    proxy_pass http://tomcat:8080/index
}

// 第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
// 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
location ^~ /static/ {
    root /webroot/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}

// 第三个规则就是通用规则，用来转发动态请求到后端应用服务器
// 非静态文件请求就默认是动态请求
location /api/ {
    proxy_pass http://tomcat:8080/
}
```

##### rewrite
能够根据域名、uri的不同，将http请求分发到后端不同的应用服务器节点上。

例如,我有一个 Node 服务的名字是 o2blog_wx ，在启动 Node 的时候访问的地址是：`http://localhost:3000/`，但是对外网我们希望是：`http://aotu.jd.com/o2blog_wx` :

``` bash
server {
    listen 80;
    server_name aotu.jd.com;
    root /var/www/;
    location /o2blog_wx/ {
        # 反向代理我们通过proxy_pass字段来设置
        # 也就是当访问http://aotu.jd.com/o2blog_wx的时候经过Nginx反向代理到服务器上的http://127.0.0.1:3000
        # 同时由于解析到服务器上的时候o2blog_wx这个字段都要处理
        # 所以通过rewrite字段来进行正则匹配替换
        # 也就是http://aotu.jd.com/o2blog_wx/hello经过Nginx解析到服务器变成http://127.0.0.1:3000/hello
        proxy_pass http://127.0.0.1:3000;
        rewrite ^/o2blog_wx/(.*) /$1 break;
    }
}
```

### 查看公网IP
运行以下命令来显示你的服务器的公共IP地址:

``` bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

## 负载均衡
公司一般会建立很多的服务器，这些服务器组成了服务器集群，然后，当用户访问网站的时候，先访问一个中间服务器，再让这个中间服务器在服务器集群中选择一个压力较小的服务器，然后将该访问请求引入选择的服务器。以保证服务器集群中的每个服务器压力趋于平衡，分担了服务器压力，避免了服务器崩溃的情况，充分利用资源。

``` bash
# upstream，定义一个上游服务器集群
upstream backend {
  server 127.0.0.1:3000;
  server 47.93.6.93;
}
server {
    listen       80;
	 server_name  localhost;
	 
    location / {
        proxy_pass http://backend;
    }
}
```

这样每次刷新浏览器都会发现，访问的服务器并不一定是同一台，但是当用户第一次访问到其中一台服务器后，下次再访问的时候就直接访问该台服务器就好了，不用总变化了。这时就需要使用 ip_hash：

``` bash
#...
    upstream firstdemo {
        ip_hash;
        server 39.106.145.33;
        server 47.93.6.93;
    }
```

ip_hash 它的作用是如果第一次访问该服务器后就记录，之后再访问都是该服务器了。

## 反向代理
互联网应用基本都基于 CS 基本结构，即 client 端和 server 端。代理其实就是在 client 端和真正的 server 端之前增加一层提供特定服务的服务器，即代理服务器。

正向代理：如翻墙工具其实就是一个正向代理工具。它会把我们访问墙外服务器 server 的网页请求，代理到一个可以访问该网站的代理服务器 proxy，这个代理服务器 proxy 把墙外服务器 server 上的网页内容获取，再转发给客户。

反向代理（Reverse Proxy）则正好相反，客户端发送的请求，想要访问 server 上的内容。但将被发送到一个代理服务器 proxy，这个代理服务器将把请求代理到和自己属于同一个 LAN 下的内部服务器上，而用户真正想获得的内容就储存在这些内部服务器上。

通过反向代理，同时也能解决跨域的问题，而解决跨域问题的方式有很多，比如有 window.name、iframe、JSONP、CORS 等等，就不详细展开了，涉及到 协议、端口 不一样的跨域请求方式是采用代理。

Nginx 反向代理的配置：

``` js
server {
  listen  80;
  # server_name 127.0.0.1;

  location / {
    // 反向代理我们通过proxy_pass字段来设置，默认不会转发请求中的 Host 头部
    proxy_pass  http://127.0.0.1:3000;
    
    //下面都是次要关注项
    proxy_set_header Host $host;
    proxy_method POST;
    
    //指定不转发的头部字段
    proxy_hide_header Cache-Control;
    proxy_hide_header Other-Header;
    
    //指定转发的头部字段
    proxy_pass_header Server-IP;
    proxy_pass_header Server-Name;
    
    //是否转发包体
    proxy_pass_request_body on | off;
    
    //是否转发头部
    proxy_pass_request_headers on | off;
    
    //显形/隐形 URI，上游发生重定向时，Nginx 是否同步更改 uri
    proxy_redirect on | off;
  }
}
```

> 上面的配置的可以理解为：

> 监听8080端口（Nginx默认启动了80端口），将 http://127.0.0.1 的所有请求服务转发到 127.0.0.1 端口为 3000；

``` js
// 最简单的配置
location ~ /api/ {
  proxy_pass  http://172.30.1.123:8081;
}
```

将 http://127.0.0.1/api/ 或者 http://127.0.0.1/api/getList 请求转发到 http://172.30.1.123:8081 。然后就可以通过 http://127.0.0.1 访问我们的WEB应用。

``` js
// 请求跨域，这里约定代理请求 url path 是以 /api/ 开头
location ^~/api/ {
  // 重写请求，将正则匹配中的第一个()中 $1 的 path，拼接到真正的请求后面，并用 break 停止后续匹配
  rewrite ^/api/(.*)$ /$1 break;
  proxy_pass https://www.kaola.com/;
}
```

当我们需要获取真实 IP 的业务时，还需要添加关于真实 IP 的配置：

``` js
server {
  listen  8080;
  server_name 127.0.0.1;

  location / {
    proxy_pass  http://127.0.0.1:3000;
    
    //以下三行是将代理服务器收到的用户的信息传到真实服务器上
    proxy_set_header Host $host:80;
    //proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location ~ /api/ {
    proxy_pass  http://172.30.1.123:8081;
    proxy_set_header Host $host:80;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

`proxy_set_header`这个配置是改变 HTTP 的请求头，而`Host`是请求的主机名，`X-Real-IP`是请求的真实IP，`X-Forwarded-For`表示请求是由谁发起的。

> 注意：Nginx每一条配置语句后面都必须要加分好; 否则会报配置错误。

### 反向代理单独配置文件参考
使用这个配置文件需要在 nginx.conf 文件中的 `http{}` 字段配置 `include proxy.conf;` 。

``` bash
## Basic reverse proxy server ##

## Start www.faychou.cn ##
server {
  listen 80;
  server_name  www.faychou.cn;

  access_log  logs/faychou.access.log  main;
  error_log  logs/faychou.error.log;
  root   html;
  index  index.html index.htm;

  location / {
    proxy_pass  http://127.0.0.1:3000;

    #Proxy Settings
    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
    proxy_max_temp_file_size 0;
    proxy_connect_timeout      90;
    proxy_send_timeout         90;
    proxy_read_timeout         90;
    proxy_buffer_size          4k;
    proxy_buffers              4 32k;
    proxy_busy_buffers_size    64k;
    proxy_temp_file_write_size 64k;
  }
}
```

#### 例如:
任何请求过来，首先 try_files，尝试直接匹配文件；没找到就匹配静态资源；还没找到就交给 Node 处理；否则就返回 4xx/5xx 的状态码。

``` bash
events {
    #这里可不写东西
    use epoll;
}
http {
    server {
        listen 127.0.0.1:8888;
        # 如果请求路径跟文件路径按照如下方式匹配找到了，直接返回
        try_files $uri $uri/index.html;
        location ~* ^/(js|css|image|font)/$ {
            # 静态资源都在 static 文件夹下
            root /home/barret/www/static/;
        }
        location /app {
            # Node.js 在 9000 开了一个监听端口
            proxy_pass http://127.0.0.1:9000;
        }
        # 上面处理出错或者未找到的，返回对应状态码文件
        error_page    404            /404.html;
        error_page    502  503  504  /50x.html;
    }
}
```

### 配置临时跳转
有时候我们觉得一开始配置的URL不好想换掉，但又不想原先的链接失效，比如一开始对外网的链接是：http://faychou.com/api/，后来想改成http://faychou.com/apis，又不想原先的失效。

这个时候可以在Nginx上配置一个302临时跳转（server部分跟前面的一样）：

``` js
location /o2blog_wx/ {
  // 当匹配到 http://faychou.com/api/ 的时候会跳转到 http://faychou.com/apis
  return 302 http://faychou.com/apis
}
```

### 配置限制访问
如在一台服务器上的资源不全是对外开放的，这个时候就需要通过 Nginx 配置一个限制访问，比如查看本服务器的 PHP 信息，我们就可以通过下面配置来实现限制访问：

``` js
// 当匹配到 /info 的时候只允许 10.7.101.224 访问，其它的机器都会 403 拒绝访问
// 同时重写请求为 /info.php
location = /info {
  allow 10.7.101.224;
  deny all;
  rewrite (.*) /info.php
}
```

又如：

``` js
location / {
  deny  192.168.1.11;
  allow 192.168.1.10/200;
  allow 10.110.50.16;
  deny  all;
}
```

首先禁止 192.168.1.11 访问，然后允许 192.168.1.10-200 IP 段内的访问（排除192.168.1.11），同时允许 10.110.50.16 这个单独 IP 的访问，剩下未匹配到的全部禁止访问。

### 适配 PC 与移动环境
Nginx 可以通过内置变量 `$http_user_agent`，获取到请求客户端的 userAgent，从而知道用户处于移动端还是 PC，进而控制重定向到 H5 站还是 PC 站。

``` js
location / {
  // 移动、PC 设备适配
  if ($http_user_agent ~* '(Android|webOS|iPhone|iPod|BlackBerry)') {
    set $mobile_request '1';
  }
  if ($mobile_request = '1') {
    rewrite ^.+ https://m.faychou.com;
  }
} 
```

### 合并请求
前端性能优化中重要一点就是尽量减少 http 资源请求的数量。通过 nginx-http-concat 模块（淘宝开发的第三方模块，需要单独安装）用一种特殊的请求 url 规则（例子：example.com/??a.js,b.js,c.js ），前端可以将多个资源的请求合并成一个请求，后台 Nginx 会获取各个资源并拼接成一个结果进行返回。例如上面的例子通过一个请求将 a.js,b.js,c.js 三个 js 资源合并成一个请求，减少了浏览器开销。

``` js
// js 资源 http-concat
location /static/js/ {
  concat on;  // 是否打开资源合并开关
  concat_types application/javascript;  // 允许合并的资源类型
  concat_unique off;  // 是否允许合并不同类型的资源
  concat_max_files 5;  // 允许合并的最大资源数目
}
```

### 图片处理
在前端开发中，经常需要不同尺寸的图片。现在的云储存基本对图片都提供有处理服务（一般是通过在图片链接上加参数）。其实用 Nginx，可以通过几十行配置，搭建出一个属于自己的本地图片处理服务，完全能够满足日常对图片的裁剪/缩放/旋转/图片品质等处理需求。需要用到 ngx_http_image_filter_module 模块，需要安装。

``` js
// 图片缩放处理
// 这里约定的图片处理 url 格式：以 faychou.cn/img/ 路径访问
location ~* /img/(.+)$ {
  alias /Users/cc/Desktop/server/static/image/$1;  // 图片服务端储存地址
  set $width -;  // 图片宽度默认值
  set $height -;  // 图片高度默认值
  if ($arg_width != "") {
    set $width $arg_width;
  }
  if ($arg_height != "") {
    set $height $arg_height;
  }
  image_filter resize $width $height;  // 设置图片宽高
  image_filter_buffer 10M;   // 设置 Nginx 读取图片的最大 buffer
  image_filter_interlace on;  // 是否开启图片图像隔行扫描
  error_page 415 = 415.png;  // 图片处理错误提示图，例如缩放参数不是数字
}
```

通过 proxy_cache 配置 Nginx 缓存，避免每次请求都重新处理图片，减少 Nginx 服务器处理压力。

### 解决前端使用 HTML5 History 模式
现在的路由一般分为两种：

一种是后端路由，用户通过 URL 访问到的页面，是通过后端路由匹配之后再返回给浏览器的；

另一种是前端路由，页面跳转的 URL 规则匹配由前端来控制。而前端路由主要是有两种显示方式：

* 带有 hash 的前端路由，优点是兼容性高。缺点是 URL 带有 # 号不好看；

* HTML% 的 history 模式，优点是 URL 不带 # 号，好看。缺点是既需要浏览器支持也需要后端服务器支持。

需要注意的是，只要在浏览器地址栏输入 URL 并回车，是一定会去请求服务器的。而如果是在页面里通过点击按钮等操作，利用 router 库的 api 来进行的 URL 更新是不会去后端服务器请求的。

所以当前端使用 history 模式时，这个 URL 是会向后端发起一个 get 请求的。如果后台没有配置相应的路由，那自然就会返回 404。所以如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。

``` js
location / {
  try_files $uri /index.html;
}
```

### 页面内容修改
Nginx 可以通过向页面底部或者顶部插入额外的 css 和 js 文件，从而实现修改页面内容。这个功能需要额外模块的支持，例如：nginx_http_footer_filter 或者 ngx_http_addition_module (都需要安装)。
工作中，经常需要切换各种测试环境，而通过 switchhosts 等工具切换后，有时还需要清理浏览器 dns 缓存。可以通过页面内容修改 +Nginx 反向代理来实现轻松快捷的环境切换。
这里首先在本地编写一段 js 代码（switchhost.js），里面的逻辑是：在页面插入 hosts 切换菜单以及点击具体某个环境时，将该 host 的 ip 和 hostname 储存在 cookie 中，最后刷新页面；接着编写一段 css 代码（switchhost.css）用来设置该 hosts 切换菜单的样式。

``` js
server {
  listen 80;
  listen  443 ssl;
  expires -1;
  // 想要代理的域名
  server_name faychou.cn;
  set $root /Users/cc/Desktop/server;
  charset utf-8;
  ssl_certificate /usr/local/etc/nginx/faychou.cn.crt;
  ssl_certificate_key /usr/local/etc/nginx/faychou.cn.key;

  // 设置默认 $switch_host，一般默认为线上 host，这里的 1.1.1.1 随便写的
  set $switch_host '1.1.1.1';
  // 设置默认 $switch_hostname，一般默认为线上 'online'
  set $switch_hostname '';
  // 从 cookie 中获取环境 ip
  if ($http_cookie ~* "switch_host=(.+?)(?=;|$)") {
    set $switch_host $1;
  }
        
  // 从 cookie 中获取环境名
  if ($http_cookie ~* "switch_hostname=(.+?)(?=;|$)") {
    set $switch_hostname $1;
  }
        
  location / {
    expires -1;
    index index.html;
    proxy_set_header Host $host;
    // 把 html 页面的 gzip 压缩去掉，不然 sub_filter 无法替换内容
    proxy_set_header Accept-Encoding '';
    // 反向代理到实际服务器 ip
    proxy_pass  http://$switch_host:80;
    // 全部替换
    sub_filter_once off;
    // ngx_http_addition_module 模块替换内容。
    // 这里在头部插入一段 css，内容是 hosts 切换菜单的 css 样式
    sub_filter '</head>' '</head><link rel="stylesheet" type="text/css" media="screen" href="/local/switchhost.css" />';
    // 将页面中的'飞舟实验室'文字后面加上环境名，便于开发识别目前环境
    sub_filter '飞舟实验室' '飞舟实验室:${switch_hostname}';
    // 这里用了另一个模块 nginx_http_footer_filter，其实上面的模块就行，只是为了展示用法
    // 最后插入一段 js，内容是 hosts 切换菜单的 js 逻辑
    set $injected '<script language="javascript" src="/local/switchhost.js"></script>';
    footer '${injected}';
  }
  // 对于 /local/ 请求，优先匹配本地文件
  // 所以上面的 /local/switchhost.css，/local/switchhost.js 会从本地获取
  location ^~ /local/ {
    root $root;
  }
}
```

## HTTPS
我们都知道HTTP在传输的过程中都是明文的，这直接导致了在传输的任何一个过程中都容易被窃取信息，所以才有了SSL（安全套接层）以及升级版TLS（传输层安全协议）的出现，其实就是在HTTP应用层给TCP/IP传输层的中间增加了TLS/SSL层，统称为HTTPS。

### 本地开发证书生成
``` bash
#首先，进入你想创建证书和私钥的目录，例如：
cd /usr/local/nginx/conf

#创建服务器私钥，命令会让你输入一个口令：
openssl genrsa -des3 -out server.key 2048

#创建签名请求的证书（CSR）：
openssl req -new -key server.key -out server.csr
```

### https文件配置
``` js
server {
  // 默认情况下HTTPS监听443端口
  listen  443 ssl;
  server_name  www.faychou.cn;
  root  /usr/www/;
  // 下面这些都是配置SSL需要的
  //ssl on;
  // sslcertificate 和 sslcertificate_key 需要指定证书目录文件，也就是将你下载的文件复制到/etc/nginx/ssl 目录中去就行
  ssl_certificate /etc/nginx/ssl/www.faychou.cn_cert.crt;
  ssl_certificate_key /etc/nginx/ssl/www.faychou.cn.key;
  ssl_session_timeout 10m;
  ssl_protocols SSLv2 SSLv3 TLSv1;
  ssl_ciphers HIGH:!aNULL:!MD5;
  ssl_prefer_server_ciphers on;
  location = /info {
    allow 127.0.0.1;
    deny all;
    rewrite (.*) /info.php;
  }
  location /images {
    root /usr/local/static/;
  }
  location / {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass         http://127.0.0.1:3000;
  }
  error_page 403 /403.html;
  error_page 404 /404.html;
}
```
上面配置之后，就可以通过`https://localhost`访问首页了。当然若要在对外网使用，必须购买第三方信任证书才行。

## 配置HTTP 2.0
将 Nginx 配置支持 HTTP2.0，实现网页秒开。[教程地址](http://cnt1992.xyz/2016/04/08/upgrade-nginx-to-http2/)

http2 是建立在 https 基础上的，所以你必须先有申请的证书。确保你的 Nginx 版本是 > 1.9.5

``` bash
# 修改 Nginx 配置
sudo vim /etc/nginx/sites-available/your_domain  

# 添加 443 端口的监听然后增加 http2 模块；
listen [::]:443 ssl http2 ipv6only=on;  
listen 443 ssl http2;  

# 修改 nginx.conf 文件
sudo vim /etc/nginx/nginx.conf  

# 在 ssl_prefer_server_ciphers on; 下面添加下面的配置:
ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;

# 测试
sudo nginx -t

# 如果看到 successful 字样，那就意味着配置没有问题;
# 重启 nginx 服务器

sudo service nginx restart 
```
