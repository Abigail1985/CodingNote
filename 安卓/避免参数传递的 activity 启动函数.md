假设SecondActivity中需要用到两个非常重要的字符串参数，在启动SecondActivity的时候必 须传递过来，那么我们很容易会写出如下代码：

```kotlin
val intent = Intent(this, SecondActivity::class.java) 
intent.putExtra("param1", "data1") 
intent.putExtra("param2", "data2") 
startActivity(intent)
```

虽然这样写是完全正确的，但是在真正的项目开发中经常会出现对接的问题。比如 SecondActivity并不是由你开发的，但现在你负责开发的部分需要启动SecondActivity，而你 却不清楚启动SecondActivity需要传递哪些数据。这时无非就有两个办法：一个是你自己去阅 读SecondActivity中的代码，另一个是询问负责编写SecondActivity的同事。你会不会觉得很 麻烦呢？其实只需要换一种写法，就可以轻松解决上面的窘境。

修改SecondActivity中的代码，如下所示：

```kotlin
class SecondActivity : BaseActivity() {

	...
	
	companion object { 
		fun actionStart(context: Context, data1: String, data2: String) { 
			val intent = Intent(context, SecondActivity::class.java) 
			intent.putExtra("param1", data1) 
			intent.putExtra("param2", data2) 
			context.startActivity(intent) 
		} 
	}
}
```

在这里我们使用了一个新的语法结构companion object，并在companion object中定义 了一个actionStart()方法。之所以要这样写，是因为Kotlin规定，所**有定义在companion object中的方法都可以使用类似于Java静态方法的形式调用**。
[[Kotlion 伴生对象]]

接下来我们重点看actionStart()方法，在这个方法中完成了Intent的构建，另外所有 SecondActivity中需要的数据都是通过actionStart()方法的参数传递过来的，然后把它们 存储到Intent中，最后调用startActivity()方法启动SecondActivity。

```kotlin
button1.setOnClickListener { 
	SecondActivity.actionStart(this, "data1", "data2") 
}
```

#kotlin 
#安卓 