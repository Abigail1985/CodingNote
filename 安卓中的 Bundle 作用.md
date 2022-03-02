当一个Activity进入了停止状态，是有可能被系统回收的,如果发生了这种情况,并不会执行 onRestart()方法，而是会执行这个Activity的onCreate()方法.

之前注意到,每一个 OnCreate()方法都带有一个 Bundle 参数,这个参数其实就是为了上面那种情况准备的,一般都是 null,但是如果在Activity被系统回收之前，你通过onSaveInstanceState()方法保存数 据，这个参数就会带有之前保存的全部数据，我们只需要再通过相应的取值方法将数据取出即 可。

如何用 OnSaveInstanceState()保存数据:
```kotlin
override fun onSaveInstanceState(outState: Bundle) { 
	super.onSaveInstanceState(outState) 
	val tempData = "Something you just typed" 
	outState.putString("data_key", tempData) 
}
```
每个保存方法需要传入两个参数，第一个参数是键，用于后面从Bundle中取 值，第二个参数是真正要保存的内容


如何获取 Bundle 中的数据:
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {

	super.onCreate(savedInstanceState) Log.d(tag, "onCreate")
	setContentView(R.layout.activity_main) 
	
	if (savedInstanceState != null) {
		val tempData = savedInstanceState.getString("data_key")
		Log.d(tag, "tempData is $tempData") 
	} ...
}

```

#安卓 
#Kotlin 
