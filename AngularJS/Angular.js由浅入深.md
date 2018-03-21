# AngularJS由浅入深
AngularJS 诞生于2009年，由Misko Hevery 等人创建，后被Google所收购，是一个基于MVC（Model-View-Controller，模型-视图-控制器）结构的开源 JavaScript 框架，主要用于构建一个单一页面应用程序。

在使用 AngularJS 开发应用的时候要摆脱思维定势。

传统前端开发思维：* 以 JQuery 为代表；* 以 DOM 为中心；* 关注 VIEW 的变化和用户操作。

新一代前端开发思维：* 以 AngularJS 为代表；* 以 Data 为中心；* 聚焦于数据的变更。### 设计模式 MV*
#### MVC
* M：Model（数据模型），专门用来操作数据，数据的 CRUD；
* V：View（视图），对于前端来说，就是页面；
* C：Controller（控制），是视图和数据模型沟通的桥梁，用于处理业务逻辑。

MVC 的一般流程是这样的：View（界面）触发事件--》Controller（业务）处理了业务，然后触发了数据更新--》不知道谁更新了 Model 的数据--》Model（带着数据）回到了 View--》View 更新数据

#### MVVM
* M：Model（数据模型）；
* V：View（视图）；
* VM：ViewModel（视图模型），就是中间层，负责监控两侧的数据，并通知另一侧进行修改。

#### MVP
* M：Model
* V：View
* P：Presenter

MVP 的思路是切断的 View 和 Model 的联系，让 View 只和 Presenter（原 Controller）交互，减少在需求变化中需要维护的对象的数量。

## SPA -单页应用程序
单页Web应用（single page application，SPA），就是只有一个 Web 页面的应用，
是加载单个 HTML 页面，并在用户与应用程序交互时动态更新该页面的Web应用程序。

* 单页面应用程序：只有第一次会加载页面, 以后的每次请求, 仅仅是获取必要的数据.然后, 由页面中 js 解析获取的数据, 展示在页面中；
* 传统多页面应用程序：对于传统的多页面应用程序来说, 每次请求服务器返回的都是一个完整的页面。

### 优势：
* 减少了请求体积，加快页面响应速度，降低了对服务器的压力；
* 更好的用户体验，让用户在 web app 感受 native app 的流畅。

##  一、认识AngularJS
AngularJS 通过 指令 扩展了 HTML，且通过表达式绑定数据到 HTML。

### 适用场景
推荐在构建一个CRUD（增加Create、查询Receive、更新Update、删除Delete）应用时使用，而对于那些图形编辑、游戏开发等应用，还是使用其他JavaScript类库比较方便。

### 引入Angular
只需要在html文档中插入以下代码即可：

``` html
<script src="angular.js"></script>
```

### Hello World
学习AngularJS，我们依旧从写一个Hello World应用开始！

``` html
<!DOCTYPE html>
<html ng-app>
<head>
	<meta charset="UTF-8">
	<title>hello world</title>
</head>
<body>
	<h1>{{ 'Hello World!' }}</h1>
	
	<script src="js/angular.min.js"></script>
</body>
</html>
```

### ng-app 指令
`ng-app` 属性标记了 AngularJS 脚本的作用域，即说明整个 `<html>` 都是 AngularJS 脚本作用域。当然，也可以在局部使用 `ng-app` 指令，如 `<div ng-app>`，则 AngularJS 脚本仅在该 `<div>` 中运行。

> 注意：一个页面应该有只有一个 `ng-app`。

### 表达式
也就是代码中的两个大括号，也称之为双括号插值语法。主要作用是把数据绑定到 HTML，并在该位置上输出数据。AngularJS 表达式 很像 JavaScript 表达式：可以包含文字、运算符和变量。可以在任意位置使用表达式，包括属性。

``` html{{ 5 + 5 }}

<p>{{ '字符串表达式' }}</p>

<h1>{{ firstName + " " + lastName }}</h1>

{{ person.name }}

<p id="{{ username }}"></p>
```
> 在 AngularJS 表达式中不可以使用循环语句、判断语句。

## 二、AngularJS 应用
``` html
<!DOCTYPE html>
<html ng-app="myApp">
<head>
  <meta charset="UTF-8">
  <script src="angular.min.js"></script>
</head>
<body>
  <div ng-controller="myCtrl">
    <h1>Hello {{ firstName + " " + lastName }}</h1>
  </div>
  
  <script>
    var app = angular.module('myApp', []);    app.controller('myCtrl', function($scope) {      $scope.firstName = "Fay";      $scope.lastName = "Chou";    });
  </script>
</body>
</html>
```

`angular.module` （模块）定义了 AngularJS 应用。`ng-app` 指令定义了应用的作用域, `ng-controller` 定义了控制器。
### 模块
在 AngularJS 中，模块是定义应用的最主要方式。它包含配置、控制、过滤、工厂模式、指令及其它模块，每一个模块都包含了定义具体功能的代码。其实就是一系列函数的集合，当应用被启动时，这些函数就会被执行。

使用模块的好处:

* 保持全局命名空间的清洁;
* 方便代码的复用性;
* 应用可以以任意的顺序加载代码的各个部分;
* 测试时只需载入相关模块，可配置性高.

