### 伴生对象
伴生对象是kotlin 对一个类里面的静态函数提出的解决方案  [[静态函数]]

kotlin 弱化了静态函数这个概念,推荐使用单例类实现Java 中的工具类[常用工具类 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1260473555087392),使用半生对象就是我们希望一个类里有一个工具函数的做法


类内部的对象声明可以用 _companion_ 关键字标记

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

该伴生对象的成员可通过只使用类名作为限定符来调用：

```kotlin
val instance = MyClass.create()
```

可以省略伴生对象的名称，在这种情况下将使用名称 `Companion`：
```kotlin
class MyClass {
    companion object { }
}

val x = MyClass.Companion
```

#kotlin 
#安卓 