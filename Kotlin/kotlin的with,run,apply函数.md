## kotlin的with,run,apply
都是利用 lambda 表达式优化代码结构, 减少代码量的语法
[[Java 中的lambda 表达式]]

原代码
```kotlin
val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape") 

val builder = StringBuilder() 

builder.append("Start eating fruits.\n") 

for (fruit in list) { 
	builder.append(fruit).append("\n") 
} 

builder.append("Ate all fruits.") 

val result = builder.toString()

println(result)
```
其中涉及[[Java 的 StringBuilder 对象]]

### with()
with函数接收两个参数：
- 第一个参数可以是一个任意类型的对 象，
- 第二个参数是一个Lambda表达式。

with函数会在Lambda表达式中提供第一个参数对象 的上下文，

with函数使用Lambda表达式中的**最后一行代码**作为返回值返回

```kotlin
val list = listof( "Apple" "Banana"， "Orange","Pear", "Grape")

val result = with(StringBuilder()) {
	append( "Start eating fruits. \n")
	for(fruit in list) {
		append(fruit).append( "\n")
	}
	append ( "Ate all fruits.")
	tostring()//toString()的内容是返回值
}
printin (result)
```

### run()
run函数通常不会直接调用， 而是要在某个对象的基础上调用；其次run函数只接收一个Lambda参数， 

run 函数并且会在Lambda表 达式中提供调用对象的上下文。

run函数使用Lambda表达式中的**最后一行代码**作为返回值返回


```kotlin
val list = listof( "Apple" "Banana"， "Orange","Pear", "Grape")

val result = StringBuilder().run {
	append( "Start eating fruits. \n")
	for(fruit in list) {
		append(fruit).append( "\n")
	}
	append ( "Ate all fruits.")
	tostring()//toString()的内容是返回值
}
printin (result)
```

### apply()
run函数通常不会直接调用， 而是要在某个对象的基础上调用；其次run函数只接收一个Lambda参数， 

run 函数并且会在Lambda表 达式中提供调用对象的上下文。

apply函数无法指定返回值，而是会**自动返回调用对象本身**


```kotlin
val list = listof( "Apple" "Banana"， "Orange","Pear", "Grape")

val result = StringBuilder().apply {
	append( "Start eating fruits. \n")
	for(fruit in list) {
		append(fruit).append( "\n")
	}
	append ( "Ate all fruits.")
}

printin (result.toString())
//返回的是StringBuilder 对象,所以要记得调用该对象的 toString 方法
```

### 在工程上的应用
[[显式intent的使用]] intent 中可以携带参数
[[安卓中的 Bundle 作用]] 都是传递参数


回想一下刚刚在最佳实践环节编写的启动Activity的代码：[[避免参数传递的 activity 启动函数]]

```kotlin
val intent = Intent(context, SecondActivity::class.java) 
intent.putExtra("param1", "data1") 
intent.putExtra("param2", "data2") 
context.startActivity(intent)
```

这里每传递一个参数就要调用一次intent.putExtra()方法，如果要传递10个参数，那就得 调用10次。对于这种情况，我们就可以使用标准函数来对代码进行精简，如下所示：


```kotlin
val intent = Intent(context, SecondActivity::class.java).apply{
	putExtra("param1", "data1") 
	putExtra("param2", "data2") 
}
context.startActivity(intent)
```

可以看到，由于Lambda表达式中的上下文就是Intent对象，所以我们不再需要调用 intent.putExtra()方法，而是直接调用putExtra()方法就可以了。传递的参数越多，这 种写法的优势也就越明显。

#kotlin 
#安卓 
