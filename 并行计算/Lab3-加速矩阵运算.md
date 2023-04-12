
[GitHub - adolfogonzalez3/mpi-example: A collection of C++ programs that use MPI](https://github.com/adolfogonzalez3/mpi-example)

## 问题
### MPI_Comm
MPI_Status是MPI标准库中用于表示消息状态的一种数据类型。在MPI中，进程之间通过发送和接收消息来进行通信，MPI_Status数据类型用于表示一个已完成的MPI消息的状态，包括了消息的发送者、接收者、标记等信息。

MPI_Status数据类型是一个结构体类型，包含了以下信息：

-   MPI_SOURCE：消息的发送者的rank。
-   MPI_TAG：消息的标记。
-   MPI_ERROR：用于存储MPI函数的错误代码，如果消息成功完成，则MPI_ERROR的值为MPI_SUCCESS。

MPI_Status数据类型可以在MPI_Recv等函数的参数中使用，用于返回已接收到的消息的状态信息。在MPI中，进程之间通信时通常需要使用MPI_Status类型的参数，以便获取消息的状态信息。例如，MPI_Wait函数可以等待某个发送或接收操作的完成，并使用MPI_Status类型的参数返回消息的状态信息。

### MPI_Cart_create（）
MPI_Cart_create是一个MPI标准库函数，用于创建一个二维笛卡尔拓扑结构。其参数依次为：输入通信器comm，表示该拓扑结构中的进程集合；维度数目ndims，表示拓扑结构中的维度数目；dims，表示每个维度上的进程数；periods，表示每个维度上是否开启周期性边界；reorder，表示是否重新排列进程的rank以优化进程之间的通信；输出通信器comm_2d，表示该拓扑结构中的进程集合。

具体到这行代码，MPI_Cart_create的作用是根据输入通信器comm，创建一个二维笛卡尔拓扑结构，并将输出结果存放在comm_2d中。这里，维度数目为2，dims表示每个维度上的进程数均为sqrt(npes)，即进程总数npes的平方根。periods参数均为1，表示每个维度上开启了周期性边界。reorder参数为1，表示重新排列进程的rank以优化进程之间的通信。在这个拓扑结构中，每个进程的rank与它在二维拓扑结构中的坐标有关。

#### 进程的二维迪卡尔结构
在MPI中，二维笛卡尔拓扑结构由MPI_Cart_create函数创建，并可以使用MPI_Comm_rank和MPI_Cart_coords函数来获取进程在该拓扑结构中的rank和坐标。同时，MPI_Cart_shift函数可以用来获取在拓扑结构中相邻进程的rank，MPI_Sendrecv_replace函数可以用来实现进程之间的通信。使用这些函数，我们可以方便地在一个二维笛卡尔拓扑结构中进行进程间的数据通信和计算任务的分配。

#### 进程的周期性边界
MPI函数周期性边界是MPI拓扑结构函数中的一种选项，用于指定在拓扑结构中的某个维度上是否开启周期性边界。周期性边界的作用是让拓扑结构在某个维度上具有环形结构，使得该维度的首尾相连，即当一个进程在该维度上的坐标达到边界时，会跳转到另一端的相应坐标。

周期性边界的开启可以通过MPI函数MPI_Cart_create中的periods参数来指定，该参数是一个大小为ndims的整数数组，其中的每个元素指定了拓扑结构中相应维度是否开启周期性边界。如果某个维度上开启了周期性边界，MPI将会自动处理数据的传输，使得在该维度上的数据传输与在环形结构上的数据传输相一致。在使用周期性边界时，需要注意处理边界情况，以保证计算结果的正确性。

### MPI_Cart_coords(comm_2d, my2drank, 2, mycoords)
MPI_Cart_coords函数的作用是获取当前进程在二维笛卡尔拓扑结构中的坐标信息。MPI_Cart_coords函数需要传入三个参数，分别为通信器、进程rank和维度数。在代码中，MPI_Cart_coords函数的输入参数为comm_2d（二维笛卡尔拓扑结构的通信器）、my2drank（当前进程在二维笛卡尔拓扑结构中的rank）和2（表示笛卡尔拓扑结构中的维度数量），返回结果存储在mycoords数组中。

mycoords是一个大小为2的整型数组，其中mycoords[0]和mycoords[1]分别表示当前进程在二维笛卡尔拓扑结构中的行和列坐标。这个函数可以帮助我们获取每个进程在笛卡尔拓扑结构中的坐标信息，以便更好地划分计算任务和进行通信操作。

### MPI_Cart_shift(comm_2d, 0, -1, &rightrank, &leftrank);
在之前那段代码中，MPI_Cart_shift函数的作用是获取在二维笛卡尔拓扑结构中当前进程在某个维度上的相邻进程的rank。MPI_Cart_shift函数需要传入五个参数，分别为通信器、坐标轴方向、移动距离、向左移动的进程rank和向右移动的进程rank，返回结果存储在rightrank和leftrank变量中。

在代码中，MPI_Cart_shift函数的输入参数为comm_2d（二维笛卡尔拓扑结构的通信器）、0（表示在笛卡尔拓扑结构的列维度上进行操作）、-1（表示向左移动一个单位距离），以及&rightrank和&leftrank（用于存储向左移动和向右移动后的进程rank）。这个函数的作用是获取当前进程在笛卡尔拓扑结构中，列维度上的左侧和右侧相邻进程的rank，分别存储在leftrank和rightrank中。

