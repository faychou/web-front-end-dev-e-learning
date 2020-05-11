# Git
一个项目的开发，往往是有多个开发者共同开发维护的，那么意味着他们需要操作同一项目文件，这就需要对文本和项目代码的变更进行记录和管理，一旦发生问题也方便对变更进行回退，这些变更就构成了一个版本库，对版本库的管理就是版本控制。

一个可以管理或追踪软件代码的工具通常称为版本控制系统（VCS）。比如 SVN、Git。

在 Git 出来之前，版本控制一直是 SVN 的天下，这是一个集中式版本控制系统。版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。

集中式版本控制系统最大的毛病就是必须联网才能工作。

而 Git 是分布式版本控制系统，该系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。

和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

## 概念
### 工作区
就是在你电脑上创建的项目目录。平时开发就是拷贝远程仓库中的一个分支，并基于该分支进行开发，不包含 ".git" 目录 。

### 暂存区
英文叫 stage, 或 index。一般存放在 ".git 目录下" 下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

暂存区会记录 `git add` 添加的文件的相关信息（文件名、大小...）,不保存文件实体。可以使用 `git status` 查看暂存区的状态。暂存区标记了你当前工作区中，哪些内容是被 Git 管理的。

### 本地仓库(版本库)
工作区有一个隐藏目录 `.git`，这就是 Git 的版本库，又名仓库，英文名 repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被 Git 管理起来，每个文件的修改、删除，Git 都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

`git commit` 后同步 stage 的目录树到本地仓库，方便从下一步通过 `git push` 同步本地仓库与远程仓库。

### 远程仓库

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
git config [--global] user.name "你的用户名"
git config [--global] user.email "你的邮箱"
```

当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置。

### 检查配置信息
``` bash
# 显示当前的 Git 配置
git config --list

# 编辑 Git 配置文件
git config -e [--global]
```

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
# 进入项目
cd <project-name>

# 初始化
git init

## 也可以直接创建新项目并初始化
git init <project-name>
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
# 添加指定文件到暂存区
git add <filename> <filename>

# 添加指定类型文件到暂存区
git add *.html

# 添加指定目录到暂存区，包括子目录
git add [dir]

# 提交所有变更（包括新提交(new)和被修改(modified)文件，但不包括被删除(deleted)文件））
git add .

# 添加已经被 add 且存在更改的文件（提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)）
git add -u

# 添加所有变化（包括新提交(new)、被修改(modified)以及被删除(deleted)文件）
git add --all
git add -A # 简写

# 对于同一个文件的多处变化在提交之前进行确认，可以实现分次提交
git add -p
```

> 注意：运行了 `git add` 之后又修改了该文件，需要重新运行 `git add` 把最新版本重新暂存起来。

### 提交到仓库
那么该文件此时此刻的版本将被留存在历史记录中。
在此之前，请一定要确认还有什么修改过的或新建的文件还没有 `git add` 过，否则提交的时候不会记录这些还没暂存起来的变化。 这些修改过的文件只保留在本地磁盘。 所以，每次准备提交前，先用 `git status` 看下，是不是都已暂存起来了， 然后再运行提交命令 `git commit`：

``` bash
git commit -m '本次提交的说明'

# 提交暂存区的指定文件到仓库区
git commit <filename1> <filename2> ... -m '本次提交的说明'

# 提交时显示所有的 diff 信息
git commit -v

# 使用新的 commit 替代上一次提交，如果代码没有任何变化，则用来改写上一次 commit 的提交信息
git commit --amend -m '需要更新的说明'
```

Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤：

``` bash
git commit -a -m '本次提交说明'
```

### 移除文件
要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。

首先从工作目录中手工删除文件，然后再运行 git rm 记录此次移除文件的操作：

``` bash
git rm <filename> <filename>
```

下一次提交时，该文件就不再纳入版本管理了。 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f（译注：即 force 的首字母）。 

