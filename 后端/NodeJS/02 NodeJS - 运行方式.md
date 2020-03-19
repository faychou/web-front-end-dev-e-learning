# NodeJS - 运行方式
### 方式一：命令交互模式
打开终端，键入 node 进入命令交互模式。

```
$ node
> console.log('Hello World!');
Hello World!
```

### 方式二：JS 文件
如果要运行一大段代码的话，可以先写一个 JS 文件再运行。

``` js
// hello.js
function hello() {
    console.log('Hello World!');
}
hello();
```

```
$ node hello.js
Hello World!
```