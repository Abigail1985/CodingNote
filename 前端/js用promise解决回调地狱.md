[Promise](https://zh.javascript.info/promise-basics)

      

-   Promise 翻译过来就是承诺, 这个承诺会在未来有一个确切的答复

-   pending - 等待中
-   resolved - 已完成
-   rejected - 已拒绝(失败)

-   Promise执行之后会直接进入 pending 状态, 根据异步操作的结果来决定该 Promise 会 reject 还是 resolve。特别的, Promise 的状态只能改变一次, 即当其从 pending 切换到任一状态之后, 其状态将不再发生改变…
-   链式调用

-   promise.then.then….