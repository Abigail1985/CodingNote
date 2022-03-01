一个app是由很多activity构成的,在as里新建一个activity,需要指定对应布局layout的名称,然后新建一个activity之后会同时生成这个layout,同时AndroidManifest.xml也会有一段代码的更新,这是一个注册表,**任何一个Activity都是需要在AndroidManifest.xml中注册的**

如果不是mainactivity,就不需要配置`<intent-filter>`标签里的内容，注 册Activity的代码也简单了许多

```kotlin
<activity android:name=".lesson2.RecyclerViewDemoActivity"></activity>  
<activity android:name=".lesson2.BasicUIDemoActivity" />  
<activity android:name=".lesson2.ActivityB" />

<activity android:name=".MainActivity">  
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />    
		<category android:name="android.intent.category.LAUNCHER" />  
	</intent-filter>

```

最后一个是主activity,前3个不是

#安卓
#Kotlin

[[创建activity时为什么要写super.onCreate]]