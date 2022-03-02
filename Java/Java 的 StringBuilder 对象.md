## Java 的 StringBuilder 对象

为了能高效拼接字符串，Java标准库提供了StringBuilder类


Java编译器对String做了特殊处理，使得我们可以直接用+拼接字符串。

考察下面的循环代码：

```java
String s = "";
for (int i = 0; i < 1000; i++) {
    s = s + "," + i;
}
```


虽然可以直接拼接字符串，但是，在循环中，每次循环都会创建新的字符串对象，然后扔掉旧的字符串。这样，绝大部分字符串都是临时对象，不但浪费内存，还会影响GC效率。

为了能高效拼接字符串，Java标准库提供了StringBuilder，它是一个可变对象，可以预分配缓冲区，这样，往StringBuilder中新增字符时，不会创建新的临时对象：

```java
StringBuilder sb = new StringBuilder(1024);
for (int i = 0; i < 1000; i++) {
    sb.append(',');
    sb.append(i);
}
String s = sb.toString();
```

#java

[StringBuilder - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1260471862687712)
[Java StringBuffer 和 StringBuilder 类 | 菜鸟教程](https://www.runoob.com/java/java-stringbuffer.html)