# GIT
## 安装
### unbutu系统
```
sudo apt-get install git -y
```

## 配置用户名与邮箱
``` bash
# 如果想设置为全局生效，添加 --global 参数
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

# GitHub
## 注册

## 创建仓库

## 关联仓库
1、 首先需要生成密钥，打开终端输入以下：

``` bash
#一路回车，不需要设置密码,方便以后的每次连接
ssh-keygen
```

密钥默认存放路径 `~/.ssh/` 路径下，可以看到生成了两个密钥文件，后缀为 .pub 的就是公钥文件，另一个没有后缀的就是私钥文件。

2、复制公钥文件中的内容；

3、然后回到 Github, 点击右上角头像的下拉按钮，选择 Settings；

4、在 Settings 页面中选择左边菜单里的 SSH and GPG keys，然后点击右上角的 New SSH key 按钮，填写 Title 和 Key，然后点击 Add SSH key 按钮提交。

## 克隆远程仓库到本地
1. 首先到远程仓库中，点击 Clone or download 按钮，选择使用 Use SSH，然后点击复制链接按钮;
2. 首先到远程仓库中，点击 Clone or download 按钮，选择使用 Use SSH，然后点击复制链接按钮.

``` bash
#git clone "你复制的仓库链接"
$ git clone git@github.com:faychou/ceshi.git
```

## 推送改动到远程仓库中
1. 在仓库的每一次改动操作之后，推送同步到远程仓库之前，都需要对这一次或这一批次的操作做提交，命令为 git commit，用法是 git commit -m "你的提交备注"，只有做好提交动作，才可以开始推送改动到远程仓库同步;

2. 当我们提交了仓库的改动后，就可以开始推送改动的内容到远程仓库了，可以使用 git push 命令来推送，用法是 git push [-u] origin <分支名>，分支名默认是 master ;

> 注意：第一次推送改动可以使用 -u 参数，使用之后会绑定你这一次的仓库分支名，这样的话下一次推送就不需要加上分支名了。