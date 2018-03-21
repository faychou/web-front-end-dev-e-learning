# npm
npm 是随同 NodeJS 一起安装的包管理工具。

## 使用场景
* 允许用户从NPM服务器下载别人编写的第三方包到本地使用；

* 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用；

* 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

## 安装 npm
安装好 nodejs 后就自带 npm 了。

## npm 常用命令
### 全局安装
``` bash
npm install -g <package-name>
```

#### 在全局安装时可能会提醒你权限不够，解决方式：
1、方法一，以管理员的身份安装：

``` bash
sudo npm install -g XXX
```

评价：每次都要输入账号和密码，非常繁琐，且官方并不推荐。

2、方法二：

``` bash
sudo chown -R 你的账号名 npm所在目录的路径 /{lib/node_modules,bin,share}
```

评价：官方推荐的做法，chown 全称为 change owner，即将 npm 目录的所有者指定为你的名字（授予权限），-R 表示对指定目录下所有的子目录和文件也都采取同种操作。

获取 npm 所在目录的路径方法：

``` bash
npm config get prefix
# /usr/local
```

最后，完整的命令：

``` bash
sudo chown -R faychou /usr/local/{lib/node_modules,bin,share}
```

>注意：`{lib/node_modules,bin,share}` 中的大括号是要写上去的

然后再次全局安装，如：npm install -g express


### 局部安装
``` bash
npm install <package-name>

# 下载安装特定版本号的包
npm install <package-name>@<version>

# 下载安装包并添加到生产环境依赖，即将信息写入 package.json 的 dependencies 字段中
npm install <package-name> --save

# 下载安装包并添加到开发环境依赖，即将信息写入 package.json 的 devdependencies 字段中
npm install <package-name> --save-dev

# 强制安装（模块无论是否安装过，npm 都要强制重新安装，可以使用 -f 或 –force 参数）
npm install <name> -f
```

> 注意：在 Linux 或 Mac，全局安装路径 默认是用户主目录下的 node_modules，局部安装路径就是命令运行所在路径下的 node_modules。

### 搜索
``` bash
npm search <package-name>
```

### 更新
``` bash
# 本地更新
npm update <package-name>

# 全局更新
npm update -g <package-name>
```

> 注意：更新 npm 直接使用 `npm update npm -g` 或者 `npm i -g npm`。


### 卸载
``` bash
# 本地卸载
npm uninstall <package-name>

# 全局卸载
npm uninstall -g <package-name>

#卸载模块，同时删除模块留在package.json中dependencies下的对应信息
npm uninstall <package-name> --save 

#卸载模块，同时删除模块留在package.json中devDependencies下的对应信息
npm uninstall <package-name> --save-dev 
```


### 查看
``` bash
# 查看当前项目的本地依赖包
npm list

# 查看本地依赖包是否不是最新版
npm outdated <package-name>

# 查看包的安装路径
npm root

# 查看 package.json 信息 
npm view <package-name>

# 单独查看 package.json 某个配置，如 dependencies 字段
npm view <package-name> dependencies
```

### 帮助
``` bash
# 查看所有帮助信息
npm help

# 查看某个命令的帮助信息
npm <command> -h
```

### 发布
第一步：发布包之前你首先要有一个npm的账号，根据提示输入账号，密码和邮箱，然后将提示创建成功：

``` bash
npm adduser
```

> 注意：npm adduser 成功的时候默认你已经登陆了。

如果已经有账号了，直接在终端输入以下命令，然后输入你创建的账号和密码，和邮箱，登陆：

``` bash
npm login
```

第二步，发布：

``` bash
npm publish
```

> 【注意点1】不能和已有的包的名字重名！

> 【注意点2】还有一点要注意的是npm对包名的限制：不能有大写字母/空格/下滑线!

> 【注意点3】你的项目里有部分私密的代码不想发布到npm上？将它写入.gitignore 或.npmignore中，上传就会被忽略了

### 更新发布后的包
1. 修改包的版本（package.json里的version字段）
2. npm publish

## package.json
用于记录项目的名称、版本号、作者、模块入口、项目依赖等信息。通过以下命令在当前项目目录下创建一个 package.json 文件。

``` bash
# 首先在终端(即cmd)中进入项目所在路径
cd 项目路径

# 然后执行以下命令，生成 package.json 文件
npm init

# 接着按照命令提示填写信息即可
```

### 以下是常见的 package.json 的字段
* name：必选项，表示模块名称。命名时不能包含 js、node、和 url 中需要转义的字符，不能以 `.` 和 `_` 为开头。

* version：必选项，表示模块的版本号。版本号以 主版本号 (Major).副版本号(Minor).补丁版本号(Patch) 构成（如1.2.0）。

* main：必选项，模块入口文件相对路径（相对于模块根目录）。

* description：可选项，表示模块功能描述，显示在 `npm search <package-name>` 中。

* homepage：项目的官网 url。

* keywords：可选项，数组类型，表示模块的关键字，显示在 `npm search <package-name>` 中。

* author：可选项，表示项目作者。

* contributors：可选项，项目的其他贡献者姓名。

* engines：可选项，依赖的 node 版本。

