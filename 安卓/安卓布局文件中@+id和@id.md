添加一个Button元素，并在Button元素的内部增加了几个属性。android:id是给当前 的元素定义一个唯一的标识符，之后可以在代码中对这个元素进行操作。

你可能会对 @+id/button1这种语法感到陌生，但如果把加号去掉，变成@id/button1，你就会觉得有些 熟悉了吧。这不就是在XML中引用资源的语法吗？只不过是把string替换成了id。

是的，**如果你需要在XML中引用一个id，就使用@id/id_name这种语法，而如果你需要在XML中定义一 个id，则要使用@+id/id_name这种语法。**

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:orientation="vertical" android:layout_width="match_parent"

android:layout_height="match_parent">

<Button

android:id="@+id/button1" android:layout_width="match_parent" android:layout_height="wrap_content"

android:text="Button 1"

/>

</LinearLayout>
```

#安卓
#Kotlin
