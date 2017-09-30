# linux操作命令

```
打开命令终端   Ctrl+Alt+T
获得root权限   sudo su
查看当前文件   ls
查看文件权限   ll
查看当前所有文件（包含隐藏文件） ls -a
进入目标文件   cd 文件名
退到父本文件   cd ..
退到最初文件   cd
更改文件名     mv 文件名A 文件名B
输入之前输入过的指令 按上下方向键
移动文件到本文件中 mv 路径 .
                 例如 mv ../pic/海岛.jpg .  (第一处两点表示路径退到父本文件夹，第二处一点表示移动到此处，若移动目标文件夹所有文件 /* )
                 

删除          rm -rf 文件名
新建文件夹     mkdir 文件名
新建文件（有则进入修改）  vim 文件名
         进入编辑       按i
         退出编辑       按ESC 后连按两次大写 Z  (或者输入:wq)
         强制执行       在操作前加上 ！
         光标上下移动n行 ：n+
                       : n-
重新启动      sudo service nginx reload
查找错误      nginx -t
改变权限      chmod a+rwx 文件名
拷贝          git clone （文件路径 或 ssh地址等多种方式）
已上传文件更新 git pull
关闭防火墙     chkconfig iptables off

更多命令行操作将在步骤中详细讲解！    

```

## 目录说明：
``` bash
# 所有用户目录
/home
```

## 创建用户
``` bash
# 创建
useradd faychou

# 切换到该用户
su faychou

# 切换到用户根目录
cd ~
```