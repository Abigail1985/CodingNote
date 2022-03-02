### 两个作用

> **获取程序在运行时刻的内部结构**。这对于程序的检查工具和调试器来说，是非常实用的功能,知道了 Java 类的内部 结构之后，就可以与它进行交互，包括创建新的对象和调用对象中的方法等。

只要有了 java.lang.Class 类 的对象，就可以通过其中的方法来获取到该类中的构造方法、域和方法。对应的方法分别是 [getConstructor](http://download.oracle.com/javase/6/docs/api/java/lang/Class.html#getConstructor%28java.lang.Class...%29) 、 [getField](http://download.oracle.com/javase/6/docs/api/java/lang/Class.html#getField%28java.lang.String%29) 和 [getMethod](http://download.oracle.com/javase/6/docs/api/java/lang/Class.html#getMethod%28java.lang.String,%20java.lang.Class...%29) 。

[Constructor](http://download.oracle.com/javase/6/docs/api/java/lang/reflect/Constructor.html) 、 [Field](http://download.oracle.com/javase/6/docs/api/java/lang/reflect/Field.html) 和 [Method](http://download.oracle.com/javase/6/docs/api/java/lang/reflect/Method.html) 这三个**类**分别表示类中的构造方法、域和方法。这些类中的方法可以获取到所对应结构的元数据。


>**在运行时刻对一个 Java 对象进行操作**。 这些操作包括动态创建一个 Java 类的对象，获取某个域的值以及调用某个方法。

在 Java 源代码中编写的对类和对象的操作，都可以在运行时刻通过反射 API 来实现。


### 缺陷
使用反射的一个最大的弊端是[性能比较差](http://stackoverflow.com/questions/435553/java-reflection-performance)。相同的操作，用反射API 所需的时间大概比直接的使用要慢一两个数量级。

[link](https://www.infoq.cn/article/cf-java-reflection-dynamic-proxy/)

[[java的class类]]

#java 
