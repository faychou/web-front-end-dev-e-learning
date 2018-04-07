# 基本模块
## global
在 NodeJS 环境中，也有唯一的全局对象，但不叫 window，而叫 global。

``` js
// 判断 JavaScript 执行环境:
if (typeof(window) === 'undefined') {
  console.log('node.js');
} else {
  console.log('browser');
}
```

## process
代表当前 NodeJS 进程的相关信息，通过 process 对象可以拿到许多有用信息：

``` js
process === global.process; // true

process.env; // 返回一个对象，包含了当前Shell的所有环境变量

process.version; // 版本号'v7.10.0'

process.platform; // 当前系统平台 'darwin'

process.arch; // 'x64'

process.argv; // 返回当前进程的命令行参数数组 '/usr/local/bin/node'

// process对象提供以下方法
process.cwd(); // 返回当前工作目录('/Users/faychou/node-demo')

process.chdir('/private/tmp'); // 切换当前工作目录

process.cwd(); // 返回运行当前脚本的工作目录的路径(绝对路径）

process.on(); //监听事件

process.exit(); //退出当前进程
```

> 在 Mac 和 Linux 的终端直接输入 env，会列出当前的环境变量

### process.argv
process.argv 属性返回一个数组，这个数组包含了启动 Node.js 进程时的命令行参数。第一个元素为 node 安装路径，第二个元素为当前执行的 JavaScript 文件路径，剩余的元素为其他命令行参数。如：

``` js
// process.argv
process.argv.forEach((val, index) => {
  console.log(`${index}: ${val}`);
});
```

则命令行运行时这样的：

``` bash
$ node process-args.js one two=three four

0: /usr/local/bin/node
1: /Users/mjr/work/node/process-args.js
2: one
3: two=three
4: four
```

### process.nextTick(callback[, ...args])
nextTick() 函数不是立刻执行，而是要等到下一次事件循环。一旦当前事件轮询队列的任务全部完成，在 next tick 队列中的所有 callbacks 会被依次调用。 这种方式不是setTimeout(fn, 0) 的别名。它更加有效率，因此别用 setTimeout 去代替 process.nextTick，args 是调用 callback 时传递给他的额外参数。

``` js
console.log('start');
// 将在下一轮事件循环中调用:
process.nextTick(function () {
  console.log('nextTick callback!');
});
console.log('nextTick was set!');

// start
// nextTick was set!
// nextTick callback!
```

如果我们响应 exit 事件，就可以在程序即将退出时执行某个回调函数：

``` js
// 程序即将退出时的回调函数:
process.on('exit', function (code) {
  console.log('about to exit with code: ' + code);
});
```

## `__filename` 与 `__dirname`
``` js
console.log(__filename); // /Users/faychou/demo/b.js 当前文件的完整绝对路径的文件名
console.log(__dirname); // /Users/faychou/demo 当前文件所在目录的完整绝对路径
```