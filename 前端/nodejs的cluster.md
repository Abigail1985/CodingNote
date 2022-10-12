 在如今机器的 CPU 都是多核的背景下，Node 的单线程设计已经没法更充分的" 压榨" 机器性能了。所以从 v0.8 开始，Node 新增了一个内置模块——“cluster”，故名思议，它可以通过一个父进程管理一坨子进程的方式来实现集群的功能

[解读 Node.js 的 cluster 模块 | AlloyTeam](http://www.alloyteam.com/2015/08/nodejs-cluster-tutorial/)