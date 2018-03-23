# ES6 模块化
JavaScript 之前是没有模块化的，ES6 标准中就原生支持模块化，通过将不同功能的代码分别写在不同的文件中，各模块只需 导出(export) 公共接口部分，然后在需要使用的地方通过 导入(import) 就可以了。

ES6 静态加载的设计思想，使得在编译时就可以确定模块的依赖关系，以及输入、输出的变量。ES6 则在语言层面上实现了模块化，取代 CommonJS、AMD、CMD 成为服务端和浏览器端通用的模块解决方案。

CommonJS 是运行时加载，ES6 模块是编译时加载。

### 特点
* ES6 模块自动运行在严格模式下；
* 每个模块的作用域独立，模块顶部 this 为 undefined；
* 必须导出外部可访问的函数、变量；
* 模块输出为值的引用，CommonJS 是值的拷贝；
* 一个模块可以加载多次，但只执行一次；
* 同步、异步加载均可。

## export
``` js
export var p1 = 'fay'

export function run() {  
  console.log('runing')
}

var age = 18
```

该文件导出了两个对象：变量 p1 和函数 run， 而变量 age 由于没有 export，则仍为此模块私有，不能被其他文件使用。

如果有很多对象需要导出的时候，可以在最后统一导出为一组对象：

``` js
export var p1 = 'fay'

export function run() {  
  console.log('runing')
}

var age = 18

export { p1,run,age }
```

### default
导出时使用关键字 default，可将对象标注为 default 对象导出。default 关键字在每一个模块中只能使用一次。

``` js
export default {
  p1,
  run,
  age
}
```

## import
``` js
import app from './app.js'; 
```

若导出了一组对象，则应该在导入声明中一一列出这些对象，如：

``` js
import {p1, run} from './app.js'
```

### 无对象导入
如果模块只包含一些逻辑要执行，且不会导出任何对象，此类对象也可以被导入到另一模块中，导入之后只执行逻辑。如：

``` js
// 使用该模块时，直接 import
import './example.js'
```

## 导出&导入引用关系
### 一般的导出&导入
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

### 导出&导入默认 default
``` js
// example2.js
export default val param2 = 'ok'
// example3.js
export default function foo () {}

import param2 from './example2.js'
import foo from './example3.js'
```

