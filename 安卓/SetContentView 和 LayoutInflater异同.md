SetContentView 和 LayoutInflater异同

SetContentView(直接用的情况下) 和 LayoutInfalter 都是根据指定的 xml 文件生成一个 View 对象,区别是前者是隐式的,创建完自动调用;后者是显式的, 需要我们手动用函数setContentView调用

When you write an XML layout, it will be inflated by the Android OS which basically means that it will be rendered by creating view object in memory. Let's call that **implicit inflation** (the OS will inflate the view for you). For instance:

```java
class Name extends Activity{
    public void onCreate(){
         // the OS will inflate the your_layout.xml
         // file and use it for this activity
         setContentView(R.layout.your_layout);
    }
}
```

You can also inflate views explicitly by using the LayoutInflater. In that case you have to:

1. Get an instance of the LayoutInflater
2. Specify the XML to inflate
3. Use the returned View
4. Set the content view with returned view (above)

For instance:

```java
LayoutInflater inflater = LayoutInflater.from(YourActivity.this); // 1

View theInflatedView = inflater.inflate(R.layout.your_layout, null); // 2 and 3

setContentView(theInflatedView) // 4

```
#安卓