#### 创建模块
``` js
angular.module(name, requires);
```

* name：模块的名称，也就是 ng-app 属性的值，
* requires：依赖模块列表。如果有依赖的话会在中括号写上依赖的模块名字，如果没有依赖，则填写一个空数组。

``` js
//例如，名称为 app 的模块依赖于 controls 和 data 模块angular.module("app", [ "controls", "data"])//controls 模块依赖于 data 模块angular.module("controls", [ "data" ])//data 模块没有依赖项，数组为空angular.module("data", []);
```> 注意：在应用程序中，对 module 的声明应该有且只有已一次,多次声明 module 会导致前边的 module 定义信息被覆盖。

### 控制器控制器是视图（View）和模型（Model）之间的桥梁。在 AngularJS 中，控制器的功能是管理页面的逻辑代码。

#### ng-controller 指令
定义控制器，即声明所有被它包含的元素都属于某个控制器。

#### app.controller()
一个函数，主要用来监听视图的作用域中的请求和行为、初始化数据、处理数据、将数据同步到 view。

每一个控制器都会创建一个新的子级作用域 `$scope`。作用域 `$scope` 会作为参数注入其中，并允许用户访问它。

为了避免产品上线时，压缩 js 代码，导致函数参数压缩后，出现错误，一般控制器函数的代码会这样写：

``` js
app.controller("MyCtrl",["$scope",function ($scope) {
  $scope.firstName = "Fay";  $scope.lastName = "Chou";
}]);
```

> 注意：在 AngularJS 1.3 之前的版本中还支持全局控制器，也就是下面的方式：

``` js
function MyCtrl($scope) {
  $scope.firstName = "Fay";  $scope.lastName = "Chou";
}
```

由于是在全局作用域中创建的这个函数，会污染全局命名空间，所以后来的版本就废弃了这种写法。

此外，建议在视图中通过对象的属性而非对象本身来进行引用绑定。

``` js
var app = angular.module('myApp', []);
app.controller("MyCtrl",["$scope",function ($scope) {
  $scope.person = {
    firstName : "Fay",
    lastName : "Chou"
  };
}]);
```

### 作用域
在 AngularJS 中，作用域（scope）是构成 AngularJS 应用的核心基础，能存储数据模型、为表达式提供上下文环境和监听表达式的变化并且传播事件，它是页面视图与控制器之间的重要桥梁。

#### scope
每一个控制器下的作用域，不同控制器拥有不同的 `$scope`，实质就是一个作用域对象，用来定义应用业务逻辑、控制器方法和视图属性的地方。

#### rootScope
所有的应用都有唯一一个 `$rootScope`，作用在 `ng-app` 指令包含的所有 HTML 元素中。是各个 controller 中 `$scope` 的桥梁。用 `$rootscope` 定义的值，可以在各个 controller 中使用。#### 作用域继承作用域链：默认情况下，对任何属性和方法，如果在当前 `$scope` 上找不到，就会到父 `$scope` 上去找，如果在父 `$scope` 上也没找到，就会继续向上回溯，一直到 `$rootScope` 上。如果在 `$rootScope` 中也找不到，程序会继续运行，但视图无法更新。所以由此可知，控制器是可以嵌套的，如下：

``` html
<div ng-controller="ParentController">  <div ng-controller="ChildController">    <span>{{ chPerson }}</span>
    <span>{{ person }}</span>  </div>  {{ person }} </div>
```子级作用域可以继承父级作用域中的全部属性和方法，但同级别的子级作用域之间却不可以互相访问各自的属性和方法。实例：

``` html
<div ng-controller="TestController">
	<div ng-controller="childController">
		<button ng-click="add(1)">
			点击次数
		</button>
		<span>
			{{total}}
		</span>
	</div>
</div>

<script>
  var myApp = angular.module('myApp', []);
  myApp.controller('TestController',["$scope",function($scope) {
    $scope.total = 0;
    $scope.add = function($num) {
      $scope.total += $num;
    };
  }]);
  
  myApp.controller('childController',["$scope",function($scope) {

  }]);
</script>
```

在上面的代码中，将 childController 嵌套于 myController 内部，也就是说 childController 的 `$scope` 对象的父级作用域就是 myController 的 `$scope` 对象。虽然没有在 childController 里面定义 `add()` 方法，但依旧可以使用其父级作用域中的 `add()` 方法。

> 注意：这种继承是一种由内向外的顺序关系，也就是说，内层可以访问外层的属性或方法，但是外层却无法访问到内层的属性或方法。

我们还可以采用链式创建多个控制器：

``` js
myApp
.controller('TestController',["$scope", function($scope) {}])
.controller('childController',["$scope", function($scope) {}]);
```

#### `$watch`
`$watch` 是一个 scope 函数，用于监听模型变化，当你的模型数据发生变化时它会通知你。

``` js
$watch(watchExpression, listener, objectEquality);
```

> 参数说明：

* watchExpression：监听的对象，它可以是一个 angular 表达式如 ’name’，或函数如 `function(){return $scope.name}`。

* listener：当 watchExpression 变化时，会被调用的函数或者表达式,它接收3个参数：

  * newValue(新值)
  * oldValue(旧值)
  * scope(作用域的引用)

