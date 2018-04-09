# Git
## 概念
### 工作区
就是在你电脑上创建的项目目录。

### 本地仓库(版本库)
工作区有一个隐藏目录 .git，这就是 Git 的版本库，又名仓库，英文名 repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被 Git 管理起来，每个文件的修改、删除，Git 都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

### 暂存区
英文叫 stage, 或 index。一般存放在 ".git 目录下" 下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

## 安装
### 在 Linux 上安装
``` bash
sudo yum install git
# 或者
sudo apt-get install git -y
```

### 在 Mac 上安装
* 方法一：Xcode Command Line Tools

* 方法二：homebrew

* 方法三：官网下载程序

### 在 Windows 上安装
直接官网下载。

## Git 配置
Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

1、 `/etc/gitconfig` 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 `--system` 选项的 git config 时，它会从此文件读写配置变量。

2、 `~/.gitconfig` 或 `~/.config/git/config` 文件：只针对当前用户。 可以传递 `--global` 选项让 Git 读写此文件。

3、 当前使用仓库的 Git 目录中的 config 文件（就是 `.git/config`）：针对该仓库。

每一个级别覆盖上一级别的配置，所以 `.git/config` 的配置变量会覆盖 `/etc/gitconfig` 中的配置变量。

## 配置用户名与邮箱
当安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。 这样做很重要，因为每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，不可更改：

``` bash
# 如果想设置为全局生效，添加 --global 参数
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置。

### 检查配置信息
使用 `git config --list` 命令来列出所有 Git 配置。

## 获取帮助
若你使用 Git 时需要获取帮助，有三种方法可以找到 Git 命令的使用手册：

``` bash
git help <cmd>
git <cmd> --help
man git-<cmd>
```

## 生成本地仓库
### 初始化仓库
如果是一个新的项目，则通过以下命令来初始化仓库：

``` bash
# cd 项目
git init
```

该命令将在项目根目录下创建一个名为 .git 的子目录（默认隐藏的），这个子目录含有你初始化的 Git 仓库中所有的必须文件，切勿改动删除该目录。

### 克隆现有的仓库
如果你想获得一份已经存在了的 Git 仓库的拷贝，比如说，从 github 上下载一个项目，这时就要用到 git clone 命令。

``` bash
git clone https://github.com/faychou
```

这会在当前目录下创建一个名为 “faychou” 的目录，并在这个目录下初始化一个 .git 文件夹，从远程仓库拉取下所有数据放入 .git 文件夹，然后从中读取最新版本的文件的拷贝。 

## 文件跟踪
### 提交到暂存区
``` bash
git add < 文件名 >
```

> 注意：运行了 `git add` 之后又修改了该文件，需要重新运行 `git add` 把最新版本重新暂存起来。

### 提交到仓库
那么该文件此时此刻的版本将被留存在历史记录中。
在此之前，请一定要确认还有什么修改过的或新建的文件还没有 `git add` 过，否则提交的时候不会记录这些还没暂存起来的变化。 这些修改过的文件只保留在本地磁盘。 所以，每次准备提交前，先用 `git status` 看下，是不是都已暂存起来了， 然后再运行提交命令 `git commit`：

``` bash
git commit -m '本次提交的说明'
```

#### 跳过使用暂存区
Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤：

``` bash
git commit -a -m '本次提交说明'
```

### 检查当前文件状态
``` bash
git status
```

`git status` 命令的输出十分详细，但其用语有些繁琐。 如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种更为紧凑的格式输出。

### 查看更改
要查看尚未暂存的文件更新了哪些部分：

``` bash
git diff
```

`git diff --staged` 或 `git diff --cached` 可查看已暂存文件和上次提交的区别

### 移除文件
要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。

首先从工作目录中手工删除文件，然后再运行 git rm 记录此次移除文件的操作：

``` bash
git rm <被删除的文件>
```

下一次提交时，该文件就不再纳入版本管理了。 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f（译注：即 force 的首字母）。 

另外一种情况是，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 对于忘记添加到 .gitignore 文件，又不小心添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 --cached 选项：

``` bash
git rm --cached <文件>
```

### 移动文件
``` bash
# 在移动的同时也重命名了文件
git mv file-from file-to
```

## 查看提交历史记录的命令
在提交了若干更新，又或者克隆了某个项目之后，你也许想回顾下提交历史。 完成这个任务最简单而又有效的命令是：

``` bash
git log
```

而 `git reflog` 的功能是查看本地操作记录，可以看到本地的commit, merge, rebase等操作记录，并带有版本号。

## 版本回退
``` bash
# 重置暂存区与工作区，与上一次commit保持一致
git reset --hard

# 取消暂存文件，恢复到已修改未暂存状态
git reset HEAD {filename}

# 表示回退到 n 个提交之前
git reset HEAD~{n}

# 直接回退到指定版本，后面带版本号
git reset {version}
```

如果发现刚刚的提交是正确的,又想回到之前版本，再输入下面这个命令，相当于你那个回退没有做：

``` bash
# 重置当前分支的 HEAD 为指定 commit，同时重置暂存区和工作区，与指定 commit 一致
git reset --hard [commitid]     
# commitid 使用 git log --stat 查看
```

### 记录每一次命令
``` bash
git reflog 
```

## 分支(branch)
``` bash
# 查看分支,列出所有分支，当前分支前面会标一个*号：
git branch

# 创建分支：
git branch <name>

# 切换分支：
git checkout <name>

# 创建+切换分支：
git checkout -b <name>

