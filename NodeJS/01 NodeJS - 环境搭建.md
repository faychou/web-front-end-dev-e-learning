# 环境搭建

## window 系统
打开 NodeJS 官网 https://nodejs.org/en/download/ ，选择对应版本下载安装即可。LTS 指稳定版本，Current 指最新版本。

## Mac 系统
### Homebrew安装
1、安装 Homebrew ：

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

2、安装 node：

``` bash
brew install node
```

3、查看 node 是否安装成功：

``` bash
# 如果 node 安装成功会有 node 的版本号返回
node -v

# 查看 npm 版本
npm -v
```

## Linux 系统
1、安装 node.js

``` bash
sudo apt-get install nodejs
```

2、安装 npm

``` bash
sudo apt-get install npm
```

## 使用 nvm
在 osx、linux 环境下, 使用 nvm 来安装 node 开发环境。

如果你使用 Windows 做开发, 那么你可以使用 nvmw 来替代 nvm 。

### 安装 nvm
直接从 github clone nvm 到本地

``` bash
git clone https://github.com/creationix/nvm.git
```

配置终端启动时自动执行

``` bash
source ~/git/nvm/nvm.sh

# 在 ~/.bashrc, ~/.bash_profile, ~/.profile, 或者 ~/.zshrc 文件添加以下命令:
source ~/git/nvm/nvm.sh

# 重新打开你的终端, 输入 
nvm
```