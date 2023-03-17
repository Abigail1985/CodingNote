
# 问题

c/c++标准库头文件的路径找不到时，IntelliSense会在头文件下用绿色波浪线(squiggles)进行提示。

# 解决方法

有两种方式可以解决，一种是通过command+shift+p命令打开：c/c++：edit configuration UI；一种是通过command+shift+p命令打开：c/c++：edit configuration JSON，即c_cpp_properties.json文件。

头文件的选择要跟编译器对应

用以下命令查看对应编译器的查找路径，好用！  
比如用下面的命令查看mpicxx的安装路径和头文件查找路径：

```bash
which mpicxx
mpicxx -v -E -x c++ -
```

  
将第一条命令的输出结果配置为 compilerPath  
将第二条命令的输出结果配置为includePath

成功解决波浪形问题