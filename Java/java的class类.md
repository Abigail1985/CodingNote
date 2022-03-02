Java中，无论生成某个类的多少个对象，这些对象都会对应于同一个Class对象，这个Class对象是由JVM生成的，通过它能够获悉整个类的结构。

要想使用反射，首先需要获得待操作的类所对应的Class对象。

### 原理

所有的java类都是继承了object这个类，在object这个类中有一个方法：getclass().这个方法是用来取得该类已经被实例化了的对象的该类的引用，这个引用指向的是Class类的对象。

我们自己无法生成一个Class对象（构造函数为private)，而 这个Class类的对象是在当各类被调入时，由 Java 虚拟机自动创建 Class 对象，或通过类装载器中的 defineClass 方法生成。

我们生成的对象都会有个字段记录该对象所属类在CLass类的对象的所在位置。如下图所示：

![Java Class类原理](http://dl.iteye.com/upload/picture/pic/101542/0047a6e9-6608-3c3c-a67c-d8ee95e7fcb8.jpg?_=4680532)

### 获取反射中的Class对象

在反射中，要获取一个类或调用一个类的方法，我们首先需要获取到该类的 Class 对象。

在 Java API 中，获取 Class 类对象有三种方法：

**第一种，使用Class类的静态方法。**当你知道该类的全路径名时，你可以使用该方法获取 Class 类对象。

```java
Class clz = Class.forName("java.lang.String");
```

**第二种，使用类的 .class语法。**

这种方法只适合在编译前就知道操作的 Class。

```java
Class clz = String.class;
```

**第三种，使用对象的 getClass() 方法。**

```java
String str = new String("Hello");
Class clz = str.getClass();
```

### Class类的静态方法
[link](http://www.51gjie.com/java/777.html)

#java