* objectEquality：是否深度监听，如果设置为 true ，它告诉 Angular 检查所监控的对象中每一个属性的变化. 如果你希望监控数组的个别元素或者对象的属性而不是一个普通的值, 那么你应该使用它。（即是否使用 `angular.equals` 方法进行比较。）

``` html
<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="UTF-8">
		<script src="angular.min.js"></script>
	</head>
	<body>
		<div ng-controller="TestCtrl">
			<h1>Hello {{count}}</h1>
		</div>
		<script>
			var myApp = angular.module('myApp', []);
			myApp.controller('TestCtrl',['$scope', '$interval', function($scope, $interval) {
				$scope.count = 0;
				$interval(function() {
					$scope.count++;
				}, 500);
				$scope.$watch('count', function(newVal, oldVal) {
					console.log(newVal);
				})
			}]);
		</script>
	</body>
</html>
```

对于多个变量的监视变化，执行同一函数的话，可以将这几个变量转为字符串，以‘+’号隔开来进行监视：

``` js
//当count或page变化时，都会执行这个匿名函数
$scope.count=1;
$scope.page=1;
$scope.$watch('count + page',function(){
  //...
})
```

太多的 `$watch` 将会导致性能问题，`$watch` 如果不再使用，我们最好将其释放掉。

`$watch` 函数返回一个注销监听的函数（deregisterWatch()），如果我们想监控一个属性，然后在稍后注销它，可以使用下面的方式：

``` js
var watch = $scope.$watch('property', callback);

watch();
```

#### `$apply`
`$apply()` 函数可以从 Angular 框架的外部让表达式在 Angular 上下文内部执行（迫使模型和DOM被正确地更新）。它接受一个表达式作为唯一的参数，该表达式被求值并被返回；也就是说将它们引入到 Angular 控制器的范围中。

``` js
$scope.$apply(expression);
```

### `$digest()`
在控制器创建阶段，创建了一个 digest 循环来与浏览器的事件循环交互。digest 循环负责把对模型的更改更新到 DOM 元素，并且执行任何已注册的监视函数（`$scope.$digest()`）。

#### `$destroy()`
当一个作用域在视图中不再需要时，AngularJS 会自动调用 `$destroy()` 方法来从浏览器内存中删除该作用域。`$destory()` 方法停止 `$digest()` 调用并删除监视器，允许内存被浏览器垃圾收集器回收。

## 三、AngularJS生命周期
每次在浏览器中加载网页时，AngularJS应用程序的生命周期的3个阶段都会发生。

### 引导阶段
在AngularJS生命周期的第一阶段是引导阶段，它在AngularJS JavaScript库被下载到浏览器时发生。AngularJS初始化自身的必要的组件，然后初始化ng-app指令指向的你的模块。模块被加载，并且任何依赖关系都会被注入你的模块，并提供给模块中的代码使用。

### 编译阶段
AngularJS生命周期的第二阶段是HTML编译阶段。当编译阶段，静态DOM被替换为一个表示AngularJS视图的动态DOM。

遍历静态DOM并收集所有的指令，然后把指令链接到AngularJS内置库或自定义指令代码中相应的JavaScript公告呢上。指令与作用域相结合，产生动态或实时视图。

### 运行阶段
运行时阶段是AngularJS应用程序的最后一个阶段，它会一直存在，直到用户重新加载或离开网页为止。在这一阶段，在作用域中的任何更改都将反映到视图中，并且在视图中的任何更改也直接更新到作用域，使作用域成为视图数据的单一来源（双向数据绑定）。

## 四、过滤器
过滤器主要用来格式化数据（包括视图模板中的表达式、控制器中的数组或对象等）。

### 使用方法：
#### 在模板中使用过滤器
在HTML中的模板绑定符号{{}}内通过管道符|符号来调用过滤器：

``` js
{{ expression | filter }}
```
#### 在指令中使用过滤器
``` html
<li ng-repeat="name in username | filter "></li>
```

#### 带参数的过滤器
如果需要传递参数给过滤器，只需在过滤器名字后面加冒号即可。如果有多个参数，可以在每个参数后面都加入冒号：

``` js
{{ expression | filter:parameter1:parameter2 }}
```

#### 多个过滤器
多个过滤器间用|符号隔开：

``` js
{{ expression | filter1 | filter2 }}
```

### 内置过滤器
#### currency
currency过滤器可以将一个数值格式化为货币格式。

``` js
{{ 123 | currency }}  // $123.00

//还可以自己设置货币符号，默认会采用客户端所处区域的货币符号
{{ 123 | currency: '￥' }}  // ￥123.00
```

#### date
date过滤器可以将日期格式化成需要的格式。默认会采用mediumDate格式。

