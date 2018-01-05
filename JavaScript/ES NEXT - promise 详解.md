## Promises 详解
Promise 的出现，原本是为了解决回调地狱的问题。它允许你为异步代码执行结果的成功和失败分别绑定相应的处理方法。这让异步方法可以像同步方法那样返回值，但是并非立即返回执行的结果，而是返回一个 Promise 对象。

### 如何创建Promises
和普通 JavaScript 对象一样，同样是通过`new`关键词来创建一个 Promise 对象实例。构造函数只接收一个参数，且该参数必须是一个函数，任何其他的值比如 undefined、null、true 等都会报一个 TypeError 的错误。同样，如果你没有通过`new`关键词创建，而是直接执行`Promise()`，同样也会报一个 TypeError 的错误。

``` js
var promise = new Promise(function(resolve, reject) {
    // 进行一些异步操作
    // 然后调用resolve或reject方法
});
```

创建Promise实例时传入的函数，同时还接受两个参数，它们分别对应Promise内部实现的两个方法。

### Promise的原理分析
Promise 本质是一个状态机。每个 promise 只能是 3 种状态中的一种：

* pending：初始状态；
* fulfilled：操作成功，状态不可变，且需要有一个最终值；
* rejected：操作失败，状态不可变，且需要有一个拒绝原因。

pending 是对象创建后的初始状态，当对象操作成功时变为 fulfilled，当对象 操作失败时变为 rejected。状态转变只能是 pending -> fulfilled 或者 pending -> rejected，状态转变不可逆。

### Promise实例方法介绍
Promise 对象拥有两个实例方法`then()`和`catch()`。 

#### Promise.prototype.then(resolve, reject) 
一个promise必须提供一个 then 方法，通过初始化返回的 promise 实例对象的 then 方法添加之后的异步操作，如果是 resolve 会进入成功回调，如果是 reject 会进入失败回调：

``` js
var promise = new Promise(function(resolve, reject) {
    //业务逻辑
    resolve("success");
});
promise.then(function(result) {
    console.log(result);
    return result;
}).then(function() {
   console.log("resolve");
});

// 当异步操作失败之后，会进入失败回调
new Promise(function(resolve, reject) {
    reject(new Error("ddd"));
}).then(function(v) {
    console.log(v)//不会被调用
}, function(e) {
    console.log(e)
});
```

从上面这个例子可以看出 then() 接收两个参数，且通常都是函数。第二个参数是可选的，不一定要提供。当 promise 状态 fulfill 时，会把 resolve(value) 中的 value 值传给调用的函数中，同理，当 promise 状态 reject 时，会把 reject(reason) 中的 reason 值传给调用的函数。

##### 注意：
* then 方法可以被同一个 promise 调用多次；
* then 方法返回一个新的 promise 。

``` js
var p = new Promise(function(resolve, reject){
    resolve(5)
}).then(function(value){
    console.log(value) //5
})

var p1 = new Promise(function(resolve, reject){
    reject(new Error('错误'))
}).then(function(value){
    console.log(value)
}, function(reason){
    console.log(reason) //Error: 错误(…)
})
```

ES6 已经支持 Promise：

``` js
var promise = new Promise((resolve, reject) => {
  setTimeout(function() {
    resolve('完成');
  }, 10);
});

promise.then((msg) => {
  console.log('first messaeg: ' + msg); //first messaeg:完成
})
```

深入理解 then 方法：

``` js
var promise = new Promise((resolve, reject) => {
  setTimeout(function() {
    resolve('完成');
  }, 10);
});

promise.then((msg) => {
  console.log('first messaeg: ' + msg); // first messaeg: 完成
}).then((msg) => {
  console.log('second messaeg: ' + msg); // second messaeg:undefined
});
```

输出两行信息：我们发现第二个then方法接收到的最终值是undefined，为什么呢？这是调用第一个then方法后，返回promise最终值为undefined，传递给第二个then的回调，如果把上面的例子稍加改动：

``` js
var promise = new Promise((resolve, reject) => {
  setTimeout(function() {
    resolve('完成');
  }, 10);
});

promise.then((msg) => {
  console.log('first messaeg: ' + msg); // first messaeg: 完成
  return msg + '第二次';
}).then((msg) => {
  console.log('second messaeg: ' + msg); // second messaeg:完成第二次
});
```