另外一种情况是，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 对于忘记添加到 .gitignore 文件，又不小心添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 --cached 选项：

``` bash
# 停止追踪指定文件
git rm --cached <文件>
```

### 移动文件
``` bash
# 在移动的同时也重命名了文件
git mv file-from file-to

# 改名文件夹
git mv -f <oldfolder> <newfolder>

# 改名文件
git mv <file-origin> <file-rename>
```

## 查看
### 检查当前文件状态
``` bash
# 查看所有文件状态
git status

# 查看指定文件状态
git status [filename]
```

`git status` 命令的输出十分详细，但其用语有些繁琐。 如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种更为紧凑的格式输出。

### 查看更改
显示暂存区和工作区的代码差异：

``` bash
# 显示暂存区和工作区的差异
git diff

# 查看文件修改后的差异
git diff [files]

# 显示暂存区和上一个 commit 的差异
git diff --cached

git diff --staged

# 显示两次提交之间的差异
git diff <first-btanch>...<second-branch>

# 显示某次提交的元素数据和内容变化
git show <commit>

# 显示某次提交时，某个文件的内容
git show <commit>:<filename>

# 显示当前分支的最近几次提交
git reflog

# 从本地 master 拉取代码更新当前分支
git rebase master
```

### 历史记录
查看该分支所有的版本历史记录：

``` bash
# 显示当前分支的版本历史
git log

# 显示commit历史，以及每次commit发生变更的文件
git log --stat

# 搜索提交历史，根据关键词
git log -S [keyword]

# 显示某个 commit 历史，以及每次 commit 发生变更的文件
git log <tag> HEAD --grep feature

# 显示某个 commit 之后的所有变动，其“提交说明”必须符合搜索条件
git log <tag> HEAD --grop feature

# 显示某个文件的版本历史，包括文件改名
git log --follow <file-name>
git whatchanged <file-name>

# 显示过去5次的提交
git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
git blame <file-name>
```

> 返回的历史记录中，类似 fcef4ce4280229e2d4a9c914677f6e94e3539ede 就是每一次提交的 commit_id，使用的时候取前五位即可。

查看本地操作记录，包括 commit, merge, rebase 等操作记录，并带有版本号：

``` bash
git reflog 
```

## 版本回退
### 撤销
``` bash
# 恢复暂存区的指定文件到工作区
git checkout <commit> <file-name>

# 恢复某个 commit 的指定文件到暂存区和工作区
git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
git checkout .
```

### 回退
``` bash
# 重置暂存区的指定文件，与上一次 commit 保持一致，但工作区不变
git reset <file-name>

# 回退到上一个版本，在Git中，用HEAD表示当前版本
git reset --hard HEAD^

# 重置暂存区与工作区，与上一次 commit 保持一致，也就是后退一步
git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
git reset [commit]

# 取消暂存文件，恢复到已修改未暂存状态
git reset HEAD {filename}

git reset <--hard> HEAD^ # 回到上个版本
git reset <--hard> HEAD~3 # 回到前三次提交，依次类推

# 直接回退到指定版本 commit
git reset <--hard> <commit>

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
git reset --keep <commit>

# 这条命令会把指定的提交的所有修改回滚，并同时生成一个新的提交
git revert <commit-id>
```
> 注：不加 --hard 即只撤销 commit 的提交文件，但是不改变代码，加了之后，commit会撤销同时代码也会变为原来的代码。

> 以下写法都代表当前分支：HEAD、HEAD ~0 或者 HEAD ^0

(<commit>|HEAD)^n，指的是HEAD的第n个父提交，可以通过在“^”后面跟上一个数字，表示第几个父提交，“^”相当“^1”。例如：HEAD^2 表示HEAD的第二次父提交。(<commit>|HEAD)~n，指的是HEAD的第n个祖先提交，可以通过在“~”后面跟上一个数字，表示第几个祖父提交，“~”相当“~1”，“~n”相当于连续的<n>个“^”。例如：HEAD~2 表示HEAD的第一个父提交的第一个父提交。