``` js
$scope.today = new Date();

//本地化的日期格式：
{{ today | date: 'medium' }}   // Feb 4, 2017 11:38:01 AM
{{ today | date:'short' }}  // 2/4/17 11:38 AM
{{ today | date:'fullDate' }}  // Saturday, February 4, 2017
{{ today | date:'longDate' }}  // February 4, 2017
{{ today | date:'mediumDate' }}  // Feb 4, 2017
{{ today | date:'shortDate' }}  // 2/4/17
{{ today | date:'mediumTime' }}  // 11:38:01 AM
{{ today | date:'shortTime' }}  // 11:38 AM

//年份格式化：
四位年份：{{ today | date:'yyyy' }}  // 2017
两位年份：{{ today | date:'yy' }}  // 17
一位年份：{{ today | date:'y' }}  // 2017

//月份格式化：
英文月份：{{ today | date:'MMMM' }}  // February
英文缩写：{{ today | date:'MMM' }}  // Feb
数字月份：{{ today | date:'MM' }}  // 02
一年中的第几个月份：{{ today | date:'M' }}  // 2

//日期格式化：
数字日期：{{ today | date:'dd' }}  // 04
一个月中的第几天：{{ today | date:'d' }}  // 4
英文星期：{{ today | date:'EEEE' }}  // Saturday
英文缩写：{{ today | date:'EEE' }}  // Sat

//小时格式化：
24小时制数字小时：{{ today | date:'HH' }}  // 00
一天中的第几个小时：{{ today | date:'H' }}  // 0
12小时制数字小时：{{ today | date:'hh' }}  // 12
上午或下午的第几个小时：{{ today | date:'h' }}  // 12

//分钟格式化：
数字分钟数：{{ today | date:'mm' }}  // 40
一个小时中的第几分钟：{{ today | date:'m' }}  // 40

//秒数格式化：
数字秒数：{{ today | date:'ss' }}  // 41
一分钟内的第几秒：{{ today | date:'s' }}  // 41
毫秒数：{{ today | date:'.sss' }}  // .440

//字符格式化：
上下午标识：{{ today | date:'a' }}  // AM
四位时区标识：{{ today | date:'Z' }}  // +0800

//自定义日期格式：
{{ today | date:'yyyy-MM-dd hh:mm:ss' }} 
```

#### number
number 过滤器将数字格式化。它的第二个参数是可选的，用来控制小数点后截取的位数。

``` js
{{ 12345678 | number }}    //  12,345,678
{{ 123.123 | number: 2 }}  // 123.12
```

> 注意：“number:0” 表示除掉小数后的数值，保留整数部分。

#### orderBy
orderBy 过滤器可以用表达式对指定的数组进行排序。

可以接受两个参数，第一个是必需的，第二个是可选的。

* 第一个参数是用来确定数组排序方向：

  * 函数：当第一个参数是函数时，该函数会被当作待排序对象的getter方法
  * 字符串：对这个字符串进行解析的结果将决定数组元素的排序方向。可以传入+或-来强制进行升序或降序排列。
  * 数组

* 第二个参数用来控制排序的方向（是否逆向）：true/false

``` js
{{ [{'name': 'tom'}, {'name': 'jon'}, {'name': 'ao'}] | orderBy: 'name' }}    

// [{"name":"ao"},{"name":"jon"},{"name":"tom"}]

{{ [{'name': 'tom'}, {'name': 'jon'}, {'name': 'ao'}] | orderBy: 'name': true }}

// [{"name":"tom"},{"name":"jon"},{"name":"ao"}]
```

#### limitTo
limitTo 过滤器会根据传入的参数生成一个新的数组或字符串，新的数组或字符串的长度取决于传入的参数，通过传入的参数的正负值来控制从前面还是从后面开始截取。

``` html
<p>{{ 'angular' | limitTo: 2 }}</p>    <!-- an -->
<p>{{ 'angular' | limitTo: -2 }}</p>   <!-- ar -->
<p>{{ [1, 2] | limitTo: 1 }}</p>       <!-- [1] -->
<p>{{ 'angular' | limitTo: 9 }}</p>    <!-- angular -->
```

> 注意：如果传入的长度值大于被操作数组或字符串的长度，那么整个数组或字符串都会被返回。

#### lowercase
lowercase 过滤器将字符串转为小写。

``` js
{{ 'Abc' | lowercase }}    // abc
```

#### uppercase
uppercase 过滤器用来将字符串转换成大写。

``` js
{{ 'Abc' | uppercase }}    //  ABC
```

#### json
json过滤器可以将一个JSON或JavaScript对象转换成字符串。

#### filter
filter过滤器可以从给定数组中选择一个子集，并将其生成一个新数组返回。

filter过滤器的第一参数可以是字符串、对象或是一个用来从数组中选择元素的函数。

* 字符串：返回所有包含这个字符串的元素。如果我们想返回不包含该字符串的元素，在参数前加!符号即可。


``` html
<div ng-controller="myController">
  {{ person.name | filter: 'o' }}    <!-- ["Tom","Bao"] -->
  {{ person.name | filter: '!o' }}   <!-- ["Lin"] -->
</div>

<script>
  myApp.controller('myController',['$scope',function($scope) {
    $scope.person = {
      name: ['Tom', 'Lin', 'Bao']
    };
  }]);
</script>
```

* 对象：AngularJS 会将待过滤对象的属性同这个对象中的同名属性进行比较。如果属性值是字符串就会判断是否包含该字符串。

