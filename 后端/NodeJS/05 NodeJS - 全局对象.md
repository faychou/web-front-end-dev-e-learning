# 全局对象

Node 中有部分的全局对象，你可以在任何地方使用它，而无需 `require`。

## `global`
在 Node 环境中，也有唯一的全局对象，但不叫 `window`，而叫 `global`。

``` js
// 判断 JavaScript 执行环境:
if (typeof(window) === 'undefined') {
  console.log('node.js');
} else {
  console.log('browser');
}
```

## `process`
代表当前 Node 进程的相关信息，通过 `process` 对象可以拿到许多有用信息，方便处理进程相关操作。

``` js
process === global.process; // true

process.env; // 返回一个对象，包含了当前 Shell 的所有环境变量

process.version; // 'v7.10.0'，返回 Node 编译时的版本号

process.pid // 3196，返回当前进程的 PID

process.platform; // 当前系统平台 'darwin'

process.arch; // 'x64'，返回启动 Node 进程时的命令行参数数组

process.execArgv; // []，返回 Node 的命令行参数数组

process.argv; // 返回当前进程的命令行参数数组 '/usr/local/bin/node'

// process 对象提供以下方法
process.cwd(); // 返回进程当前的工作目录('/Users/faychou/node-demo')

process.chdir('/private/tmp'); // 切换当前工作目录

process.cwd(); // 返回运行当前脚本的工作目录的路径(绝对路径）

process.on(); //监听事件

process.exit(); //退出当前进程，此方法可接收一个退出状态的可选参数 code，不传入时，会返回表示成功的状态码 0。
```

> 在 Mac 和 Linux 的终端直接输入 `env`，会列出当前的环境变量

如果我们响应 `exit` 事件，就可以在程序即将退出时执行某个回调函数：

``` js
// 程序即将退出时的回调函数:
process.on('exit', function (code) {
  console.log('about to exit with code: ' + code);
});
```

### `process.argv`

`process.argv` 属性返回一个数组，这个数组包含了启动 Node 进程时的命令行参数。第一个元素为 Node 安装路径，第二个元素为当前执行的 JavaScript 文件路径，剩余的元素为其他命令行参数。如：

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
1: /Users/fay/work/node/process-args.js
2: one
3: two=three
4: four
```

### `process.nextTick(callback[, ...args])`
`nextTick()` 用于延迟回调函数的执行，将 callback 延迟到事件循环的下一次循环中。一旦当前事件轮询队列的任务全部完成，在 `next tick` 队列中的所有 `callbacks` 会被依次调用。 这种方式比 `setTimeout(fn, 0)` 更加有效率，该方法能在任何 I/O 之前调用我们的回调函数。

`args` 是调用 `callback` 时传递给他的额外参数。

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

### `process` 标准流对象

process 中有三个标准备流的操作，与 其他 streams 流操作不同的是，process 中流操作是同步、阻塞的。

`process.stderr` 是一个指向标准错误流的可写流，`console.error` 就是通过 `process.stderr` 实现的。

process.stdin 是一个指向标准输入流的可读流。

``` js
process.stdin.setEncoding('utf8')

process.stdin.on('readable', () => {
  let chunk
  // 使用循环确保我们读取所有的可用数据。
  while ((chunk = process.stdin.read()) !== null) {
    if (chunk === '\n') {
      process.stdin.emit('end')
      return
    }
    process.stdout.write(`收到数据: ${chunk}`)
  }
})

process.stdin.on('end', () => {
  process.stdout.write('结束监听')
})
```

`process.stdout` 是一个指向标准输出流的可写流，`console.log` 就是通过 `process.stdout` 实现的。

``` js
console.log = function(d) {
  process.stdout.write(d + '\n')
}

console.log('Hello Nodejs') // Hello Nodejs
```

