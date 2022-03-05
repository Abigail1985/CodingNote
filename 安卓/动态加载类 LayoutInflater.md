通过LayoutInﬂater的from()方法可以**构建出 一个LayoutInflater对象**，然后调用inflate()方法就可以**动态加载一个布局文件**。

LayoutInﬂater类的目的是让程序能够根据设备的分辨率或屏幕大小，在运行时决定加载哪个布局, 用setContentView 的话, 在编译时就已经写死了,没办法动态决定.

LayoutInflater这个类最主要的功能就是实现**将xml表述的layout转化为View的功能**。

[[View和 ViewGroup]]: layout 是 viewgroup 的子类, viewgroup 是 view 的子类
![[控件和布局的继承结构.jpg]]

为了便于理解，我们可以将它与findViewById()作一比较，二者都是实例化某一对象，不同的是findViewById()是找xml布局文件下的具体widget控件实例化，而LayoutInflater找res/layout/下的xml布局文件来实例化的。