这次两个then方法的回调都接收到了最终值，正如我们前文所说，’then’方法返回一个新promise，并且该新promise根据其传入的回调执行的返回值，进行决议，而函数未明确return返回值时，默认返回的是undefined，这也是上面实例第二个then方法的回调接收undefined参数的原因。

这里使用了链式调用，我们需要明确：共产生三个promise，初始promise，两个then方法分别返回一个promise；而第一个then方法返回的新promise是第二个then方法的主体，而不是初始promise。

#### Promise.prototype.catch(onRejected)
该方法为promise添加拒绝回调函数，将返回一个新promise，该新promise根据回调函数执行的返回值进行决议；若promise决议为完成状态，则新promise根据其最终值进行决议。

``` js
var promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('failed');
  }, 0);
});

var promise2 = promise.catch((reason) => {
  console.log(reason);
  return 'successed';
});

var promise3 = promise.catch((reason) => {
  console.log(reason);
});

var promise4 = promise.catch((reason) => {
  console.log(reason);
  throw 'failed 2';
});
```

输出如下图：
![catch](mdImgs/promise-catch.png)

* catch会为promise注册拒绝回调函数，一旦异步操作结束，调用了reject回调函数，则依次执行注册的拒绝回调；

* 另外有一点和then方法相似，catch方法返回的新promise将使用其回调函数执行的返回值进行决议，如promise2,promise3状态均为完成（resolved），但是promise3最终值为undefined，而promise2最终值为successed，这是因为在调用promise.catch方法时，传入的回调没有显式的设置返回值；

* 对于promise4，由于调用catch方法时，回调中throw抛出异常，所以promise4状态为拒绝（rejected），拒绝原因为抛出的异常；

* 特别需要注意的是这里一共有四个promise，一旦决议，它们之间都是独立的，我们需要明白无论是then方法，还是catch方法，都会返回一个新promise，此新promise与初始promise相互独立。

* catch方法和then方法的第二个参数一样，都是为promise注册拒绝回调。

### Promise静态方法
#### Promise.resolve(value)
返回一个以给定值解析后的 Promise 对象。但如果这个值是个 thenable（即带有then方法），返回的 promise 会“跟随”这个 thenable 的对象，采用它的最终状态（指resolved/rejected/pending/settled）；否则以该值为成功状态返回 promise 对象。

#### Promise.reject(reason)
返回一个用 reason 拒绝的 Promise。

#### Promise.all(iterable) 
all 方法接受一个或多个 promsie（以数组方式传递），返回一个新 promise，该 promise 状态取决于传入的参数中的所有 promsie 的状态：

* 当所有 promise 都完成是，返回的 promise 完成，其最终值为由所有完成 promsie 的最终值组成的数组；
* 当某一 promise 被拒绝时，则返回的 promise 被拒绝，其拒绝原因为第一个被拒绝 promise 的拒绝原因；

``` js
var p1 = new Promise((resolve, reject) => {
  setTimeout(function(){
    console.log('p1决议');
    resolve('p1');
  }, 10);
});

var p2 = new Promise((resolve, reject) => {
  setTimeout(function(){
    console.log('p2决议');
    resolve('p2');
  }, 10);
});

Promise.all( [p1, p2] )
  .then((msgs) => {
    // p1和p2完成并传入最终值
    console.log(JSON.stringify(msgs)); // ["p1","p2"]
  })
  .then((msg) => {
    console.log( msg );  // undefined
  });
```

#### Promise.race(iterable) 
race方法返回一个promise,只要传入的诸多promise中的某一个完成或被拒绝，则该promise同样完成或被拒绝，最终值或拒绝原因也与之相同。

### 例子1：使用 promise 封装 ajax：

``` js
getData(method, url){
  var promise = new Promise(function(resolve, reject){
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.open(method, url);
    xmlHttp.send();
    xmlHttp.onload = function () {
      if (this.status == 200 ) {
        resolve(this.response);
      } else {
        reject(this.statusText);
      }
    };
    xmlHttp.onerror = function () {
      reject(this.statusText);
    };
  })
  return promise;
}

getData('get','www.xxx.com').then(successFun, failFun)
```