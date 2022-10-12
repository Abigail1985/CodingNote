熟悉Linux操作系统的同学对于ip addr命令应该不陌生，知道它是用来查看本地IP地址的，除了IP地址，其它额外的信息有必要了解一下。

root@test:~# ip addr  
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default   
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00  
inet 127.0.0.1/8 scope host lo  
valid_lft forever preferred_lft forever  
inet6 ::1/128 scope host   
valid_lft forever preferred_lft forever  
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000  
link/ether fa:16:3e:c7:79:75 brd ff:ff:ff:ff:ff:ff  
inet 10.10.11.92/24 brd 10.100.122.255 scope global eth0  
valid_lft forever preferred_lft forever  
inet6 fe80::f816:3eff:fec7:7975/64 scope link   
valid_lft forever preferred_lft forever

我的机器上命令结果显示两部分，lo和eth0，如果有多块网卡，每块网卡都会显示一部分。

1. lo：全称loopback，是回环地址，经常被分配到127.0.0.1地址上，用于本机通信，经过内核处理后直接返回，不会在任何网络中出现。

2.eth0：网卡名，如果有多块网卡，会有多个eth 或其它名称。

   link/ether：这个是MAC地址，唯一的，一块网卡一个MAC。

   inet：网卡上绑定的IP地址，通常所说的IPV4，一块网卡可以绑定多个IP地址。在绑定IP地址时注意：windows主机会提示IP地址冲突，而linux主机无任何提示，在添加新的IP地址时务必检测一下新地址是否和原有地址冲突，避免造成访问不可用。常用检测命令：ping或arping IP；

   inet6：IPV6地址，暂时没有，预留。

3.网络设备状态标识：<BROADCAST,MULTICAST,UP,LOWER_UP>

   UP：网卡处于启动状态。

   BROADCAST：网卡有广播地址，可以发生广播包。

   MULTICAST：网卡可以发生多播包。

   LOWER_UP：L1是启动的，即网线是插着的。
   
-   eno1 is your embedded NIC (onboard **N**etwork **I**nterface **C**ard). It is a regular physical network interface.
-   lo is your loopback [interface](http://www.tldp.org/LDP/nag/node66.html)
- `lo` is a loopback device. You can imagine it as a virtual network device that is on all systems, even if they aren't connected to any network. It has an IP address of `127.0.0.1` and can be used to access network services locally. For example, if you run a webserver on your machine and browse to it with Firefox or Chromium on the same machine, then it will go via this network device.

## mut是什么
最大传输单元（Maximum Transmission Unit，MTU）用来通知对方所能接受[数据服务](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E6%9C%8D%E5%8A%A1/23724818?fromModule=lemma_inlink)[单元](https://baike.baidu.com/item/%E5%8D%95%E5%85%83/32922?fromModule=lemma_inlink)的最大尺寸，说明发送方能够接受的[有效载荷](https://baike.baidu.com/item/%E6%9C%89%E6%95%88%E8%BD%BD%E8%8D%B7/3653893?fromModule=lemma_inlink)大小
[以太网](https://baike.baidu.com/item/%E4%BB%A5%E5%A4%AA%E7%BD%91/99684?fromModule=lemma_inlink)和802.3对数据帧的长度都有一个限制，其最大值分别是1500[字节](https://baike.baidu.com/item/%E5%AD%97%E8%8A%82/1096318?fromModule=lemma_inlink)和1492字节。链路层的这个特性称为MTU，即最大传输单元。不同类型网络的数帧长度大多数都有一个上限。如果IP层有一个数据包要传，而且数据帧的长度比链路层的MTU还大，那么IP层就需要进行[分片](https://baike.baidu.com/item/%E5%88%86%E7%89%87/13677994?fromModule=lemma_inlink)( fragmentation)，即把数据包分成干片，这样每一片就都小于MTU。 [3] 

当同一个网络上的两台主机互相进行通信时，该网络的MTU是非常重要。但是如果两台主机之间的通信要通过多个网络，每个网络的链路层可能有不同的MTU，那么这时重要的不是两台主机所在网络的MTU的值，而是两台主机通信路径中的最小MTU，称为路径MTU( Path mtu，PMTU)。 [3] 

两台主机之间的PMTU不一定是个常数，它取决于当时所选择的路径，而且[路由](https://baike.baidu.com/item/%E8%B7%AF%E7%94%B1/363497?fromModule=lemma_inlink)选择也不一定是对称的(从A到B的路由可能与从B到A的路由不同)，因此，[PMTU](https://baike.baidu.com/item/PMTU/1963207?fromModule=lemma_inlink)在两个方向上不一定是一致的。 [3] 

RFC1191描述了PMTU的发现机制，即确定路径MTU的方法。ICMP的不可到达错误采用的就是这种方法， traceroute程序也是用这种方法来确定到达目的节点的PMT的

#网络