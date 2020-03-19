# 调试器
``` js
在命令行输入 node debug <被执行的脚本文件名> // 启动调试器

启动后,输入 cont/c  // 继续执行余下的代码

输入 next/n // 执行下一句代码

输入 step/s // 进入函数内部

输入 out/o //立即执行完函数内部剩余代码

setBreakpoint/sb([filename默认为当前运行脚步] , line) //设置断点

clearBreakpoint/cb([filename],line) // 取消断点

在调试过程输入repl  // 进入REPL环境

restart // 重新开始脚步调试

kill  // 终止脚本调试

list(n) // 查看当前所执行代码之前/之后的n行代码

backtrace/bt  // 查看函数/外层各函数的返回位置,包括返回代码的行号,起始字符的所在位置

run // 重新开始脚步调试

scripts // 查看当前运行的文件,及所有被加载的模块文件名. 

version // 显示V8引擎版本号
```