``` bash
等式1：HEAD~ === HEAD^ === HEAD^1

等式2：HEAD~2 === HEAD^^ === HEAD^1^1
```

如果发现刚刚的提交是正确的,又想回到之前版本，再输入下面这个命令，相当于你那个回退没有做：
``` bash
# 重置当前分支的 HEAD 为指定 commit，同时重置暂存区和工作区，与指定 commit 一致
git reset --hard [commitid]     
# commitid 使用 git log --stat 查看
```
回滚后提交可能可能会失败，必须强制提交：
``` bash
git push origin HEAD --force
```

## 分支(branch)
``` bash
# 查看分支,列出所有分支，当前分支前面会标一个*号
# 本地分支可以不同步到远程仓库，我们可以在dev开发，然后merge到master，使用master同步代码，当然也可以同步
git branch --all # git branch

# 列出所有远程分支
git branch -r

# 列出所有本地分支和远程分支
git branch -a

# 创建本地分支
git branch <branch-name>

# 切换分支：
git checkout <branch-name>

# 新建一个分支，并切换到该分支
git checkout -b <branch-name>

# 新建一个分支，指向指定的 commit
git branch <branch-name> <commit>

# 新建一个分支，与指定远程分支建立追踪关系
git branch --track <branch-name> <remote-branch>

# 建立追踪关系，在现有分支和指定的远程分支之间
git branch --set-upstream dev origin/dev 

# 发布dev分支
git push origin dev:dev  # 这样远程仓库也有一个dev分支了

# → 重命名分支
git branch -m brancholdname branchnewname 

# 删除分支
git branch -d <branch-name>
```

### 拉取
``` bash
# git pull <远程主机名> <远程分支名>:<本地分支名>

# 将远程 origin 主机的 master 分支合并到当前 master 分支,冒号后面的部分表示当前本地所在的分支
git pull origin master:master

# 如果远程分支是与当前分支合并，则冒号后面的部分可以省略
git pull origin master

# 允许合并两个不同项目的历史记录
git pull origin master --allow-unrelated-histories

# 删除远程 branchname 分支
git push origin -d <branchname> 

# 如果合并需要采用 rebase 模式，可以使用--rebase选项
# git pull --rebase <远程主机名> <远程分支名>:<本地分支名>

git pull = git fetch && git merge
git pull --rebase = git fetch && git rebase

 # 更新分支
git fetch --p
```

### 推送
将本地分支的更新，推送到远程主机。
``` bash
# git push <远程主机名> <本地分支名>:<远程分支名>

# 如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名）
# 将本地 master 分支推送到远程 origin 主机的 master 分支
# origin 是远程 Git名字，这个可以自己定义
git push origin master  

# 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支 
git push origin :master
# 等同于
git push origin --delete master

# 如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略
git push origin

# 如果当前分支只有一个追踪分支，那么主机名都可以省略
git push

# 如果当前分支与多个主机存在追踪关系，则可以使用-u选项指定一个默认主机，这样后面就可以不加任何参数使用git push
git push -u origin master

# 不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机，这时需要使用--all选项
git push --all origin

# 如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用--force选项
# 结果导致远程主机上更新的版本被覆盖。除非你很确定要这样做，否则应该尽量避免使用--force选项
git push --force origin
```

### 合并分支
``` bash
# 合并指定分支到当前分支
git merge <branch-name>

# 在本地（当前）分支上合并远程分支
git merge origin/master  

# 在本地 master 分支上合并远程分支
git merge --no-ff origin/develop   

# 终止本次 merge，并回到 merge 前的状态
git merge --abort  
```
> 注意：合并操作之前必须保证暂存区内没有待提交内容，否则 git 会阻止合并。
有时候合并的两个分支不是同一个人的，就会有很大的概率遇到两人同时修改文件某一行的情况，这时就会发生冲突，那就需要开发者手动的解决冲突，才能让 git 继续合并。

