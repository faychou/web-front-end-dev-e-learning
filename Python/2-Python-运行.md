# 运行 Python
## 交互式环境
安装成功后，打开命令提示符窗口(运行 --> cmd)，敲入 python 并回车后，如果看到版本后，则表示安装成功。

提示符 `>>>` 表示已经在 Python 交互式环境中了，然后就可以输入任意的 Python 代码，回车后会立刻得到执行结果。

现在直接在交互式环境中输入 `print("Hello Python !");` ，并且回车后，就可以看到有消息打印出来了。

> 也可以通过运行 IDLE（搜索 IDLE） 也能直接进入到 Python 交互式环境中。

## 命令模式运行 py 文件
1、使用编辑器创建一个以 `.py` 为后缀名的文件，如  `hello.py`：

``` python
print("Hello Python !");
```

2、直接在 cmd 中运行 `hello.py` 文件:

```
python hello.py
```

## 直接运行 py 文件
在 Mac 和 Linux 上，可以像运行 `.exe` 文件那样直接运行 `.py` 文件，方法是在 `.py` 文件的第一行加上一个特殊的注释：

``` python
#!/usr/bin/env python3

print("Hello Python !");
```

然后给 hello.py 设置执行权限：

``` 
$ chmod a+x hello.py
```

接着就可以直接运行 `hello.py`了。