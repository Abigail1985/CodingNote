Object类中包含一个方法名叫`getClass`，利用这个方法就可以获得一个实例的类型类。类型类指的是代表一个类型的类，因为一切皆是对象，类型也不例外，在Java使用类型类来表示一个类型。所有的类型类都是Class类的实例。**`getClass()`会看到返回`Class<?>`。**
[[显式intent的使用]]

`T  bean ;`

`Class<T> bean;`

`Class<?> bean;`

单独的T代表一个类型，而`Class<T>`和`Class<?>`代表这个类型所对应的类

`Class<T>`在实例化的时候，T要替换成具体类

`Class<?>`它是个通配泛型，?可以代表任何类型   

`<? extends T>`受限通配，表示T的一个未知子类。

`<? super T>`下限通配，表示T的一个未知父类。


注意：class是java的关键字, 在声明Java类时使用;

> Class类的实例表示Java应用运行时的类（class ans enum）或接口（interface and annotation）（每个Java类运行时都在JVM里表现为一个Class对象，可通过类名.class，类型.getClass()，Class.forName("类名")等方法获取Class对象）。数组同样也被映射为为Class对象的一个类，所有具有相同元素类型和维数的数组都共享该Class对象。基本类型boolean，byte，char，short，int，long，float，double和关键字void同样表现为Class对象。




#java