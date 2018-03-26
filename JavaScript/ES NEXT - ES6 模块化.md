# ES6 模块化
JavaScript 之前是没有模块化的，ES6 标准中就原生支持模块化，通过将不同功能的代码分别写在不同的文件中，各模块只需 导出(export) 公共接口部分，然后在需要使用的地方通过 导入(import) 就可以了。

ES6 静态加载的设计思想，使得在编译时就可以确定模块的依赖关系，以及输入、输出的变量。ES6 则在语言层面上实现了模块化，取代 CommonJS、AMD、CMD 成为服务端和浏览器端通用的模块解决方案。

CommonJS 是运行时加载，ES6 模块是编译时加载。

### 特点
* ES6 模块自动运行在严格模式下，所以模块顶部 this 为 undefined；
* 每个模块的作用域独立；
* 必须导出外部可访问的函数、变量；
* 模块输出为值的引用，CommonJS 是值的拷贝；
* 一个模块可以加载多次，但只执行一次；
* 同步、异步加载均可。

## 模块加载
在浏览器中，默认的行为就是将 JavaScript 作为脚本来进行加载，而非模块。所以我们将type 属性指定为 "module" 来告诉浏览器加载的是模块：

``` html
<!-- 第一种方式 -->
<script type="module">
  import { add } from "./example";
  let num = add(1, 1);
</script>

<!-- 第二种方式 -->
<script type="module" src="example.js">
```

当 HTML 解析器遇到 `<script>` 元素的 `type="module"` 的时候，模块文件就开始下载，直到文件被完全解析完成才会去执行模块内的代码。模块文件是按照他们出现在 HTML 文件中顺序执行的，也就是说无论用何种方式引入模块，第一个 `<script type="module">` 总是在第二个 `<script type=""module>` 之前执行。


## export
在 ES6 中我们可以使用 export 关键字将一部分代码暴露给其他模块，以供其他模块或代码使用。

``` js
// 导出变量
export var person = 'fay'

// 导出函数
export function run() {  
  console.log('runing')
}

//导出类
export class Add {
  constructor(value) {
    this.value = value + a;
  }
}

var age = 18
```

该文件导出了三个对象：变量 p1 、函数 run 和类 Add， 而变量 age 由于没有 export，则仍为此模块私有，不能被其他文件使用。

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
注意使用 export 导出的函数和类都需要一个名称，所以当我们需要导出匿名的函数或者类时，使用关键字 default。default 关键字在每一个模块中只能使用一次。

``` js
// 第一种写法
export default function(a, b) {
  return a + b;
}

// 第二种写法
function add(a, b) {
  return a + b;
}
export default add;

// 第三种写法
function add(a, b) {
  return a + b;
}
export { add as default };

// 同时默认导出多个元素写法
export default {
  p1,
  run,
  age
}
```

## import
在 ES6 中，要导入其他模块中的功能可以通过 import 关键字。import 语句由两部分组成：要导入元素的标识符和元素应当从哪个模块导入。

``` js
import app from './app.js'; 
```

### 多元素导入
若想导入多个对象，可以使用以下方法，如：

``` js
// 导入多个元素
import {p1, run} from './app.js'

// 导入整个模块
import * as app from "./app.js"
```

上面的导入整个模块就是把 app.js 中导出的所有元素全部加载到一个叫做 app 的对象中，而所导出的元素就会作为 app 的属性或方法被访问。因为 app 对象是作为 app.js 中所导出成员的命名空间对象而被创建的，所以这种导入方式被称为命名空间导入。

### 无对象导入
如果模块只包含一些逻辑要执行，且不会导出任何对象，此类对象也可以被导入到另一模块中，导入之后只执行逻辑。如：

``` js
// 使用该模块时，直接 import
import './example.js'
```

### 
不管 import 语句把一个模块写了多少次，该模块只执行一次。意思就是，在首次执行导入模块后，实例化的模块就会被保存在内存中，只要使用 import 语句引用它就可以重复使用：

``` js
// 首次导入模块
import { a } from "./app.js"

// 下面的两个 import 将无需重复加载 app.js
import { addA } from "./app.js"
import { say2 } from "./app.js"
```

## 注意
### 语法限制
export 和 import 必须在条件语句和函数之外使用：

``` js
if (ture) {
  export var a = 1; //语法错误
}

function add() {
  import a from "./app.js"; //语法错误
}
```

### 重命名
在导出过程或者导入过程中去改变导出元素的名称：

``` js
// 导出
function add(a, b) {
  return a + b;
}
export { add as add1 };  // 在导入时必须使用 add1 作为名称 

// 导入
import {add as add1 } from "./app"

console.log(add1(1,1));  // 2
console.log(typeof add); // undefined
```

### 导出已导入的元素
同样可以在本模块内导出我们在本模块内导入的元素：

``` js
//  第一种语法
import { add } from ./example.js;
export { add };

//  第二种语法
export { add } from ./example.js;

//换一个名称导出
export { add as add1 } from ./example.js; //以add这个名称导入，再以add1的名称导出

// 导出整个模块
export *  from ./example.js;
```

## export & import 结合
### 一般的 导出 & 导入
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

### 导出 & 导入 默认 default
``` js
// example2.js
export default val param2 = 'ok'
// example3.js
export default function foo () {}

import param2 from './example2.js'
import foo from './example3.js'
```

