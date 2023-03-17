
## 使用 MPI_Bcast 来进行广播

_广播_ (broadcast) 是标准的集体通信技术之一。一个广播发生的时候，一个进程会把同样一份数据传递给一个 communicator 里的所有其他进程。广播的主要用途之一是把用户输入传递给一个分布式程序，或者把一些配置参数传递给所有的进程。

广播的通信模式看起来像这样：

![MPI_Bcast 模式](https://mpitutorial.com/tutorials/mpi-broadcast-and-collective-communication/broadcast_pattern.png)

在这个例子里，进程0是我们的_根_进程，它持有一开始的数据。其他所有的进程都会从它这里接受到一份数据的副本。

在 MPI 里面，广播可以使用 `MPI_Bcast` 来做到。函数签名看起来像这样：

```
MPI_Bcast(
    void* data,
    int count,
    MPI_Datatype datatype,
    int root,
    MPI_Comm communicator)
```

尽管根节点和接收节点做不同的事情，它们都是调用同样的这个 `MPI_Bcast` 函数来实现广播。当根节点(在我们的例子是节点0)调用 `MPI_Bcast` 函数的时候，`data` 变量里的值会被发送到其他的节点上。当其他的节点调用 `MPI_Bcast` 的时候，`data` 变量会被赋值成从根节点接受到的数据。

实现使用了一个树形广播算法来获得比较好的网络利用率


## 函数示例

```c
/************************************    
// 程序功能: MPI_Bcast函数用法             
// 作成日期：2016/12/14     
// 详细说明：
//************************************/  

#include <stdio.h>
#include <mpi.h>

#pragma comment(lib,"mpi.lib")

/************************************************
MPI_BCAST(buffer,count,datatype,root,comm) 
    IN/OUT　buffer　　  通信消息缓冲区的起始地址(可变)
    IN　　　 count　  　 通信消息缓冲区中的数据个数(整型) 
    IN 　　　datatype 　通信消息缓冲区中的数据类型(句柄) 
    IN　　　 root　  　　发送广播的根的序列号(整型) 
    IN 　　　comm   　　通信子(句柄) 
int MPI_Bcast(void* buffer,int count,MPI_Datatype datatype,int root, MPI_Comm comm) 

MPI_BCAST是从一个序列号为root的进程将一条消息广播发送到组内的所有进程,
包括它本身在内.调用时组内所有成员都使用同一个comm和root,
其结果是将根的通信消息缓冲区中的消息拷贝到其他所有进程中去.
**********************************************/

int main(int argc,char *argv[]){
    int rank,nproc;

    MPI_Init(&argc,&argv);
    MPI_Comm_rank(MPI_COMM_WORLD,&rank);
    MPI_Comm_size(MPI_COMM_WORLD,&nproc);

    int data = 99;
    int tag = 100;
    MPI_Status status;
    MPI_Bcast(&data,1,MPI_INT,0,MPI_COMM_WORLD);

    for (int i=0; i<nproc && rank!=0; i++){
    
        printf("data = %d in %d process.\n",data,rank);
    }

    MPI_Finalize();
    return 0;
}
```