* repository：可选项，源码托管地址，可以是 git 或 svn。

* scripts：可选项，自定义在 cli 中输入 `npm <script>` 时实际执行的程序。npm 默认提供大量的 script 供我们调用。

* dependencies 和 devDependencies：可选项，用于配置模块的生产环境依赖包和开发环境依赖包。当执行 `npm install` 时，npm 会根据这两个配置项的值去下载安装相关的依赖包。两者的区别是 devDependencies 是模块开发过程的依赖包（如：grunt 只在开发时有用的模块），并且当其他模块需要依赖当前模块时，当通过 `npm install <package-name>` 时会自动下载安装 dependencies 的包而不会下载 devDependencies 的包。

## cnpm
``` bash
npm install cnpm -g
```
如果你在中国，那么你可能需要使用这个源：淘宝 NPM 镜像:

``` bash
npm install cnpm -g --registry=https://registry.npm.taobao.org
```

> 注意：cnpm支持 npm 除了 publish 之外的所有命令，也就是不支持publish，当然这并不影响我们使用，publish时换回npm即可

## smart-npm
智能的 npm，让你在中国使用 npm 时，下载速度更快，使用更方便！在我们使用 npm install 时自动从国内的镜像下载，而在我们使用 npm publish 又能发布到官方的 registry 上！

### 安装
``` bash
npm install --global smart-npm --registry=https://registry.npm.taobao.org/
```

如果 window 用户安装最新版本不成功的话，可以试试安装 smart-npm@1 ， 两者功能差不多是一样的， 发布版本 2 的主要原因是由于 npm 的升级，使的在 mac 上无法通过 bin 别名的方式覆盖原来的 npm， 只能通过先删除原来的 npm link 文件，再创建一个新的；但这种方式在 window 上可能会有问题， 所以，如果你是 window 用户，并且通过上面脚本无法安装成功的话，可以用下面脚本再试试。

``` bash
npm install --global smart-npm@1 --registry=https://registry.npm.taobao.org/
```

安装成功后默认会在你的 npm 用户配置文件 ~/.npmrc 中添加淘宝的 registry。

### 卸载
``` bash
npm smart uninstall   # 2.x.x 版本的 smart-npm 在卸载前需要先执行此脚本
npm uninstall --global smart-npm
```

要先执行 npm smart uninstall 是因为如果直接执行 npm uninstall 会导致找不到 npm 文件。

## nrm
nrm 是一个 NPM 源管理器，允许你快速地在如下 NPM 源间切换，现已支持now include: npm, cnpm, taobao,nj(nodejitsu), rednpm。

### nrm安装
``` bash
npm install -g nrm
```

### 使用
``` bash
# 查看可用版本
$ nrm ls

* npm -----  https://registry.npmjs.org/
  cnpm ----  http://r.cnpmjs.org/
  taobao --  https://registry.npm.taobao.org/
  nj ------  https://registry.nodejitsu.com/
  rednpm -- http://registry.mirror.cqupt.edu.cn
  skimdb -- https://skimdb.npmjs.com/registry
  
# 使用 use 来切换当前使用哪个仓库
nrm use cnpm
```

``` bash
# 增加源
nrm add [home]
```

``` bash
# 删除源
nrm del
```

``` bash
# 测试速度
nrm test
```

> 注意： nrm只是一个源管理器，也不能使用publish命令。

## 发布第三方包
第一步：在官网注册账号： https://www.npmjs.com/signup 。
也可以通过以下命令 ，根据提示注册用户；

``` bash
npm adduser
```

第二步：在终端中登录：

``` bash
npm login
```

第三步：发布，发布过程会把整个目录发布,不想发布的内容模块, 可以通过 .gitignore 或 .npmignore 文件忽略。发布的时候，仓库要设置回 http://registry.npmjs.org；

``` bash
npm publish
```

第四步：如果包有修改，先更新 package.json 中的版本号，然后再执行以下命令：

``` bash
npm version <版本号>
```

## 常见问题
### 使用 npm 安装模块的时候，出现 npm ERR! network connect ETIMEDOUT 的几种解决方案：
1、通过config命令：

``` bash
npm config set registry https://registry.npm.taobao.org
npm info underscore #（如果上面配置正确这个命令会有字符串response）

# or
npm config set registry https://registry.npm.taobao.org
npm config set disturl https://npm.taobao.org/dist
```

2、命令行指定:

``` bash
npm --registry https://registry.npm.taobao.org info underscore 
```

3、编辑 ~/.npmrc 加入下面内容:

``` bash
registry = https://registry.npm.taobao.org
```

4、设置代理

```
npm config set proxy http://proxy-server-address:8080 
npm config set https-proxy http://proxy-server-address:8080
```

5、如果以上方法都无法解决问题，可以尝试这种：

``` bash
npm config edit
# 然后找到与 proxy、registry 相关的代码行并删除
```

### 如何清除已设置的 npm 淘宝镜像？
1、 方法一：使用 nrm 做切换。
  
2、方法二：

``` bash
npm config delete registry
npm config delete disturl

## 或者输入以下命令后找到淘宝那两行,删除
npm config edit
```