``` html
<div ng-controller="myController">
  {{ person.intro | filter: {'sex': 'girl'} }}    <!-- [{"name":"Lin","sex":"girl"}] -->
</div>

<script>
  myApp.controller('myController',['$scope',function($scope) {
    $scope.person = {
      intro: [{
        'name': 'Tom',
        'sex': 'boy'
      },{
        'name': 'Lin',
        'sex': 'girl'
        },{
        'name': 'Bao',
        'sex': 'boy'
      }]
    };
  }]);
</script>
```

如果我们希望对全部属性都进行比较，可以将 `$` 当作键名。

``` js
{{ person.intro | filter: {'$': 'o'} }}  //  [{"name":"Tom","sex":"girl"},{"name":"Bao","sex":"boy"}]
```

* 函数：对每个元素都执行这个函数，返回由非假值组成的新数组。

``` html
<div ng-controller="myController">
  {{ num | filter: isBigger }}  <!-- [5, 8] -->
</div>

<script>
  myApp.controller('myController',['$scope',function($scope) {
    $scope.num = [1, 5, 8];
    $scope.isBigger = function(num) {
      return num > 4;
    };
  }]);
</script>
```

> 注意：当filter使用自定义函数时，必须传递一个参数。

### 自定义过滤器
除了内置过滤器外，我们还可以使用 `.filter()` 来自定义过滤器，它接受两个参数，第一个参数是过滤器名称，第二个是一个函数。

``` js
// 实现首字母大写
myApp.filter('capitalize', function() {
  return function(input) {
    if (input) {
      return input[0].toUpperCase() + input.slice(1);
    }
  }
});

{{ 'hello' | lowercase | capitalize }}    //  "Hello"
```

自定义带参数的过滤器:

``` js
myApp.filter('add', function() {
  return function(input, max) {
    var num = input + 1;
    return num > max ? num: 0;
  }
});

{{ 2 | lowercase | add: 4 }}    // 0

{{ 5 | lowercase | add: 4 }}   // 6
```

## 五、内置指令
AngularJS 指令是扩展 HTML 的属性，带有前缀 ng。通过内置的指令来为应用添加功能。也允许自定义指令。指令的引用方式一般是采用 `ng-` ，如 ng-app，如果想通过一些 html 浏览器的校验可以使用 `data-ng-app` 。
### ng-init
`ng-init` 指令初始化应用程序数据，也就是为 AngularJS 应用程序定义初始值，通常情况下，我们会使用一个控制器或模块来代替它。可以为多个变量赋初始值，变量之间用分号隔开。

``` html
<div ng-init="person={name:'angular'}">
  <p>{{ person.name }}</p>
</div>
```

### ng-hide
隐藏或显示 HTML 元素。

### ng-show
显示或隐藏 HTML 元素。

### ng-if
如果条件为 false 移除 HTML 元素。

### ng-repeat
用来遍历集合(数组)，给每个元素生成模板实例。

### ng-bind
``` js
<div ng-init="arr=[1, 'a']">
   <p><span ng-bind="arr[1]"></span></p>
</div>
```

HTML加载含有 `{{ }}` 语法的元素后并不会立刻渲染它们，这也是因此而导致未渲染内容闪烁。但是使用 `ng-bind` 可以避免这个问题，因为内容会被当做子文本节点渲染到含有 `ng-bind` 指令的元素内。

### ng-bindHtml
绑定 HTML 元素的 innerHTML 到应用程序数据，并移除 HTML 字符串中危险字符。

### ng-bindTemplate
规定要使用模板替换的文本内容。

### ng-cloak
当一个元素中含有此指令时，只有AngularJS模板已经完全编译，才会显示该元素。否则，显示元素的原始形式和模板代码。这也另一种处理闪烁问题的方法。

``` html
<div class="ng-cloak">{{1+2+3}}</div>
```

> 注意，如果 angular.js 文件放在底部引入，那么依旧会出现闪烁，解决办法是在 css 样式里添加以下代码：

``` css
.cloak {
  display:none;
}
```

### ng-model
绑定 HTML 控制器的值到应用数据。

### ng-include
在应用中包含 HTML 文件。

### ng-list
用来把作用域中的数组对象转换为分隔符分隔的字符串(逗号是默认的分隔符)。

### ng-href
为 `<a>` 元素指定链接。

### ng-src
指定 `<img>` 元素的 src 属性。

### ng-srcset
指定 `<img>` 元素的 srcset 属性。

### ng-disabled
规定一个元素是否被禁用。

### ng-transclude
用来把元素标志为装置点，供使用 transclude 选项来包装其他元素的指令使用。

### ng-view
把当前路由的已呈现的模板包含在主布局文件。

## 六、样式
### ng-class
指定 HTML 元素使用的 CSS 类。

### ng-classEven
类似 ng-class，但只在偶数行起作用。

### ng-classOdd
类似 ng-class，但只在奇数行起作用。

### ng-style
指定元素的 style 属性。

## 七、事件
### 常见事件指令
ngClick 、ngBlur 、ngFocus 、ngChange 、ngDblclick 、ngSubmit 、ngCopy（拷贝事件）、ngPaste（粘贴事件）、ngCut（剪切事件） 、ngKeydown 、ngKeypress 、ngKeyup 、ngMousedown 、ngMouseenter 、ngMouseleave 、ngMousemove 、ngMouseover 、ngMouseup 、ngSwipeLeft（左滑事件）、ngSwipeRight（右滑事件）

