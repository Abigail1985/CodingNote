
## 三、安装多个 GCC 版本

这一节提供一些指令，关于如何在 Ubuntu 20.04 上安装和使用多个版本的 GCC。更新的 GCC 编译器包含一些新函数以及优化改进。  
在写作本文的时候，Ubuntu 源仓库包含几个 GCC 版本，从`7.x.x`到`10.x.x`。在写作的同时，最新的版本是`10.1.0`。  
在下面的例子中，我们将会安装最新的三个版本 GCC 和 G++：  
输入下面的命令，安装想要的 GCC 和 G++ ：

```
sudo apt install gcc-8 g++-8 gcc-9 g++-9 gcc-10 g++-10
```

下面的命令配置每一个版本，并且设置了优先级。默认的版本是拥有最高优先级的那个，在我们的场景中是`gcc-10`。

```
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 100 --slave /usr/bin/g++ g++ /usr/bin/g++-10 --slave /usr/bin/gcov gcov /usr/bin/gcov-10
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 90 --slave /usr/bin/g++ g++ /usr/bin/g++-9 --slave /usr/bin/gcov gcov /usr/bin/gcov-9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 80 --slave /usr/bin/g++ g++ /usr/bin/g++-8 --slave /usr/bin/gcov gcov /usr/bin/gcov-8
```

以后，如果你想修改默认的版本，使用`update-alternatives`命令：

```
sudo update-alternatives --config gcc
```

输出：

```
There are 3 choices for the alternative gcc (providing /usr/bin/gcc).
  Selection    Path            Priority   Status
------------------------------------------------------------
* 0            /usr/bin/gcc-10   100       auto mode
  1            /usr/bin/gcc-10   100       manual mode
  2            /usr/bin/gcc-8    80        manual mode
  3            /usr/bin/gcc-9    90        manual mode
Press <enter> to keep the current choice[*], or type selection number:
```

你将会被展示一系列已经安装在你的 Ubuntu 系统上的 GCC 版本。输入你想设置为默认的 GCC 版本，并且按回车`Enter`。  
这个命令将会创建符号链接到指定版本的 GCC 和 G++。