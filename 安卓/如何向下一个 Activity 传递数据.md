如何向下一个 Activity 传递数据

Intent中提供了一系列putExtra()方法的重载，可 以把我们想要传递的数据暂存在Intent中，在启动另一个Activity后，只需要把这些数据从 Intent中取出就可以了

```kotlin
button1.setOnClickListener { 
	val data = "Hello SecondActivity" 
	val intent = Intent(this, SecondActivity::class.java) 
	intent.putExtra("extra_data", data) 
	startActivity(intent) 
}
```

在SecondActivity 中取出数据:
```kotlin
class SecondActivity : AppCompatActivity() {

	override fun onCreate(savedInstanceState: Bundle?) 
	{ 
		super.onCreate(savedInstanceState) 
		setContentView(R.layout.second_layout) 
		val extraData = intent.getStringExtra("extra_data") 
		Log.d("SecondActivity", "extra data is $extraData") 
	}

}
```

#安卓
#kotlin