### 作用域事件
#### `$emit`
要把事件沿着作用域向上派送(从子作用域到父作用域)，我们要使用 `$emit()` 函数。

#### `$broadcast`
要把事件向下传递(从父作用域到子作用域)，我们使用 `$broadcast()` 函数。

#### `$on`
用来接收事件。

## 八、表单
### ng-form
指定 HTML 表单继承控制器表单。

### ng-switch
规定显示或隐藏子元素的条件。

### ng-options
在 `<select>` 列表中指定 `<options>` 。

### ng-value
规定 input 元素的值。

### ng-readonly
指定元素的 readonly 属性，定元素是否被选中。

### ng-required
设置必填。

### ng-selected
指定元素的 selected 属性中。

### ng-minlength
定义最小值。

### ng-maxlength
定义最大值。

### ng-pattern
定义正则匹配。

### ng-trueValue
用来设置选中复选框时的值。

### ng-falseValue
用来设置不选中时复选框的值。

## 九、自定义指令
directive()方法，接受两个参数：

* name：字符串型，用来定义这个指令的名字，指令名应该是驼峰命名风格
* function：函数应该返回一个对象。

``` html
<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="UTF-8">
		<script src="angular.min.js"></script>
	</head>
	<body>
		<div ng-controller="TestController">
			<div my-button></div>
		</div>
		<script>
			var myApp = angular.module('myApp', []);
			myApp.controller('TestController', ['$scope', function($scope) {	
			  //...
			}]);

			myApp.directive('myButton', function() {
				return {
					restrict: 'EA',
					template: '<button ng-click="click()">按钮</button>'
				};
			});
		</script>
	</body>
</html>
```

> 注意：如上例所示，定义指令命名为驼峰式的 myButton，而调用时则是 my-button 形式。

### 指令参数详解
#### restrict
可选，用来告诉AngularJS这个指令在DOM中以何种形式被声明。默认是A，即以属性的形式来进行声明。

``` html
<!-- 元素:restrict: 'E' -->
<my-directive></my-directive>

<!-- 属性:restrict: 'A' -->
<div my-directive></div>

<!-- 样式:restrict: 'C' -->
<div class="my-directive"></div>

<!-- 注释:restrict: 'M' -->
<--directive: my-directive expression-->
```

当然也可以混合使用，比如：“EAC”。一般是使用元素形式。

#### priority
priority 参数是数值型，用来定义指令的优先级。大多数指令会忽略这个参数，使用默认值0。

如果一个元素上具有两个优先级相同的指令，声明在前面的那个会被优先调用。如果其中一个优先级更高，则具有更高优先级的指令总是优先运行。

> 注：ngRepeat是优先级为1000，是所有内置指令中优先级最高的，它总是在其他指令之前运行。

#### terminal
terminal 参数是一个布尔型参数。这个参数用来告诉 AngularJS 停止运行当前元素上比本指令优先级低的指令。但同当前指令优先级相同的指令还是会被执行。

比如：ngIf 的优先级略高于 ngView。如果 ngIf 的表达式值为 true，ngView 就可以被正常运行，但如果 ngIf 表达式的值为 false，由于 ngView 的优先级低就不会被执行了。

#### template
模版字符串，值是字符串或函数。如果是一个函数，那么这个函数可以接受两个参数，参数为 tElement 和 tAttrs ，并返回一个代表模板的字符串。

如果模板字符串中含有多个DOM元素，或者由一个单独的文本节点构成，那它必须被包含在一个父元素内。也就是说，有且只有一个根DOM元素。

#### templateUrl
外部HTML文件路径。

> 注意：template和templateUrl只能选择一个。

#### replace
可选，布尔型类型。默认为 false，表示模版会被当作子元素插入到调用此指令的元素内部。设置为 true，用模板替换当前标签。。

``` html
<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="UTF-8">
		<script src="angular.min.js"></script>
	</head>
	<body>
		<div ng-controller="TestController">
			<div my-button></div>
		</div>
		<script>
			var myApp = angular.module('myApp', []);
			myApp.controller('TestController', ['$scope', function($scope) {		
			  //...
			}]);

			myApp.directive('myButton', function() {
				return {
					restrict: 'EA',
					replace: true,
					template: '<button ng-click="click()">按钮</button>'
				};
			});
		</script>
	</body>
</html>
```
具体DOM结构如下所示：

``` html
<!-- replace:true -->
<div ng-controller="TestController" >
  <button ng-click="click()" my-button>按钮</button>
</div>

<!-- replace:false -->
<div ng-controller="TestController" >
  <div my-button>
    <button ng-click="click()" my-button>按钮</button>
  </div>
</div>
```

#### scope
可选，可以设置为 true、false 或一个对象，默认是 false ，一般会设置为一个空对象来创建隔离作用域。

* false，继承父作用域（默认值），父能影响子，子也能影响父;
* true，继承父作用域，并且创建自己的作用域（子作用域），父能影响子，子不能影响父;
* {}，创建一个全新的隔离作用域，父作用域和子作用域是互不影响的。

