放在任务队列里的异步任务基本上都是 io 操作,需要从用户/网络获得信息这种,为了避免等待输入时候浪费 cpu 资源,放入任务队列直到得到输入之后才进入主线程执行

[JavaScript 运行机制详解：再谈Event Loop - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2014/10/event-loop.html)

[简介：回调](https://zh.javascript.info/callbacks)
[[js回调]]写的很好

异步函数大多都会通过「回调」的形式来执行后续的操作, 如 http 请求, 先进行请求动作, 再挂载一个回调函数, 当请求完成时, 执行该回调函数(如展示用户信息…)

回调函数指通过参数将函数传递到其它代码, 为某一块可执行代码的引用

#javascript 
#前端 