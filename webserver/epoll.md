epoll是Linux特有的I/O复用函数。

首先，epoll使用一组函数来完成任务，而不是单个 函数。
其次，epoll把用户关心的文件描述符上的事件放在内核里的一 个事件表中，从而无须像select和poll那样每次调用都要重复传入文件 描述符集或事件集。

但epoll需要使用一个额外的文件描述符，来唯一 标识内核中的这个事件表。这个文件描述符使用如下epoll_create函数来创建

LT:level trigger 检测到门口有人门铃就一直响着，epoll默认工作模式

ET:edge trigger 效率比LT高，门口人来了，人走了这种时候才响