Java的接口

在 Java 程序设计语言中，接口不是类，而是对希望符合这个接口的类的一组需求。

“类可以实现接口，表示类的对象具有接口所表示的能力”

“一个类可以实现多个接口，表明类的对象具备多种能力，各个接口之间以逗号分隔”



为什么不用抽象类? 使用抽象类表示通用属性存在一个严重的问题。每个类只能扩展一个类。假设 Enployee 类已经扩展了另一个类，例如 Person，它就不能再扩展第二个类了。

`class Enployee extends Person, Comparable // ERROR
`
但每个类可以实现多个接口，如下所示：

`class Employee extends Person implements Comparable // OK`

有些程序设计语言（尤其是 C++）允许一个类有多个超类。我们将这个特性称为多重继承（multiple inheritance)。[C++](http://c.biancheng.net/cplus/)支持**多继承（Multiple Inheritance）**，**即一个派生类可以有两个或多个基类。**

```cpp
#include <iostream>
u[sin](http://c.biancheng.net/ref/sin.html)g namespace std;

//基类
class BaseA{
public:
    BaseA(int a, int b);
    ~BaseA();
protected:
    int m_a;
    int m_b;
};
BaseA::BaseA(int a, int b): m_a(a), m_b(b){
    cout<<"BaseA constructor"<<endl;
}
BaseA::~BaseA(){
    cout<<"BaseA destructor"<<endl;
}

//基类
class BaseB{
public:
    BaseB(int c, int d);
    ~BaseB();
protected:
    int m_c;
    int m_d;
};
BaseB::BaseB(int c, int d): m_c(c), m_d(d){
    cout<<"BaseB constructor"<<endl;
}
BaseB::~BaseB(){
    cout<<"BaseB destructor"<<endl;
}

//派生类
class Derived: public BaseA, public BaseB{
public:
    Derived(int a, int b, int c, int d, int e);
    ~Derived();
public:
    void show();
private:
    int m_e;
};
Derived::Derived(int a, int b, int c, int d, int e): BaseA(a, b), BaseB(c, d), m_e(e){
    cout<<"Derived constructor"<<endl;
}
Derived::~Derived(){
    cout<<"Derived destructor"<<endl;
}
void Derived::show(){
    cout<<m_a<<", "<<m_b<<", "<<m_c<<", "<<m_d<<", "<<m_e<<endl;
}

int main(){
    Derived obj(1, 2, 3, 4, 5);
    obj.show();
    return 0;
}
```

Java 的设计者选择了不支持多重继承，其主要原因是多重继承会让语言变得非常复杂（如同 C++)，或者效率会降低（如同 Eiffel)。

实际上，接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性。

#java
#cpp