# 合并某分支到当前分支：
git merge <name>

# 删除分支：
git branch -d <name>
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

## 克隆仓库
远程仓库中，点击 Clone or download 按钮，然后点击复制链接按钮，默认是是使用 https 协议，也可以在复制链接之前点击 Use SSH ，选择使用 SSH 协议。

``` bash
#git clone "你复制的仓库链接"
git clone git@github.com:faychou/ceshi.git
```

克隆远程仓库到本地的同时重命名本地仓库的名字：

``` bash
git clone <版本库的网址> <本地目录名>
```

## 推送
1. 在仓库的每一次改动操作之后，推送同步到远程仓库之前，都需要对这一次或这一批次的操作做提交，命令为 git commit，用法是 git commit -m "你的提交备注"，只有做好提交动作，才可以开始推送改动到远程仓库同步;

2. 当我们提交了仓库的改动后，就可以开始推送改动的内容到远程仓库了，可以使用 git push 命令来推送，用法是 git push [-u] origin <分支名>，分支名默认是 master ;

> 注意：第一次推送改动可以使用 -u 参数，使用之后会绑定你这一次的仓库分支名，这样的话下一次推送就不需要加上分支名了。

具体步骤：

``` bash
# 绑定账户
##第一步：创建 SSH Key
ssh-keygen -t rsa -C email@example.com

##第二步：登陆 GitHub，打开 "Account settings" => "SSH Keys"

##第三步：点 "Add SSH Key"，填上任意 Title，在 Key 文本框里粘贴 id_rsa.pub 文件的内容

##第四步：点 "Add Key" 完成公钥的添加

# 添加远程仓库
##第一步：登陆 GitHub，创建一个新仓库

##第二步：关联远程仓库
git remote add origin git@github.com:faychou/ceshi.git

##第三步：同步远程仓库到本地，注意再次之前本地不要提交修改
git pull --rebase origin master

##第四步：第一次将本地库的内容推送到远程仓库，注意在此之前要提交所有修改
git push -u origin master

##之后每次推送的时候直接运行以下命令即可
git push origin master



```

# 三、.gitignore
一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为 .gitignore 的文件，列出要忽略的文件。

## 创建方法
### 方法一
进入项目根目录，然后新建 `.gitignore` 文件即可。

### 方法二
在终端中进入项目目录；然后输入以下命令创建：

``` bash
touch .gitignore
```

> 注意：该文件内的规则一定要在 git 管理之前创建，否则即使添加规则，也不会生效，这种情况下我们必须通过以下命令来实现忽略掉已经被 git 管理的文件：

``` bash
git rm --cached FILENAME
```

### 创建全局的 .gitignore 文件
我们可以通过创建全局的 .gitignore 文件，这样每一个 git 仓库就都会生效该规则。

``` bash
# 在终端输入以下命令
git config --global core.excludesfile ~/.gitignore_global
```

## 配置语法：
* 所有空行或者以 ＃ 开头的行都会被忽略(相当于注释)
* 匹配模式可以以`/`结尾指定目录；
* `*`匹配零个或多个任意字符；
* `?`只匹配一个任意字符；
* `[abc]` 匹配任何一个列在方括号中的字符；
* `[0-9]` 表示匹配所有 0 到 9 的数字;
* `!`表示不忽略(跟踪)匹配到的文件或目录;
* 匹配模式可以以`/`开头防止递归;
* 使用`**`表示匹配任意中间目录，比如`a/**/z`。

## 示例说明
### 示例规则一：
```
fd1/*
```

说明：忽略目录 fd1 下的全部内容；注意，不管是根目录下的 /fd1/ 目录，还是某个子目录 /child/fd1/ 目录，都会被忽略；

### 示例规则二：
```/fd1/*
```

说明：忽略根目录下的 /fd1/ 目录的全部内容；

### 示例规则三：
```
/*
!.gitignore
!/fw/bin/
!/fw/sf/
```

说明：忽略全部内容，但是不忽略 .gitignore 文件、根目录下的 /fw/bin/ 和 /fw/sf/ 目录；

>> 需要强调的一点是，如果你不慎在创建.gitignore文件之前就push了项目，那么即使你在.gitignore文件中写入新的过滤规则，这些规则也不会起作用，Git仍然会对所有文件进行版本管理。

## 常见.gitignore的配置
``` bash
#Built application files
*.apk
*.ap_

# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini
.project

# Files for the Dalvik VM
*.dex

# node
node_modules/
 
# Java class files
*.class
 
# Generated files
*/bin/
*/gen/
*/out/
 
# Gradle files
.gradle/
build/
*/build/
gradlew
gradlew.bat
 
# Local configuration file (sdk path, etc)
local.properties
 
# Proguard folder generated by Eclipse
proguard/
 
# Log Files
*.log
 
# Android Studio Navigation editor temp files
.navigation/
 
# Android Studio captures folder
captures/
 
# Intellij
*.iml
*/*.iml
 
# Keystore files
#*.jks
#gradle wrapper
gradle/
 
#some local files
*/.settings/
*/.DS_Store
.DS_Store
*/.idea/
.idea/
gradlew
gradlew.bat
unused.txt

# IntelliJ IDEA Project files
.idea
*.iml
*.ipr
*.iws
out
 
# Eclipse Project files
.classpath
.project
.settings/
 
bin/
gen/
local.properties
 
Thumbs.db
 
*.bak
*.tem
*.temp
#.swp
*.*~
~*.*
```