``` bash
# 查看冲突
git status

# 解决完冲突之后，需要再提交
git add .
git commit -m "fix merge conflict"
```

## 标签
``` bash
# 列出所有 tag
git tag

# 在当前 commit 新建一个 tag
git tag <tag>

# 新建一个 tag 在指定 commit
git tag <tag> <commit>

# 删除本地 tag
git tag -d <tag>

# 删除远程 tag
git push origin :refs/tags/<tag>

# 查看 tag 信息
git show <tag>

# 提交指定 tag
git push <remote> <tag>

# 提交所有 tag
git push <remote> --tages

# 新建一个分支，指向某个 tag
git checkout -b <branch> <tag>
```

## 远程分支
``` bash
# 更新远程仓储
git remote update 

# 显示所有远程仓库
git remote -v

# 显示某个远程仓库信息
git remote show <remote>

# 增加一个新的远程仓库，并命名
git remote add <shortname> <url>

# 取回远程仓库的变化，并与本地分支合并
git push <remote> <branch-name>

# 上传本地分支到远程仓库
git push origin dev:dev  # 这样远程仓库也有一个dev分支了

# 强行推送当前分支到远程仓库
git push <remote> --force # git push <remote> -f

# 推送所有分支到远程仓库
git push <remote> --all
```

### 暂存
``` bash
# 把当前的工作隐藏起来 等以后恢复现场后继续工作
git stash 

#  恢复工作现场（恢复隐藏的文件，同时删除stash列表中对应的内容）
git stash pop 
```

### 冲突
如果同一个文件在合并分支时都被修改了则会引起冲突。
Git用 <<<<<<<，=======，>>>>>>> 标记出不同分支的内容，其中 <<<HEAD 是指主分支修改的内容，>>>>> dev6 是指 dev6上 修改的内容。
解决的办法是我们可以修改冲突文件后重新提交。

简单来讲就是正常的解决冲突过程是

1，git add .

2，git commit -m "..." 

3，git push时因为本地仓库代码与远程仓代码有冲突，所以接下来

4，git pull拉取远程代码，而冲突需要手动解决

5，解决好后重新进行git add . git commit -m".." git push

 

而git pull 这一步如果加上了 --rebase的选项，那么第5步操作将变成如下

git add .

git rebase --continue

git push

 

所以git pull --rebase用在合并代码的时候其作用就是在一个随机创建的分支上处理冲突，避免了直接污染原来的分区。

# GitHub
## 注册

## 创建仓库

## 关联仓库
1、 首先需要生成密钥，打开终端输入以下：

``` bash
# 生成密钥，一路回车，不需要设置文件名和密码,方便以后的每次连接
ssh-keygen -t rsa -C "你的github用户名"
# -t 指定密钥类型，默认是 rsa ，可以省略。
# -C 设置注释文字，比如邮箱；
# -f 指定密钥文件存储文件名，上面命令省略了

# 查看密钥文件夹    
ls -al ~/.ssh
```

密钥默认存放路径 `~/.ssh/` 路径下，可以看到生成了两个密钥文件，后缀为 .pub 的就是公钥文件，另一个没有后缀的就是私钥文件。

2、复制公钥文件中的内容；

3、然后回到 Github, 点击右上角头像的下拉按钮，选择 Settings；

4、在 Settings 页面中选择左边菜单里的 SSH and GPG keys，然后点击右上角的 New SSH key 按钮，填写 Title 和 Key，然后点击 Add SSH key 按钮提交。

``` bash
# 验证是否成功
ssh -T git@github.com
```



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
```
/fd1/*
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

## GIT 常见错误
### 情境一
``` bash
Your branch is up-to-date with 'origin/master'.
```
说明当前分支已经是最新的了，不需要再 commit 了。