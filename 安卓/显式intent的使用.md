Intent是Android程序中各组件之间进行交互的一种重要方式，它不仅可以指明当前组件想要执 行的动作，还可以在不同组件之间传递数据。**Intent可用于启动Activity**

Intent有多个构造函数的重载，其中一个是
`Intent(Context packageContext, Class<?> cls)`

`Context`和`Class<?>`都是数据类型, 后面的是形式参数名称

>关于第二个参数类型,可以看[[Java泛型和反射]],  ` Class<?> `是通配形式
>关于第一个参数类型,可以看[[安卓的context类]]

这个构造函数接收两个参数：
第一个参数`Context`要求提供一个启动Activity的上下文；
第二个参数`Class`用于指定想要启动的目标Activity，通过这个构造函数就可以构建出 Intent的“意图”

## startActivity()方法
**Activity类中提供了一个 startActivity()方法，专门用于启动Activity，它接收一个Intent参数**，这里我们将构建好 的Intent传入startActivity()方法就可以启动目标Activity了

修改FirstActivity中按钮的点击事件，代码如下所示：

```kotlin
button1.setOnClickListener { 
	val intent = Intent(this, SecondActivity::class.java) 
	startActivity(intent) 
}
```

我们首先构建了一个Intent对象，第一个参数传入this也就是FirstActivity作为上下文，第二 个参数传入`SecondActivity::class.java`作为目标Activity，这样我们的“意图”就非常明 显了，即在FirstActivity的基础上打开SecondActivity。

Kotlin中` SecondActivity::class.java`的写法就相当于Java中`SecondActivity.class`的写法[[java的class类]]
[[Java反射机制]]

#安卓 
#kotlin 