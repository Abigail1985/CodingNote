kotlin中所有的参数和变量都不能为空, 且Kotlin将空指针异常的检查提前到了编译时期

除非在定义数据类型时候在后面加上一个问号?,这种情况下这个数据可以为空,程序员自己要做空指针异常处理,如下
```kotlin
fun doStudy(study: Study?) { 
	if (study != null) { 
		study.readBooks() 
		study.doHomework() 
	} 
}
```


有一个判空操作符是`?.`用法如下:
```kotlin
if (a != null) { a.doSomething() }
```
可以简化成
```kotlin
a?.doSomething()
```

因此之前的代码可以简化成:

```kotlin
fun doStudy(study: Study?) { 
		study?.readBooks() 
		study?.doHomework() 
}
```

#安卓
#kotlin 
