# ES6 模块化
ES6 静态加载的设计思想，使得在编译时就可以确定模块的依赖关系，以及输入、输出的变量。ES6 则在语言层面上实现了模块化，取代 CommonJS、AMD、CMD 成为服务端和浏览器端通用的模块解决方案。

CommonJS 是运行时加载，ES6 模块是编译时加载。


### 特点
* ES6 模块自动运行在严格模式下；
* 每个模块的作用域独立，模块顶部 this 为 undefined；
* 必须导出外部可访问的函数、变量；
* 一个模块可以加载多次，但只执行一次。

## 导出&导入
``` js
// example1.js
export let/var/const param1 = 'ok'
export function method1 () {}
// 先声明再导出 
let/var/const param2 = 'error'
export param2

import { param1, method1 } from './example1.js'
import * as example1 from './example1.js'

example1.param1 // 'ok'
```

## 导出&导入默认 default
``` js
// example2.js
export default val param2 = 'ok'
// example3.js
export default function foo () {}

import param2 from './example2.js'
import foo from './example3.js'
```

## 无绑定导入
``` js
// example.js
Array.prototype.pushAll = (items) => {
    // ES6数组方法的内部实现，可以写篇文章讨论下
    // isArray方法实现：
    // Object.prototype.toString.call(arg) === '[object Array]'
    if (!Array.isArray(items)) {
        throw new TypeError('参数必须是数组')
    }
    // 使用展开运算符
    return this.push(...items)
}

// 使用时，直接import
import './example.js'
let items = []
let arr = [1, 2, 3]
items.pushAll(arr)
```

