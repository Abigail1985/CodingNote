
# 1.安装mpi

[基于docker的MPI安装_docker mpi_pppp_k的博客-CSDN博客](https://blog.csdn.net/weixin_50674454/article/details/124770487)

# 2.建立ssh免密通信
制作一个docker网段
```
docker network create --subnet=192.168.10.0/16 network_mpi 
docker network ls
```

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
docker run -it --name node4 -h node4 --net network_mpi --ip 192.168.10.20 --add-host node5:192.168.10.21  --add-host node6:192.168.10.22 -v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work  node4image /bin/bash

docker run -it --name node5 -h node5 --net network_mpi --ip 192.168.10.21 --add-host node4:192.168.10.20  --add-host node6:192.168.10.22 -v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work  node4image /bin/bash

docker run -it --name node6 -h node6 --net network_mpi --ip 192.168.10.22 --add-host node4:192.168.10.20  --add-host node5:192.168.10.21 -v  /C/Users/12585/Desktop/mpiwork:/home/mpi/work  node4image /bin/bash
```