``` html
<!DOCTYPE html>
<html ng-app="myApp">
	<head>
		<meta charset="UTF-8">
		<script src="angular.min.js"></script>
	</head>
	<body>
		<div ng-controller="TestController">
			<p>name的值：{{ name }}</p>
			<p><button ng-click="tclick()">A按钮</button></p>
			<div my-button></div>
		</div>
		<script>
			var myApp = angular.module('myApp', []);
			myApp.controller('TestController', ['$scope',function($scope) {
				$scope.name = 'A';
				$scope.tclick = function() {
					$scope.name = 'A1';
				};
				$scope.$watch('name',function(newVal, oldVal) {
					console.log(newVal);
				});
			}]);

			myApp.directive('myButton',function() {
				return {
					restrict: 'EAC',
					template: '<button ng-click="click()">B按钮</button>',
					scope: false,
					controller: function($scope) {
						$scope.click = function() {
							$scope.name = 'B1';
						};
						$scope.$watch('name',function(newVal, oldVal) {
							console.log(newVal);
						});
					}
				};
			});
		</script>
	</body>
</html>
```

修改 scope 的值，然后观察控制台的输出。

##### 绑定策略
在为指令创建隔离作用域的同时，还能访问到父级中的属性。AngularJS 提供了三种不同的绑定策略：

* 单向绑定：@父域的值，传递的值必须是字符串且在指令里引用时必须加上{{}}：

``` js
scope: {
  name: '@myName'
}
```

在元素中使用属性：

``` html
<div my-directive my-name="{{ name }}"></div>
```

当表达式的值发生改变时，my-name 参数也跟着改变。隔离scope中的 name 属性的值也相应地被改变。

如果 scop 的属性与标签属性一样，也可以采取以下的简写：

``` html
<div my-directive name="{{ name }}"></div>

scope: {
  name: '@`
}
```

* 双向绑定：通过 = 可以将本地作用域上的属性同父级作用域上的属性进行双向的数据绑定。也就是说，本地属性会反映出父数据模型中所发生的改变，父数据模型的变化也会影响本地属性。

``` html
<div my-directive age="age"></div>

scope: {
  age: '=',
}
```
> 注意，数据的双向绑定要通过 = 前缀标识符实现，所以不可以使用 `{{}}`。

* 父级作用域绑定：通过 & 来调用父级作用域的方法（单向）。

``` html
<div my-directive change-age="changeAge()"></div>

scope: {
  changeAge: '&changeAge'
}
```
实例：

``` html
<!DOCTYPE html>
<html ng-app="myApp">
  <head>
    <meta charset="UTF-8">
    <script src="angular.min.js"></script>
  </head>
  <body>
    <div ng-controller="TestController">
      {{ age }}
      {{ name }}
      <div my-directive my-name="{{name}}" age="age" change-age="changeAge()"></div>
    </div>
    <script>
      var myApp = angular.module('myApp', []);
      myApp.controller('TestController', ['$scope', function($scope) {
        $scope.name = 'Tom';
        $scope.age = 10;
        $scope.changeAge = function() {
          $scope.age = 12;
        };
      }]);

      myApp.directive('myDirective', function() {
        return {
          restrict: 'EA',
          template: '<div class="my-directive">' +
            '<p><span ng-bind="name"></span></p>' +
            '<p><span ng-bind="age"></span></p>' +
            '<p><input ng-model="name"></p>' +
            '<p><button ng-click="changeAge()">改变年龄</button></p>' +
            '</div>',
          scope: {
            name: '@myName',
            age: '=',
            changeAge: '&changeAge'
          },
          controller: function($scope) {  
            //...
          }
        };
      });
    </script>
  </body>
