# async 详解
async 函数可以让你写出读起来像是同步代码的，但实际基于 Promise 的代码。一旦你使用了 async 关键字来定义函数，你就可以在函数体内使用 await 关键字。async 函数被调用时，它会返回一个 Promise。当 async 函数体中返回一个值时，该 Promise 完成（fulfilled）。当 async 函数抛出错误时，该 Promise 失败（rejected）。

await 关键字可以用来等待一个 Promise 完成并且返回结果值。如果传给 await 关键字的值不是一个 Promise，它会将其转换成一个已完成的 Promise。