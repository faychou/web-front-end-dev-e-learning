# SSH
## 生成SSH
使用 `ssh-keygen` 就可以直接生成登录需要的密钥对。`ssh-keygen` 是 Linux 下的命令，不添加任何参数就可以生成密钥对。

然后根据提示信息输入即可，第一个是输入生成的私钥的名称，如果不填，默认私钥保存在 `/home/用户名/.ssh/id_rsa` 文件中。第二个是提示用户输入密码，用来保证私钥的安全的。如果填写了密码，那么在使用密钥进行登录的时候，会让你输入密码，这样子保证了如果私钥丢失了不至于被恶意使用。但是一般我们不填写，直接回车跳过，第三个是确认密码的。

> 生成的公钥的文件名，通常是私钥的文件名后面加 .pub 的后缀。

## 登陆SSH
登录远程服务器的命令是

``` bash
ssh 登录用户@服务器ip
```

## config 配置
默认情况下 ssh 是去读取 `$HOME/.ssh/id_rsa` 文件作为私钥登录的。但是不同的服务器应该使用不同的私钥。所以需要在 .ssh 目录下编写 config 文件来进行配置。

config 的配置很简单，只要指明哪个用户登录哪台远程服务器需要使用哪个私钥即可。

``` bash
# Host 指明了远程主机的 ip
Host github.com
    # User 指的是登录远程主机的用户
    User faychou
    # IdentityFile 指明使用哪个私钥文件
    IdentityFile ~/.ssh/id_rsa.github
    
Host 192.168.1.1
    User centOS
    IdentityFile ~/.ssh/id_rsa.xxx
```

编写好 config 文件之后，需要把 config 文件的权限改为 `rw-r--r--`。如果权限过大，ssh 会禁止登录。