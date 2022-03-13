### 方法一
```kotlin

//新建一个类继承自 Thread,然后重写父类的 run 方法
class MyThread: Thread(){
	overrider fun run(){
	//编写具体的逻辑
	}
}

//调用
MyThread().start()

```


### 方法二

```kotlin
//新建一个类,要求这个类里面实现 Runnable 接口,重写接口里面的 run 方法
class MyThread:Runnable(){
	override fun run(){
	//编写具体的逻辑
	}
}

//新建一个类的实例
//Thread 的构造函数接受一个 Runnable 对象,而这个实例正好是一个实现了 Runnable 接口的对象,可以传进 Thread 的构造函数里,调用 Thread 的 start 方法
 val myThread=MyThread()
 Thread(myThread).start()

```

这个方法相比起第一个继承的方法来说,降低了耦合性

### 方法三
```kotlin
//使用 Lambda 表达式
Thread{
	//编写具体的逻辑
}.start()
```

这个方法相比起第二个实现接口的方法来说, 不需要专门定义一个类实现 runnable 接口污染环境

### 方法四

```kotlin

thread{
	//编写具体的逻辑
}
```

这是 kotlin 一个内置的顶层函数, 连 start 方法都不需要调用

#kotlin 