[Async/await](https://zh.javascript.info/async-await)
在函数前面的 “async” 这个单词表达了一个简单的事情：即这个函数总是返回一个 promise。其他值将自动被包装在一个 resolved 的 promise 中。

关键字 `await` 让 JavaScript 引擎等待直到 promise 完成（settle）并返回结果。

当我们需要同时等待多个 promise 时，我们可以用 `Promise.all` 把它们包装起来，然后使用 `await`

#javascript 
#前端 