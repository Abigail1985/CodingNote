
# 1.安装mpi

[基于docker的MPI安装_docker mpi_pppp_k的博客-CSDN博客](https://blog.csdn.net/weixin_50674454/article/details/124770487)

# 2.建立ssh免密通信
制作一个docker网段
```
docker network create --subnet=192.168.10.0/16 network_mpi 
docker network ls
```

这里有一个大坑就是 192.168.10.0/16这个网段和docker默认网段192.168.0.0./24冲突了。一建这个字网就会导致docker连不上网报错

Error response from daemon: Get "https://registry-1.docker.io/v2/": proxyconnect tcp: dial tcp: lookup http.docker.internal on 192.168.65.7:53: read udp 192.168.0.1:42789->192.168.65.7:53: i/o timeout

然后容器里也无法apt-get

最后改成了192.161.10.0网段
 

如第三节创建三个节点，

在每个节点`ssh-keygen -t rsa`

先将所有节点的公钥发到一个节点中
```
#把node5的公钥发给node4
#node5
scp ~/.ssh/id_rsa.pub root@node4:~/.ssh/5.pub

#把node6的公钥发给node4
#node6
scp ~/.ssh/id_rsa.pub root@node4:~/.ssh/6.pub
```

在这个节点将所有公钥写进一个文件

```
node4
#自己
cp ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys
#写node5
cat ~/.ssh/5.pub >> ~/.ssh/authorized_keys
#写node6
cat ~/.ssh/6.pub >> ~/.ssh/authorized_keys

```

再将这个文件发给其他节点
```
#node4 
#发给node5 
scp ~/.ssh/authorized_keys root@node5:~/.ssh/ 
#发给node6 
scp ~/.ssh/authorized_keys root@node6:~/.ssh/
```


# 3.挂载宿主机文件夹

注意：`-v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work` 挂载命令

一旦run后想再修改挂载目录，就要docker commit现在这个镜像，删除已有容器，根据新的镜像再run一次用新的挂载目录

```
docker run -it --name node4 -h node4 --net network_mpi --ip 192.161.10.20 --add-host node5:192.161.10.21  --add-host node6:192.161.10.22 -v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work  node4image /bin/bash

docker run -it --name node5 -h node5 --net network_mpi --ip 192.161.10.21 --add-host node4:192.161.10.20  --add-host node6:192.161.10.22 -v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work  node4image /bin/bash

docker run -it --name node6 -h node6 --net network_mpi --ip 192.161.10.22 --add-host node4:192.161.10.20  --add-host node5:192.161.10.21 -v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work  node4image /bin/bash

```

# 4.在节点开启ssh服务
[[linux里运行和检查ssh服务]]


# 运行计算pi任务成功
```bash
root@node4:/home/mpi/work/project1# bash run.sh
-- Configuring done
-- Generating done
-- Build files have been written to: /home/mpi/work/project1/build
Scanning dependencies of target calculate_pi
[ 25%] Building CXX object CMakeFiles/calculate_pi.dir/calculate_pi.cpp.o
[ 50%] Linking CXX executable calculate_pi
[ 50%] Built target calculate_pi
Scanning dependencies of target hello_world
[ 75%] Building CXX object CMakeFiles/hello_world.dir/hello_world.cpp.o
[100%] Linking CXX executable hello_world
[100%] Built target hello_world
============== Test for calculate_pi ==============
Hello world from process 0 of 1 on node4.
Using 10000000 intervals.
The number of processor is 1
Pi is approximately: 3.1415926535897309
Time elapsed: 0.032160 seconds

Hello world from process 1 of 2 on node5.
Hello world from process 0 of 2 on node4.
Using 10000000 intervals.
The number of processor is 2
Pi is approximately: 3.1415926535899850
Time elapsed: 0.015662 seconds

Hello world from process 1 of 3 on node5.
Hello world from process 2 of 3 on node6.
Hello world from process 0 of 3 on node4.
Using 10000000 intervals.
The number of processor is 3
Pi is approximately: 3.1415926535899121
Time elapsed: 0.010438 seconds

Hello world from process 0 of 4 on node4.
Hello world from process 1 of 4 on node5.
Hello world from process 2 of 4 on node6.
Hello world from process 3 of 4 on node4.
Using 10000000 intervals.
The number of processor is 4
Pi is approximately: 3.1415926535896861
Time elapsed: 0.015716 seconds

Hello world from process 1 of 5 on node5.
Hello world from process 2 of 5 on node6.
Hello world from process 4 of 5 on node5.
Hello world from process 3 of 5 on node4.
Hello world from process 0 of 5 on node4.
Using 10000000 intervals.
The number of processor is 5
Pi is approximately: 3.1415926535897145
Time elapsed: 0.006408 seconds

Hello world from process 3 of 6 on node4.
Hello world from process 1 of 6 on node5.
Hello world from process 4 of 6 on node5.
Hello world from process 0 of 6 on node4.
Hello world from process 5 of 6 on node6.
Hello world from process 2 of 6 on node6.
Using 10000000 intervals.
The number of processor is 6
Pi is approximately: 3.1415926535897709
Time elapsed: 0.006418 seconds

Hello world from process 3 of 7 on node4.
Hello world from process 1 of 7 on node5.
Hello world from process 5 of 7 on node6.
Hello world from process 6 of 7 on node4.
Hello world from process 4 of 7 on node5.
Hello world from process 2 of 7 on node6.
Hello world from process 0 of 7 on node4.
Using 10000000 intervals.
The number of processor is 7
Pi is approximately: 3.1415926535897958
Time elapsed: 0.004643 seconds

Hello world from process 3 of 8 on node4.
Hello world from process 1 of 8 on node5.
Hello world from process 0 of 8 on node4.
Hello world from process 7 of 8 on node5.
Hello world from process 6 of 8 on node4.
Hello world from process 5 of 8 on node6.
Hello world from process 4 of 8 on node5.
Hello world from process 2 of 8 on node6.
Using 10000000 intervals.
The number of processor is 8
Pi is approximately: 3.1415926535898069
Time elapsed: 0.010013 seconds

=================== End of test ===================
```
