方便访问对象的私有成员变量，不需要再自己写getter和setter

Java Bean的概念，它是一个非常简单的Java类，会根据类中的字段自动生成相应的Getter和 Setter方法，如下所示：

```java
public class Book {

private int pages;

public int getPages() { return pages; }

public void setPages(int pages) { this.pages = pages; }

}

```

在Kotlin中调用这种语法结构的Java方法时，可以使用一种更加简便的写法，比如用如下代码来 设置和读取Book类中的pages字段：

```kotlin
val book = Book() 
book.pages = 500 
val bookPages = book.pages
```

这里看上去好像我们并没有调用Book类的setPages()和getPages()方法，而是直接对 pages字段进行了赋值和读取。其实这就是Kotlin给我们提供的语法糖，它会在背后自动将上述 代码转换成调用setPages()方法和getPages()方法。

#安卓
#Kotlin
#java