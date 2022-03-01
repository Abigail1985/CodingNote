创建activity时候首先用`override fun onCreate(savedInstanceState: Bundle?) `重写onCreat方法，这类似于C++里的纯虚函数，我们调用纯虚函数然后自己实现方法。

然后要写`super.onCreate(savedInstanceState)`，这是因为在java/kotlin里，通过这个命令可以让程序不仅运行接下来我们自己写的代码，还运行原先就存在的Oncreate里的代码，不加这一行的话，原先的代码就会被忽略，会导致程序出现很多错误

原话：

Every Activity you make is started through a sequence of method calls. onCreate() is the first of these calls.

Each and every one of your Activities extends android.app.Activity either directly or by subclassing another subclass of Activity.

In Java, when you inherit from a class, you can override its methods to run your own code in them. A very common example of this is the overriding of the toString() method when extending java.lang.Object.

When we override a method, we have the option of completely replacing the method in our class, or of extending the existing parent class' method. By calling super.onCreate(savedInstanceState);, you tell the Dalvik VM to run your code in addition to the existing code in the onCreate() of the parent class. If you leave out this line, then only your code is run. The existing code is ignored completely.

#安卓