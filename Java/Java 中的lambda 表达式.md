[Lambda 表达式有何用处？如何使用？ - 知乎](https://www.zhihu.com/question/20125256)

## 什么是Lambda?

我们知道，对于一个[Java变量](https://www.zhihu.com/search?q=Java%E5%8F%98%E9%87%8F&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A324121308%7D)，我们可以赋给其一个**“值”**。


![](https://pic1.zhimg.com/80/v2-ab6545c49383236a4af3f28a47886090_1440w.jpg?source=1940ef5c)

如果你想把“一块代码”**赋给一个Java变量，应该怎么做呢？

比如，我想把右边那块代码，赋给一个叫做aBlockOfCode的Java变量：


![](https://pica.zhimg.com/80/v2-1cc87e82fba0872c2cae3fee08e8fe41_1440w.jpg?source=1940ef5c)

在Java 8之前，这个是做不到的。但是Java 8问世之后，利用[Lambda特性](https://www.zhihu.com/search?q=Lambda%E7%89%B9%E6%80%A7&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A324121308%7D)，就可以做到了。


![](https://pic3.zhimg.com/80/v2-145a556d86806c3163391a13428e3f03_1440w.jpg?source=1940ef5c)

当然，这个并不是一个很简洁的写法。所以，为了使这个赋值操作更加elegant, 我们可以移除一些没用的声明。


![](https://pic2.zhimg.com/80/v2-a712753b42972e094a548ae02fa82987_1440w.jpg?source=1940ef5c)

这样，我们就成功的非常优雅的把“一块代码”赋给了一个变量。**而“这块代码”，或者说“这个被赋给一个变量的函数”，就是一个Lambda表达式**。

但是这里仍然有一个问题，就是变量aBlockOfCode的类型应该是什么？

在Java 8里面，所有的Lambda的类型都是一个接口，而Lambda表达式本身，也就是”那段代码“，需要是这个接口的实现。这是我认为理解Lambda的一个关键所在，简而言之就是，[Lambda表达式](https://www.zhihu.com/search?q=Lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A324121308%7D)本身就是一个接口的实现。直接这样说可能还是有点让人困扰，我们继续看看例子。我们给上面的aBlockOfCode加上一个类型：


![](https://pic3.zhimg.com/80/v2-55de66060b4cb70193ddc7fea201b257_720w.jpg?source=1940ef5c)

这种只有**一个接口函数需要被实现的接口类型，我们叫它”函数式接口“。**为了避免后来的人在这个接口中增加接口函数导致其有多个[接口函数](https://www.zhihu.com/search?q=%E6%8E%A5%E5%8F%A3%E5%87%BD%E6%95%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A324121308%7D)需要被实现，变成"非函数接口”，我们可以在这个上面加上一个声明@FunctionalInterface, 这样别人就无法在里面添加新的接口函数了：


![](https://pic2.zhimg.com/80/v2-2c57e7411de227d1eb09c327d01fb766_720w.jpg?source=1940ef5c)

这样，我们就得到了一个完整的Lambda表达式声明：

![](https://pic1.zhimg.com/80/v2-02eedc528fcee115f5ed0b7b045846d7_720w.jpg?source=1940ef5c)
  

## Lambda表达式有什么作用?

**最直观的作用就是使得代码变得异常简洁。**

我们可以对比一下Lambda表达式和传统的Java对同一个接口的实现：

![](https://pic2.zhimg.com/80/v2-dbd46cf9d188d0fde25db700c23dcc79_720w.jpg?source=1940ef5c)

这两种写法本质上是等价的。但是显然，Java 8中的写法更加优雅简洁。并且，由于Lambda可以直接赋值给一个变量，**我们就可以直接把Lambda作为参数传给函数, 而传统的Java必须有明确的接口实现的定义，初始化才行：**

![](https://pic3.zhimg.com/80/v2-28606f4328308baf7f70a36bd689e5ea_720w.jpg?source=1940ef5c)

有些情况下，这个接口实现只需要用到一次。传统的Java 7必须要求你定义一个“污染环境”的接口实现MyInterfaceImpl，而相较之下Java 8的Lambda, 就显得干净很多。

#java 

[[静态函数]]

## Lambda结合FunctionalInterface Lib, forEach, stream()，method reference等新特性可以使代码变的更加简洁！
[[Java 的泛型和 C++的模板]]

## Lambda配合`Optional<T>`可以使Java对于null的处理变的异常优雅