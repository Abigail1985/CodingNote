## When 表达式
第一行代码 2.4.2 P42

[控制流：if、when、for、while - Kotlin 语言中文站](https://www.kotlincn.net/docs/reference/control-flow.html)

_when_ 表达式取代了类 C 语言的 switch 语句。其最简单的形式如下：

```kotlin
when (x) {

 1 -> print("x == 1")

 2 -> print("x == 2")

 else -> { // 注意这个块

 print("x is neither 1 nor 2")

 }

}
```

_when_ 将它的参数与所有的分支条件顺序比较，直到某个分支满足条件。 _when_ 既可以被当做表达式使用也可以被当做语句使用。如果它被当做表达式， 符合条件的分支的值就是整个表达式的值，如果当做语句使用， 则忽略个别分支的值。（像 _if_ 一样，每一个分支可以是一个代码块，它的值是块中最后的表达式的值。）

如果其他分支都不满足条件将会求值 _else_ 分支。 如果 _when_ 作为一个表达式使用，则必须有 _else_ 分支， 除非编译器能够检测出所有的可能情况都已经覆盖了［例如，对于 [枚举（_enum_）类](https://www.kotlincn.net/docs/reference/enum-classes.html)条目与[密封（_sealed_）类](https://www.kotlincn.net/docs/reference/sealed-classes.html)子类型］。

如果很多分支需要用相同的方式处理，则可以把多个分支条件放在一起，用逗号分隔：

```kotlin
when (x) {

 0, 1 -> print("x == 0 or x == 1")

 else -> print("otherwise")

}
```

我们可以用任意表达式（而不只是常量）作为分支条件


```kotlin
 when (x) {

 parseInt(s) -> print("s encodes x")

 else -> print("s does not encode x")

}
```

我们也可以检测一个值在（_in_）或者不在（_!in_）一个[区间](https://www.kotlincn.net/docs/reference/ranges.html)或者集合中：

```
when (x) {

 in 1..10 -> print("x is in the range")

 in validNumbers -> print("x is valid")

 !in 10..20 -> print("x is outside the range")

 else -> print("none of the above")

}
```

另一种可能性是检测一个值是（_is_）或者不是（_!is_）一个特定类型的值。注意： 由于[智能转换](https://www.kotlincn.net/docs/reference/typecasts.html#%E6%99%BA%E8%83%BD%E8%BD%AC%E6%8D%A2)，你可以访问该类型的方法与属性而无需任何额外的检测。

```
fun hasPrefix(x: Any) = when(x) {

 is String -> x.startsWith("prefix")

 else -> false

}
```

_when_ 也可以用来取代 _if_-_else_ _if_链。 如果不提供参数，所有的分支条件都是简单的布尔表达式，而当一个分支的条件为真时则执行该分支：

```
when {

 x.isOdd() -> print("x is odd")

 y.isEven() -> print("y is even")

 else -> print("x+y is odd.")

}
```

自 Kotlin 1.3 起，可以使用以下语法将 _when_ 的主语（subject，译注：指 `when` 所判断的表达式）捕获到变量中：

```
fun Request.getBody() =

 when (val response = executeRequest()) {

 is Success -> response.body

 is HttpError -> throw HttpException(response.status)

 }
 ```

在 _when_ 主语中引入的变量的作用域仅限于 _when_ 主体

#kotlin 
#安卓 