</html>
```

#### transclude
transclude 属性用来开启内嵌机制，是否将当前元素的内容转移到模板中，默认是 false。
要开启嵌入机制，就要配合 ng-transclude 内置指令来使用。

``` js
transclude:true,template:'<div>hello,template <span ng-transclude></span></div>'
```


> 注意：在一个指令的模板中，只能声明一次ng-transclude
#### controller
controller 参数可以是一个字符串或一个函数。

* 当设置为字符串时，会以字符串的值为名字，来查找注册在应用中的控制器的构造函数。

* 为函数时：

``` js
controller: function($scope, $element, $attrs, $transclude) {    //...}
```
* 
  * `$scope`：与指令元素相关联的当前作用域

  * `$element`：当前指令对应的元素

  * `$attrs`：由当前元素的属性组成的对象。
  
  * `$transclude`：嵌入链接函数会与对应的嵌入作用域进行预绑定。

> 用 controller 的场景是该指令（a）会被其他指令（b）require 的时候，在 b 的指令里可以传入 a 的这个 controller，目的是为了指令间的复用和交流。
> `$transclude` 嵌入链接函数，实际被执行用来克隆元素和操作 DOM中 的函数（除非是用来定义一些可复用的行为，否则一般不推荐在这使用）。

#### controllerAs
用来设置控制器的别名，刻意以此名来发布控制器，并且作用域可以访问 controllerAs。

#### require
require 是指令与指令之间通讯的桥梁，指定需要依赖的其他指令，可以是字符串或者数组：

* 字符串代表另一个指令的名字，它将作为link函数的第四个参数。

* 数组：指定多个指令的名称（一般还会使用 controllerAs 注册一个别名，然后调用，比如 `controllerAs: ctrList`，使用是这样：`ctrList[0]` ）require 策略：

* 没有前缀：指令会在自身提供的控制器中进行查找，如果找不到，则会抛出一个 error；

* ？：在自身指令寻找，如果没有找到，link() 函数第4个参数为 null ；

* ^ ：在自身指令寻找指令名称的同时，向上父元素寻找；

* ^?：如果在当前和父元素中都没有找到控制器，则会将 null 传给 link。

``` html
<!DOCTYPE html>
<html ng-app="myApp">
  <head>
    <meta charset="UTF-8">
    <script src="angular.min.js"></script>
  </head>
  <body>
    <div ng-controller="TestController">
      <accordion>
        {{ count }}
        <my-directive></my-directive>
      </accordion>
    </div>
    <script>
      var myApp = angular.module('myApp', []);
      myApp.controller('TestController', ['$scope', function($scope) {
      }]);
      
      myApp.directive('accordion', function() {
          return {
            restrict: 'E',
            controller: function($scope) {
              $scope.count = 0;
              this.add = function() {
                $scope.$apply(function() {
                  $scope.count++;
                });
              }
            }
          }
        })
        .directive('myDirective', function() {
          return {
            restrict: 'E',
            template: '<button id="btn">点击</button>',
            require: '^accordion',
            link: function($scope, iElem, iAttrs, ctrl) {
              angular.element(document.getElementById('btn')).on('click', ctrl.add);
            }
          };
        });
    </script>
  </body>
</html>
```

#### link
链接 函数负责注册 DOM 事件和更新 DOM。它是在模板被克隆之后执行的，它也是大部分指令逻辑代码编写的地方。

使用 link() 函数创建可以操作DOM的指令，所以，如果你想在自定义指令里面修改DOM，就应该使用 link() 函数。

如果定义了编译函数，它会返回链接函数；如果两个都定义了，编译函数会重载链接函数，所以一般都不会同时定义。

``` js
link: function(scope, element, attrs[, controller]) {
  //...
}
```

* scope：指令内部的作用域;

* element：指令将被插入的元素;

* attrs：元素上的属性组成的列表，可以在所有指令的链接函数间共享。会以JavaScript 对象的形式进行传递;

* controller：由 require 选项定义的控制器。如果没有设置 require，那么值为 undefined 。控制器在所有的指令间共享，因此指令可以将控制器当作通信通道（公共API）。如果设置了多个 require ，那么这个参数会是一个由控制器实例组成的数组。

> 注意：controller 是为了指令间的复用和交流，而 link 只能在指令内部中定义行为。

#### compile
编译函数是用来处理需要修改模板DOM的情况的。因为大部分指令都不需要修改模板，所以这个函数也不常用。需要用到的例子有 ngRepeat ，这个是需要修改模板的，还有 ngView 这个是需要异步载入内容的。

compile参数可以返回一个对象或函数：

``` js
compile: function (tElement, tAttrs, transclude) {
  return {
    pre: function(scope, iElement, iAttrs, controller) { ... },
    post: function(scope, iElement, iAttrs, controller) { ... }
  }
  // 或者
   return function postLink(...) { ... }
}
```

编译函数可以返回一个对象或者函数。

* 返回函数 - 等效于在编译函数不存在时，使用配置对象的link属性注册的链接函数。

* 返回对象 - 返回一个通过pre或post属性注册了函数的对象。

> 注意：在编译函数里面不要进行任何DOM变形之外的操作。 更重要的，DOM监听事件的注册应该在链接函数中做，而不是编译函数中。

compile和link两者区别：

* compile 是对指令的模板进行转换，link 是在模型和视图之间建立关联（包括注册事件监听）;

* compile 对同一个指令的多个实例只会执行一次，link 对于指令的每个实例都会执行一次;

* compile 和 link 是互斥的，编写了 compile，自定义的 link 将无效;

* 一般情况下只需要编写 link。

## run()函数
AngularJS在注入器创建后会执行这个函数。

案例：比如我们需要在每次路由发生变化时，都执行一个函数来验证用户是否登录，放置这个功能唯一合理的地方就是run方法；

``` js
myApp.service('authService',
function() {
  var isLoggined = false;
  return {
    isUserLogined: function() {
      isLoggined = !isLoggined;
      return isLoggined;
    }
  }
});

// run配置块
myApp.run(['$rootScope', '$location', 'authService',
function($rootScope, $location, authService) {
  $rootScope.$on('$routeChangeStart',
  function(event, next, current) {
    // 如果用户未登录
    if (authService.isUserLogined()) {
      if (next.templateUrl === 'login.html') {
        // 已经是登录页面，因此无需再重定向
      } else {
        $location.path('/login');
      }
    } else {
      $location.path('/main');
    }
  });
}]);
```

在上面的代码中，我们创建了一个服务authService，用来放置用户权限相关方法，然后注入到控制器中，再监听路由的变化之前的事件，接着判断用户是否已登录，决定跳转到登录页面还是不跳转。