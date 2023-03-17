
## 编写MPI程序

### 启动和终止进程
```c
#include "mpi.h"  
#include <stdio.h>  
  
int main(int argc, char** argv)
{  
	MPI_Init( &argc, &argv );  
	printf( "Hello world\n" );  
	MPI_Finalize();  
	return 0;  
}
```
-   \#include "mpi.h"提供基本的MPI定义和类型
-   MPI_Init 启动MPI
-   MPI_Finalize 终止MPI
-   注意所有非MPI例程都是本地的；因此printf运行于每一个进程

### 存在多少个进程？以及我是谁？
对于并行程序的头两个问题是：存在多少个进程？以及我是谁？

多少的问题由MPI_Comm_size来回答，
我是谁的问题由MPI-Comm-rank来回答。

标识数是0到size-1的数。

```c
#include "mpi.h"  
#include <stdio.h>  
  
int main(int argc, char** argv) 
{  
	int rank, size;  
	MPI_Init( &argc, &argv );  
	MPI_Comm_rank( MPI_COMM_WORLD, &rank );  
	MPI_Comm_size( MPI_COMM_WORLD, &size );  
	printf( "Hello world! I'm %d of %d\n",   
	rank, size );  
	MPI_Finalize();  
	return 0;  
}
```

### 广播和归约
MPI_Bcast例程从一个进程发送数据到所有其它进程。

MPI_Reduce例程结合所有进程的数据（通过在此例中将它们相加），并且将结果返回给一个单个进程。


### 例子：计算Pi
```c
#include "mpi.h" 
#include <math.h> 

 
int main(argc,argv) 
int argc; 
char *argv[]; 
{ 
  int done = 0, n, myid, numprocs, i, rc; 
  double PI25DT = 3.141592653589793238462643; 
  double mypi, pi, h, sum, x, a; 

 
MPI_Init(&argc,&argv); 
  MPI_Comm_size(MPI_COMM_WORLD,&numprocs); 
  MPI_Comm_rank(MPI_COMM_WORLD,&myid);
while (!done) 
  { 
    if (myid == 0) { 
        printf("Enter the number of intervals: (0 quits) "); 
        scanf("%d",&n); 
    } 
    MPI_Bcast(&n, 1, MPI_INT, 0, MPI_COMM_WORLD); 
    if (n == 0) break; 
  

 
h   = 1.0 / (double) n; 
    sum = 0.0; 
    for (i = myid + 1; i <= n; i += numprocs) { 
        x = h * ((double)i - 0.5); 
        sum += 4.0 / (1.0 + x*x); 
    } 
    mypi = h * sum; 
    

 
MPI_Reduce(&mypi, &pi, 1, MPI_DOUBLE, MPI_SUM, 0, 
                MPI_COMM_WORLD); 
    

 
if (myid == 0) 
        printf("pi is approximately %.16f, Error is %.16f\n", 
               pi, fabs(pi - PI25DT)); 
  } 
  MPI_Finalize(); 
}
```

