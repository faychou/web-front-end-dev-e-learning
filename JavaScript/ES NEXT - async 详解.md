# async 详解
ES7 提出的 async 函数，终于让 JavaScript 对于异步操作有了终极解决方案。async 函数是 Generator 函数的语法糖。使用 关键字 async 来表示，在函数内部使用 await 来表示异步。async 函数可以让你写出读起来像是同步代码的，但实际是基于 Promise 的代码，async 函数被调用时，会返回一个 Promise。当 async 函数体中返回一个值时，该 Promise 完成（fulfilled）。当 async 函数抛出错误时，该 Promise 失败（rejected）。

await 关键字可以用来等待一个 Promise 完成并且返回结果值。如果传给 await 关键字的值不是一个 Promise，它会将其转换成一个已完成的 Promise。

## 语法
async 函数返回一个 Promise 对象，函数内部 return 返回的值，会成为 then 方法回调函数的参数。

``` js
async function  f() {
  return 'hello world'
};
f().then( (v) => console.log(v))  // hello world
```

如果 async 函数内部抛出异常，则 Promise 对象变为 reject 状态，抛出的错误会被 catch 方法回调函数接收到。

``` js
async function e() {
  throw new Error('error');
}
e().then(v => console.log(v))
.catch( e => console.log(e));
```

### await
await 关键字接收一个 Promise，等待代码执行，直到 Promise 状态变为 resolved 或者 rejected，这种特性能让我们的异步代码阅读起来更像是同步代码。

async 函数返回的 Promise 对象，必须等到内部所有的 await 命令的 Promise 对象执行完，才会发生状态改变。也就是说，只有当 async 函数内部的异步操作都执行完，才会执行 then 方法的回调。

``` js
const delay = timeout => new Promise(resolve=> setTimeout(resolve, timeout));
async function f(){
  await delay(1000);
  await delay(2000);
  await delay(3000);
  return 'done';
}

f().then(v => console.log(v)); // 等待6s后才输出 'done'
```

正常情况下，await 命令后面跟着的是 Promise ，如果不是的话，也会被转换成一个 立即 resolve 的 Promise。

``` js
async function  f() {
  return await 1
};
f().then( (v) => console.log(v)) // 1
```

## Async 函数的错误处理
``` js
let a;
async function f() {
  await Promise.reject('error');
  a = await 1; // 这段 await 并没有执行
}
f().then(v => console.log(a));
```

如上面所示，当 async 函数中只要一个 await 出现 reject 状态，则后面的 await 都不会被执行。

### 解决办法：可以添加 try/catch。
``` js
// 正确的写法
let a;
async function correct() {
  try {
    await Promise.reject('error')
  } catch (error) {
    console.log(error);
  }
  a = await 1;
  return a;
}

correct().then(v => console.log(a)); // 1
```

如果有多个 await 则可以将其都放在 try/catch 中。

## Async 与其他异步操作的对比
``` js
function fetchUser() { 
  return fetch('https://api.github.com/users/superman66')
  .then((data) => {
    resolve(data.json());
  }, (error) => {
    reject(error);
  });
}
```

``` js
// Promise 方式
function getUserByPromise() {
  fetchUser()
    .then((data) => {
      console.log(data);
    }, (error) => {
      console.log(error);
    })
}
getUserByPromise();
```

Promise 的方式虽然解决了 callback hell，但是这种方式充满了 Promise的 then() 方法，如果处理流程复杂的话，整段代码将充满 then。语义化不明显，代码流程不能很好的表示执行流程。

``` js
// Generator 方式
function* fetchUserByGenerator() {
  const user = yield fetchUser();
  return user;
}

const g = fetchUserByGenerator();
const result = g.next().value;
result.then((v) => {
  console.log(v);
}, (error) => {
  console.log(error);
})
```

Generator 的方式解决了 Promise 的一些问题，流程更加直观、语义化。但是 Generator 的问题在于，函数的执行需要依靠执行器，每次都需要通过 g.next() 的方式去执行。

``` js
// async 方式
async function getUserByAsync(){
  let user = await fetchUser();
  return user;
}
getUserByAsync()
.then(v => console.log(v));
```

async 函数完美的解决了上面两种方式的问题。流程清晰，直观、语义明显。操作异步流程就如同操作同步流程。同时 async 函数自带执行器，执行的时候无需手动加载。
