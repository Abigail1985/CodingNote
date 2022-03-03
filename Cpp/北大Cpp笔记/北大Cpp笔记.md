[TOC]

# 预处理指令

https://www.cnblogs.com/zi-xing/p/4550246.html

本文主要记录了C/C++预处理指令，常见的预处理指令如下：

1. \#空指令，无任何效果
2. \#include包含一个源代码文件
3. \#define定义宏
4. \#undef取消已定义的宏
5. \#if如果给定条件为真，则编译下面代码
6. \#ifdef如果宏已经定义，则编译下面代码
7. \#ifndef如果宏没有定义，则编译下面代码
8. \#elif如果前面的#if给定条件不为真，当前条件为真，则编译下面代码
9. \#endif结束一个#if……#else条件编译块
10. \#error停止编译并显示错误信息
#cpp


## 什么是预处理指令?

预处理指令是以#号开头的代码行。#号必须是该行除了任何空白字符外的第一个字符。#后是指令关键字，在关键字和#号之间允许存在任意个数的空白字符。整行语句构成了一条预处理指令，该指令将在编译器进行编译之前对源代码做某些转换。

以前没有在意的学者注意了,预处理指令是在编译器进行编译之前进行的操作.预处理过程扫描源代码，对其进行初步的转换，产生新的源代码提供给编译器。可见预处理过程先于编译器对源代码进行处理。在很多编程语言中，并没有任何内在的机制来完成如下一些功能：在编译时包含其他源文件、定义宏、根据条件决定编译时是否包含某些代码(防止重复包含某些文件)。要完成这些工作，就需要使用预处理程序。尽管在目前绝大多数编译器都包含了预处理程序，但通常认为它们是独立于编译器的。预处理过程读入源代码，检查包含预处理指令的语句和宏定义，并对源代码进行响应的转换。预处理过程还会删除程序中的注释和多余的空白字符。

## 预编译指令

\#ifdef,#ifndef,#endif...

以上这些预编译指令，都是条件编译指令，也就是说，将决定那些代码被编译，而哪些不被编译

**Exemple1**:

```c++
#include <stdio.h>
#include <stdlib.h>
#define DEBUG
int main(void)
{
    int i = 0;
    char c;
    while(1)
    {
        i++;
        c = getchar();
        if('\n' != c)
        {
            getchar();
        }
        if('q' == c || 'Q' == c)
        {
#ifdef DEBUG//判断DEBUG是否被定义了
            printf("We get:%c,about to exit.\n",c);
#endif
            break;
        }
        else
        {
            printf("i = %d",i);
#ifdef DEBUG
            printf(",we get:%c",c);
#endif
            printf("\n");
        }
    }
    printf("Hello World!\n");
    return 0;
}

/*#endif用于终止#if预处理指令。*/
```

**Exemple2**:

```c++
#include <stdio.h>
#define DEBUG
void main()
{
#ifdef DEBUG
    printf("yes ");
#endif
#ifndef DEBUG
    printf("no ");
#endif
}
//#ifdefined等价于#ifdef;
//#if!defined等价于#ifndef
```



# 从C到C++

c++对c扩展主要是为了实现面向对象，但有一些简单的扩展与面向对象关系不大，在这一节中介绍

## 引用

```c++
int n=4;
int &r=n;//r引用了n，r的类型是int
```

- r引用了n，r跟n就是一回事了：**改变n，r也会改变，反之亦然**。

- 定义引用时，一定要将其**初始化**。

- 为什么我们要使用”引用“？？

  1. **可以更方便的交换两个变量值**

  C中的swap函数：

  ```c
  void swap(int *a,int *b)//参数是两个指针
  {
      int tmp;
      tmp=*a;*a=*b;*b=temp;
  }
  int n1,n2;
  swap(&n1,&n2)//传入的参数是两个变量的地址
  ```

  C++中的swap函数：

  ```c++
  void swap(int &a,int &b)
  //参数是两个变量的引用（直接在函数声明中创建）
  {
      int tmp;
      tmp=a;a=b;b=tmp;
  }
  int n1,n2;
  swap(n1,n2);
  ```

  2. **引用作为函数的返回值**

  ```c
  int n=4;
  int &SetValue(){return n;}
  int main()
  {
      SetValue()=40;
      cout<<n;
      return 0;
  }//输出：40
  ```

  

- **常引用**

  不能通过常引用去修改他引用的内容，但他引用的内容可以改变

  ```c++
  int n=100;
  const int &r=n;
  r=200;//编译错误
  n=300;//没有问题
  ```

  

## const关键字

1. 常量
   ```const int r=2```

2. 常量指针
   ```c++
   int n,m;
   const int *p=&n;//p是指向n的常量指针
   *p=5;//不能通过常量指针去修改其指向的内容-->编译出错
   n=4;
   p=&m;//常量指针的指向可以变化-->编译通过
   ```

   ```c++
   const int*p1;
   int *p2;
   p2=p1;//不能把常量指针赋值给非常量指针-->编译错误
   p1=p2;//反过来可以-->编译通过
   p2=(int*)p1;//强制类型转换-->编译通过
   ```

3.常引用



## 动态内存分配

c中的malloc可以实现；

c++中的new可以实现：

1. **分配一个变量**

   ```P=new int``` 

   int：类型名

   P：类型为int*的**指针**

   new将一片大小为sizeof（int）字节的内存空间的**地址**赋值给P。

2. **分配一个数组**

   ```P=new int[N]```

   N:要分配的数组元素的个数

**注意**：new T/new T[N]的返回值都是T*，所以 ```int *p=new  p```是成立的



c++用delete释放被new动态分配的内存空间：

1. **释放一个变量**

   delete必须指向new出来的空间；

   一个内存空间不能被delete两次

2. **释放一个数组**

   ```c++
   int* p=new int[20];
   p[0]=1;
   delete[]p;//不要忘记这个中括号
   ```


### 练习：神秘的数组初始化

```c++
#include <iostream>
using namespace std;

int main()
{
	int * a[] = {NULL,NULL,new int,new int[6]};
	//上面大括号中是初始化的内容
	*a[2] = 123;
	a[3][5] = 456;
	if(! a[0] ) {
		cout << * a[2] << "," << a[3][5];
	}
	return 0;
}
```



## 内联函数和重载函数

**内联函数**

- 函数调用是有时间开销的。如果函数本身只有几条语 句，执行非常快，而且函数被反复执行很多次，相比 之下调用函数所产生的这个开销就会显得比较大。
- 为了**减少函数调用的开销**，引入了内联函数机制。编 译器处理对内联函数的调用语句时，是将整个函数的
  代码插入到调用语句处，而不会产生调用函数的语句。

- 在函数定义前加关键字```inline```，即可定义内联函数

**重载函数**

**一个或多个函数，名字相同，然而参数个数或参数类型不相同，这叫做函数的重载。**
 以下三个函数是重载关系： 

```c
(1)int Max(double f1,double f2){}
(2)int Max(int n1,int n2) {} 
(3)int Max(int n1,int n2,int n3) {}
```

- 函数重载**使得函数命名变得简单** 
-  编译器根据调用语句的中的实参的个数和类型判断应
  该调用哪个函数。

```c++
Max(3.4,2.5); //调用 (1) 
Max(2,4); //调用 (2) 
Max(1,2,3); //调用 (3)
Max(3,2.4);//error,二义性
```



**函数的缺省参数**

- C++中，定义函数的时候可以让最右边的**连续**若干个参 数有缺省值，那么调用函数的时候，若相应位置不写参 数，参数就是缺省值。

  ```c++
  void func( int x1, int x2 = 2, int x3 = 3) { }
  func(10 ) ; //等效于 func(10,2,3) 
  func(10,8) ; //等效于 func(10,8,3)
  func(10, , 8) ; //不行,只能最右边的连续若干个参数缺省
  ```

- 函数参数可缺省的目的在于提高程序的**可扩充性**。 
- 即如果某个写好的函数要添加新的参数，而原先那些 调用该函数的语句，未必需要使用新增的参数，那么 为了避免对原先那些函数调用语句的修改，就可以使用缺省参数。



## 类和对象的基本概念（1）

**结构化程序设计**：

- c使用：```程序=数据结构+算法```

- 不足：结构混乱，没有封装和隐藏的概念，难以debug，不能重用类似的代码。

**面向对象的程序设计**：

- c++使用：```程序=类+类+类+...+类```

- 特点：抽象，封装，继承，多态

  设计方法：

  归纳：将某类客观事物属性归纳出来（用**变量**描述属性）

  抽象：将这类事物能进行的操作归纳出来（用**函数**描述操作）

  封装：将属性和函数捆绑在一起，形成一个**类**

- 通过**类**定义来的**变量**就叫做**对象**，C中的struct也是类，他定义出的变量也叫做对象。
- C++中，可以像使用基本类型```int```，```char```一样使用类

# 类和对象基础

## 类和对象的基本概念（2）

1. 类成员的可访问范围

   private：只能在成员函数内访问（缺省定义）

   public：可以在任何地方访问

   protected：以后再说

   

2. “隐藏”机制的好处：

   **强制对成员变量的访问一定要通过成员函数进行**

   ```c++
   class CEmployee{
       private:
              char szName[30];//名字，sz是char字符串前缀
       public:
              int salary;
              void setName(char *name)
              void getName(char *name)
              void averageSalary(CEmployee e1,CEmployee e2)
   };
   
   void CEmployee::setName(char * name){strcpy(szName,name);} void CEmployee::getName(char * name){strcpy(name,szName);} /*在类的成员函数内部，能够访问当前对象的全部变量/函数*/ 
   void CEmployee::averageSalary(CEmployee e1,CEmployee e2){
       cout<<e1.szName;
       salary=(e1.salary+e2.salary)/2;
       //在类的成员函数内部，能够访问同类其他对象的全部变量/函数
   }
   
   int main(){
       CEemployee e;
       strcpy(e.szName,"Tom123456789");❌❌❌
       //在类的成员函数外部，只能访问该类对象的公有变量/函数，szName是私有变量
       e.setName("Tom");🆗
       e.salary=5000;🆗
       //setName是公有函数，salary是公有变量
       return 0；
   }
   ```

   若将上述程序移植到内存紧张的手持设备上，希望将```char szName[30]```改为```char szName[5]```：

   由于不存在```strcpy(e.szName,"Tom123456789")```这种语句，所以只需要在``setName``中加一个判断语句，当输入多于5个符号时报错即可。

   

3. 成员函数的重载，参数缺省

   - 成员函数可以重载，也可以缺省

   - **使用缺省参数时要注意避免函数重载导致的歧义**

     ```c++
     class location{
         private:int x,y;
         public:void init(int x=0,int y=0)
                void valueX(int val=0){x=val;}
                void valueX(){return x;}
     };
     
     location A;
     A.valueX();❌❌❌
     //编译器无法判断调用哪个valueX
     ```

## 构造函数

1. 什么是构造函数？

   **构造函数名字与类的名字相同**，可以有参数，**不可以**有返回值

   ```c++
   class Complex{
       private:double real,imag;
       public:Complex(double r,double i=0);
       //构造函数名字与类名相同，无返回值——连void都没有！！！
   };
   
   Complex::Complex(double r,double i=0){real=r;imag=i;}
   
   Complex c1；❌
   Complex* ptr_c2=new Complex;❌
   Complex c3(2);🆗//i有缺省值0
   Complex* ptr_c4=new Complex(3,4);🆗//c++中变量名可以含有下划线_
   ```

2. 为什么需要构造函数？

   对象没被初始化就使用会导致程序出错！

   在用类定义对象时，构造函数可以**自动进行初始化**操作（就像ios捷径里的自动化！），不必担心忘记初始化。

3. 如果定义类时没有写构造函数，编译器会自动生成**无参数构造函数**

   ```c++
   class Complex{
       private:double real,imag;
       public:void Set(double r,double i)
       //编译器自动生成默认构造函数
   };
   Complex c1；🆗
   Complex* pc2=new Complex;🆗//c++中指针前缀：p
   ```

4. 可以有多个构造函数，**需要参数个数/类型不同**。(这些构造函数间是重载的关系)

   ```c++
   class Complex{
       private:double real,imag;
       public:Complex(double r,double i)
              Complex(double r);
              Complex(Complex c1,Complex c2);
              void Set(double r,double i);
   };
   
   Complex::Complex(double r,double i){real=r;imag=i;}
   Complex::Complex(double r){real=r;imag=0;}
   Complex::Complex(Complex c1,Complex c2)
   {
       real=c1.real+c2.real;
       imag=c1.imag+c2.imag;
   }
   
   
   Complex c1(3,1),c2(4),c3(c1,c2);
   //c1={3,1},c2={4,0}c3={7,1}
   ```

5. 构造函数在数组中的使用

   ```c++
   class Test{
       public:Test(int n){}//(1)
              Test(int n,int m){}//(2)
              Test(){}//(3)
   };
   
   Test array1[3]={1,Test(1,2)};
   //三个元素分别用(1)(2)(3)初始化
   Test* pArray2[3]={new Test(4),new Test(1,2)};
   //👉两个元素👈分别用(1)(2)初始化
   ```

## 复制构造函数

1. 什么是复制构造函数？

   一种特殊的构造函数，其形式参数必须为**引用**类型

   

2. 如果定义类时没有写，编译器会自动生成**无参数复制构造函数**

3. **复制构造函数被调用时，可以不进行复制工作，见4.2的例子**

4. 什么时候用到复制构造函数？

   - 用一个对象去初始化同类的另一个对象时

     ```c++
     Complex c1;
     Complex c2(c1);
     ```

   - 如果函数有一参数是类A的对象，当该函数被调用时，类A的复制构造函数被调用

     ```c++
     class A{
         public:A(){};//构造函数
                A(A& a){cout<<"Copy constructor called"<<endl;}//复制构造函数
     };
     
     void Func(A a1){}
     
     int main()
     {
         A a2;
         Func(a2);//此时复制构造函数被调用，但并没有把a1变成a2复制品！
         return 0;
     }
     //输出：Copy constructor called
     ```

   - 如果函数的返回值是类A的对象，当该函数返回值时，leiA的复制构造函数被调用

     ```c++
     class A{
         public:int v;
                A(int n){v=n;}
                A(const A& a){
                    v=a.v;//和之前的代码不同，此处进行了复制工作
                    cout<<"Copy constructor called"<<endl;}
     };
     
     A Func(){
         A b(4);//此处使用类A的构造函数，参数是4
         return b;
     }
     
     int main()
     {
         cout<<Func().v<<endl;
         //此处复制构造函数被调用，且将Func().v变成了b的复制品
         return 0;
     }
     //输出：Copy constructor called 4
     ```

5. 对象间赋值并不导致复制构造函数被调用

   > 假设A 是一个类的名字，下面哪段程序不会调用A的复制构造函数？
   >
   > - A. A a1,a2; a1 = a2;
   > - B.void func( A a) { cout << "good" << endl; }
   > - C. A func( ) { A tmp; return tmp; }
   > - D.A a1; A a2(a1);
   >
   > 正确答案：A你选对了

6. 为什么我们要在复制构造函数中使用常量引用参数```A(const A& a)```

   ```c++
   void test(A a){cout<<"Test"<<endl;}
   ```

   这样的函数，调用时生成形参会引发复制构造函数调用，**开销比较大**。 

   👉所以可以考虑使用```CMyclass &``` 引用类型作为参数。
        👉**为了确保实参的值在函数中不被改变**，那么可以加上```const``` 关键字：

   ```c++
   class A{
       public:A(){}
              A(const A& a){}//定义时就使用const
   };
   
   void test(const A& a){cout<<"Test"<<endl;}
   //函数中任何试图改变a值的语句都将是变成非法
   ```

7. **<font color=red>为什么我们要自己写复制构造函数？？？？？</font>**

   👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇



## 类型转换构造函数

1. 什么是类型转换构造函数？

   **只有一个参数**，且这参数**不是引用类型的**，**一般**就是转换构造函数
   
   此类函数目的是实现类型的自动转换



实例：

```c++
#include<iostream>
using namespace std; 

class Complex{
    public:
    double real,imag;
    Complex(int i);//类型转换构造函数
    Complex(double r,double i){real=r;imag=i;}
}; 

Complex::Complex(int i)
{
    cout<<"IntConstructor called"<<endl;
    real=i;imag=0;//此处实现了“将int类型转化为Complex类型”
}

int main()
{
    Complex c1(7,8);
    Complex c2=12;//直接调用类型转换构造函数
    c1=9;//9被自动转换为一个👉临时👈Complex对象
    cout<<c1.real<<","<<c1.imag<<"\n"<<c2.real<<","<<c2.imag<<endl;
    return 0;
}
```

```
IntConstructor called
IntConstructor called
9,0
12,0
```

## 析构函数

1. 析构函数是什么？

   ```c++
   class Ctest{
       public:
       ~Ctest(){cout<<"destructor called"<<endl；}
       //析构函数与类名相同，前有一"~"号
       //析构函数没有参数，没有返回值
       //一个类只能有一个析构函数
   };
   
   int main(){
       Ctest array[2];//生成一个含有两个Ctest类的对象的数组
       cout<<"End Main"<<endl;
       return 0;
       //此时main函数结束，array消亡，其中每个元素的析构函数都被调用
   }
   ```

   ```
   End Main 
   destructor called
   destructor called
   ```

2. 析构函数与delete

   对象：

   ```c++
   Ctest *pTest;
   pTest=new Ctest;//构造函数调用
   delete Ctest;//此时析构函数被调用！！
   ```

   对象数组：

   ```c++
   Ctest *pTest;
   pTest= new Ctest[3];
   delete [] Ctest;//👉必须写[]！👈否则只delete一个对象（调用一次析构函数）！！！！
   ```

3. 析构函数在对象作为函数返回值返回后被调用

   ```c++
   class Ctest{
       public:
       ~Ctest(){cout<<"destructor called"<<endl;}
   };
   
   Ctest Func(Ctest t){return t;}//作为参数的对象t消亡时，调用析构
   
   int main(){
       Ctest t1;
       t1=Func(t1);//函数返回的临时对象Func(t1)被用过后，调用析构
       return 0;//main函数结束t1消亡，调用析构
   }
   ```

   ```c++
   destructor called
   destructor called
   destructor called
   ```



## 构造/析构函数什么时候被调用？

```c++
class Demo{
    int id;
    public:
         Demo(int i);//类型转换构造函数
        ~Demo();//析构函数
};

Demo::Demo(int i){
    id=i;
    cout<<"id="<<id<<"constructed"<<endl;}
Demo::~Demo(){
    cout<<"id="<<id<<"destructed"<<endl;}
```

```c++
Demo d1(1); //d1定义在这里，说明这是一个全局对象

void Func() {
    static Demo d2(2); 
    Demo d3(3); 
    cout << "func" << endl;
} 

int main () {
    Demo d4(4); 
    d4 = 6; //这个语句成立是因为有类型转换构造函数
    cout << "main" << endl;
    {Demo d5(5);}//d5定义在花括号里，说明这是一个局部对象
    Func(); 
    cout << "main ends" << endl; 
    return 0;
}
```

输出：

```c++
id=1constructed
id=4constructed
id=6constructed
id=6destructed
main
id=5constructed
id=5destructed
id=2constructed
id=3constructed
func
id=3destructed
main ends
id=6destructed
id=2destructed
id=1destructed
```



# 类和对象提高

## This指针

1. **为什么引入This指针？**

   - 刚开始没有c++编译器，需要把c++翻译成c用c编译器编译。

   - 由于**c++的成员函数在c中并没有对应概念**，因此发明了This指针，该指针可以**指向成员函数作用的对象**，能将一个**全局函数转变为成员函数**

     ![](2020-10-09-12-18.png)

   

2. **This指针在c++中的作用**

   **非静态**成员函数中可以直接使用this来代表<font color=red>指向该函数作用的对象的指针</font>

   ```c++
   class A {
               int i; 
       public: void Hello(){ cout << "hello" << endl; }
   }; //用this翻译：void Hello(A*this){ cout << "hello" << endl; }
   
   int main() {
       A * p = NULL;
       p->Hello(); //用this翻译：Hello(p);
   }//结果会怎样？输出：hello
   ```

   ```c++
   class A {
               int i; 
       public: void Hello(){ cout << i << "hello" << endl; }
   }; //用this翻译：void Hello(A*this){ cout << this->i << "hello" << endl;}
      
   //this若为NULL，则出错！！
   int main() {
        A * p = NULL; 
        p->Hello();//用this翻译：Hello(p);
   } // 结果会怎样？编译出错！！！！！
   ```

## 静态成员变量和静态成员函数

1. 定义和性质

   - 普通成员变量每个对象各自有一份，静态成员变量一共就一份，所有对象共享

   - sizeof运算符不会计算静态成员变量

     ```c++
     class CMyclass { 
         int n; 
         static int s;
     };
     //sizeof(CMyclass)=4
     ```

   - 普通成员函数必须具体作用于某个对象，静态成员函数**不具体作用于某个对象**

   - **静态成员不需要通过对象就能访问**

   - 静态成员变量本质上是全局变量，哪怕一个对象都不存在，类 的静态成员变量也存在。

   - 静态成员函数本质上是全局函数。

   - 设置静态成员这种机制的目的是**将和某些类紧密相关的全局变量和函数写到类里面，看上去像一个整体，易于维护和理解。**

     

2. 如何访问静态成员

   - **类名::成员名**``CRectangle::PrintTotal()``

   - **对象名.成员名**``CRectangle r;r.PrintTotal()``

   - **指针->成员名**``CRectangle* p;p->PrintTotal()``

   - **引用.成员名**``CRectangle& ref;int n=ref.nTotalNumber;``

     <font color=red>虽然后三个的形式与普通成员变量相同，但并不意味着该成员是属于那个对象的，要判断静态还是普通需要看成员的定义</font>

     

3. 静态成员实例

   考虑一个需要随时知道矩形总数和总面积的图形处理程序
   可以用全局变量来记录**总数和总面积**
   用静态成员将这两个变量封装进类中，就更容易理解和维护

   ```c++
   class CRectangle
   {
       private:
              int w,h;
              static int nTotalArea;
              static int nTotalNumber;
       public:
             CRectangle(int w_,int h_);
            ~CRectangle();
             static void PrintTotal();
             CRectangle(CRectangle & r )//复制构造函数
   };
   ```

   ```c++
   CRectangle::CRectangle(int w_,int h_) {
       w = w_; 
       h = h_; 
       nTotalNumber ++; 
       nTotalArea += w * h;
   }
   CRectangle::~CRectangle() {
       nTotalNumber --; 
       nTotalArea -= w * h;
   }
   void CRectangle::PrintTotal() {
       cout << nTotalNumber << "," <<
   }
   CRectangle::CRectangle(CRectangle & r ) {
       w = r.w;
       h = r.h;
       nTotalNumber ++;
       nToTalArea+=w*h;
   }/*💢💢💢不要忘了定义复制构造函数！！！！！
   
   👉在使用CRectangle类时，有时会调用复制构造函数 生成临时的隐藏的CRectangle对象：
   
       调用一个以CRectangle类对象作为参数的函数时， 
       调用一个以CRectangle类对象作为返回值的函数时
       
   👉临时对象在消亡时会调用析构函数，减少nTotalNumber 和 nTotalArea的值，可是这些临时对象在生成时却没有增加nTotalNumber 和 nTotalArea的值。
   ```

   ```c++
   int CRectangle::nTotalNumber = 0; 
   int CRectangle::nTotalArea = 0; 
   // 必须在定义类的文件中对静态成员变量进行一次说明或初始化。否则编译能通过，链接不能通过。 
   int main() {
       CRectangle r1(3,3), r2(2,2); 
       /*cout << CRectangle::nTotalNumber;  👉Wrong , 私有*/
       CRectangle::PrintTotal(); 
       r1.PrintTotal(); //虽然这样写，但不代表PrintTotal是普通成员函数
       return 0;
   }
   ```

   Output:

   ```c++
   2,13
   2,13
   ```

   

4. **静态成员函数不能访问非静态成员变量，不能调用非静态成员函数**

   ```c++
   void CRectangle::PrintTotal() {
        cout << w << "," << nTotalNumber << "," << nTotalArea << endl; //wrong
   }
   CRetangle::PrintTotal(); 
   //解释不通，w 到底是属于那个对象的？
   //同理，非静态成员函数会访问非静态成员变量，调用了非静态函数就等于访问了非静态变量
   ```

   

## 成员对象和封闭类

- 有**成员对象**的类称之为**封闭类（enclosing）**

- 任何生成封闭类对象的语句，都要让编译器知道封闭类中的成员对象如何初始化，所以我们一定要添加**封闭类构造函数的初始化列表**

  ![](2020-10-09-12-20-16.png)

  **例子**：

  > ```cpp
  > class Ctyre{
  >       private:
  >               int radius;
  >               int width;
  >       public:CTyre(int r,int w):radius(r),width(w){}
  >       //上方的构造函数添加了初始化列表，将 radius 初始化成 r，width 初始化成 w。这种写法比在函数体内用 r 和 w 对 radius 和 width 进行赋值的风格更好。建议对成员变量的初始化都使用这种写法
  > };
  > 
  > 
  > class Cengine{};
  > 
  > class Ccar{
  >       private:
  >               int price；//成员变量
  >               Cengine engine;//成员对象
  >               Ctyre tyre;//成员对象
  >       public:
  >               CCar(int p,int tr,int tw);//Ccar的初始化函数
  > };
  > CCar::CCar(int p,int tr,int tw):price(p),tyre(tr,w){};
  > //:和{之间的内容即为Ccar的构造函数的初始化列表，如果没有这列表，“CCar car”这句话就会编译出错，因为tyre的构造函数需要参数，但那句话并没有给出参数，因此无法构造tpre，因此无法构造car
  > 
  > int main(){
  >     Ccar car(20000,17,225);
  >     return 0;
  > }
  > ```
  >
  > 详细解释看这篇文章：http://c.biancheng.net/view/167.html



- 封闭类对象生成时，先执行成员对象的构造函数，在执行封闭类的成员函数

  👇👇👇

  对象成员构造函数的顺序与其**在封闭类中的说明顺序一致**

  👇👇👇

  封闭类对象消亡时，先执行封闭类的析构函数，再执行成员对象的析构函数

  👉👉👉**<font color=red>就像汽车一样，造汽车时先造轮胎和引擎，再组装汽车；拆汽车时先把汽车拆成零件，再去把零件拆散</font>**

  

- 封闭类对象如果用默认复制构造函数初始化，那么其成员对象也会用复制构造函数初始化

  **例子**：

  > ```cpp
  > class A{
  > public:A(){cout << "default" << endl;}
  >        A(A& r){cout << "copy" << endl;}
  > };
  > 
  > class B{A a;};
  > 
  > int main(){
  >  B b1;
  >  B b2(b1);
  >  return 0;
  > }
  > ```
  >
  > Output：
  >
  > ```c++
  > default
  > copy//说明b2.a是用类A的复制构造函数初始化的，而且调用复制构造函数时的实参就是b1.a
  > ```

  

## 常量成员函数和常量对象

- **常量对象**

  如果**不希望某个对象的值被改变**，则定义该对象时可以在**前面**加const关键字

  ```c++
  class Demo{...}
  const Demo Obj;//常量对象
  ```

- **常量成员函数**

  如果**不希望成员函数执行期间修改其作用的对象**，则定义该函数时可以在**后面**加const关键字

  ❗❗❗<font color=red>常量成员函数不能使用普通函数/变量，可以使用静态函数/成员，因为静态函数/成员不具体作用于任何对象</font>

  ```cpp
  class Sample
  {
      public:
          int value;
          void GetValue() const;//常量成员函数
          void func(){};
          Sample(){};
  };
  
  void Sample::GetValue()const
  {
      value=0;//❌❌❌不能访问普通成员变量
      func();//❌❌❌不能调用普通成员函数
  }
  ```

  ```cpp
  int main()
  {
      const Sample o;
      o.value=100;//❌常量对象不能被修改
      o.func();//❌常量对象上不能执行非常量成员函数，因为电脑不知道那函数会不会改变这个对象
      o.GetValue();//✅常量对象上可以执行常量成员函数
  }
  ```

<font color=red>**注意：两个成员函数，名字和参数表一样，但一个是const一个不是，算重载**</font>

![](2020-10-09-12-21-10.png)


- **常引用**

  用对象的引用作为参数，不必调用复制构造函数，又比指针做参数好看

  ```cpp
  class Sample{...};
  void PrintfObj(const Sample& o){...}
  //这样函数中能确保不会无意间更改o的值
  ```

  

## 友元（friends）


- 友元这个机制算是对c语言程序员的一种妥协，为关系相近的类提供了一种调用函数的方便。友元函数不属于该类的成员函数，他是定义在类外的普通函数，只是在类中声明该函数可以直接访问类中的private或者protected成员

- **友元函数**：一个类的友元函数可以访问该类的私有成员

  ```cpp
  class CCar；//提前声明CCar类，以便后面的CDriver使用
  
  class CDriver
  {
      public:void ModifyCar(CCar* pCar);
      //'改装汽车'函数的参数是一个指向CCar类对象的指针，这里就用到了CCar类
  };
      
  class CCar
  {
      private:
              int price;
      friend void CDriver::ModifyCar(CCar* pCar);
      //可以将一个类A的成员函数(包括构造/析构函数)说明为另一个类B的友元：friend_返回值_A::func()
      friend int MostExpensiveCar(CCar cars[],int total);
      //上面两函数是CCar类的友元函数，可以访问CCar的私有变量price
  };
  //定义第一个友元函数
  void CDriver::ModifyCar(CCar* pCar)
  {
      pCar->price+=1000;//汽车改装后价值增加
  }
  //定义第二个友元函数
  int MostExpensiveCar(CCar cars[],int total)
  {
      int tmpMax=-1;
      for(int i=0;i<total;++i)
          if(cars[i].price>tmpMax)
              tmpMax=cars[i].price;
      return tmpMax;
  }
  
  int main(){return 0;}
  ```

- 友元类：一个类的友元类可以访问该类的私有成员

  ```cpp
  class CCar
  {
      private:int price;
      friend class CDriver;//声明CDriver为友元类
  };
  
  class CDriver
  {
      public:
             CCar myCar;
             void ModifyCar(){myCar.price+=1000;}
             //因CDriver是CCar的友元类，故可以访问CCar的私有成员       
  };
  
  int main(){return 0;}
  ```

- 友元类之间的关系不能传递，不能继承

  

# 运算符重载

## 运算符重载的基本概念

1. 为什么引入运算符重载？

   在数学上，两个复数可以直接进行+、-等运算。但在C++中，直接将+或-用于复数对象是不允许的。有时会希望，**让对象也能通过运算符进行运算**。这样代码更简洁，容易理解。

   例如：

   > complex_a和complex_b是两个复数对象； 
   >
   > 求两个复数的和, 希望能直接写：
   > complex_a + complex_b

   

2. 运算符重载是什么？

   - 运算符重载，就是对已有的运算符(C++中预定义的运算符)赋予多重的含义，使同一运算符作用于不同类型的数据时导致不同类型的行为。

   - 运算符重载的目的是：扩展C++中提供的运算符的适用范围，使之能作用于对象。

   -  同一个运算符，对不同类型的操作数，所发生的行为不同。 

     > complex_a + complex_b =新的复数对象
     >
     > 5 + 4 = 9

3. 运算符重载的形式？

   - 本质是函数重载

   - 可以重载为**普通函数**，也可以重载为**成员函数** 

   -  把含运算符的表达式转换成对运算符函数的调用。 

   - 把运算符的操作数转换成运算符函数的参数。

   - 运算符被多次重载时，根据实参的类型决定调用哪个运算符函数。

     ```c++
     返回值类型 operator 运算符 (形参表)
     {
         ...
     }
     ```

     



**例子**：

```cpp
class Complex
{
    public:
        double real,imag;
    Complex(double r=0.0,double r=0.0):real(r),imag(i){}
    //上方的构造函数添加了初始化列表，将real初始化为r，将imag初始化为i，这种风格比在函数体内直接用r，i赋值的风格更好
};

//重载成普通函数，参数个数为运算符目数
Complex operator+(const Complex& a,const Complex& b)
{return Complex(a.real+b.real,a.imag+b.imag);}
//返回一个Complex类的临时对象

//重载成成员函数，参数个数为运算符目数-1,因为此时一个参数已经确定了，就是这个重载归属的那个类定义的对象
Complex Complex::operator-(const Complex& c)
{return Complex(real-c.real,imag+c.imag);}
//返回一个Complex类的临时对象

int main()
{
    Complex a(4,4),b(1,1),c;
    c=a+b;
    //等价于c=operator+(a,b);
    cout<<c.real<<","<<c.imag<<endl;
    cout<<(a-b).real<<","<<(a-b).imag<<endl;
    //a-b等价于a.operator-(b)，operator-被固定的那个变量就是对象a
    return 0;
}
```

Output:

```cpp
5,5
3,3
```

小结：

1. 重载成普通函数，参数个数为运算符目数；

   重载成成员函数，参数个数为运算符目数-1

2. ``c=a+b``等价于``c=operator+(a,b)``;

   ``a-b``等价于``a.operator-(b)``



## 赋值运算符的重载

### 为什么引入赋值运算符重载？

**有时候希望赋值运算符两边的类型可以不匹配** 

比如，把一个int类型变量赋值给一个Complex对象， 或把一个 char * 类型的字符串赋值给一个字符串对象,此时就需要重载赋值运算符“=”。

<font color=red>**赋值运算符”=“只能重载为成员函数**</font>

**例子**

```cpp
class String
{
    private:char* str;
    public:String():str(new char[1]){str[0]=0;}
           //上面的构造函数添加了初始化列表，which new了一个只有一个元素的字符串数组，然后用这个数组的地址初始化str。在构造函数中往str写入一个0。最终str指向一个空字符串
           const char* c_str(){return str;};
           //上面的成员函数没有参数，返回一个指向常量字符串的指针，也就是str
           String& operator=(const char* s);
           //将“=”重载为读取一个指向char型数据的指针，返回一个String类临时对象的引用
           String::~String(){delete[]str;} 
           //由于str指向的字符串数组是被new出来的，所以删除时必须使用delete[]
};

//下面的重载是为了使得obj="hello"能够成立
String& String::operator=(const char* s)
{
    delete[]str;
    //先删除对象String中变量str原本指向的字符串数组
    str=new char[strlen(s)+1];
    //初始化str，令str指向一个new出来的字符串数组，该数组大小为“=”参数数组长度+1
    strcpy(str,s);
    //上上句新建好str后，这句把s的内容拷贝到了str里面
    return *this;
    //返回这个成员函数作用的对象String的引用
}

int main()
{
    String s;
    s="Good Luck,";//等价于s.operator=("Good Luck,");
    cout<<s.c_str()<<endl;
    //String s2="hello!";
    //这句话不注释掉就会出错，因为这句话不是赋值语句，而是初始化语句，会调用构造函数，但我们之前的构造函数不接受参数
    s="Shenzhou 8!";
    cout<<s.c_str()<<endl;
    return 0;
}
```

Output:

```cpp
Good Luck,
Shenzhou 8!
```



### 浅拷贝和深拷贝

```cpp
class String
{
    private:char* str;
    public:String():str(new char[1]){str[0]=0;}
           const char* c_str(){return str;};
           String& operator=(const char* s);
           String::~String(){delete[]str;} 
};

String& String::operator=(const char* s)
{
    delete[]str;
    str=new char[strlen(s)+1];
    strcpy(str,s);
    return *this;
}
```

还是这个例子，但此时我们想要实现：

> String S1，S2；
>
> S1=“this”；
>
> S2=“that”；
>
> S1=S2；

如果不改变上面的代码(也就是**浅拷贝**)，实际执行情况是下面这样的：

![](2020-10-09-12-21-54.png)

这导致以下几种问题：

- 如不定义自己的赋值运算符，那么S1=S2实际上导致 **S1.str和 S2.str 指向同一地方**。原先S1指向的地方无法删除被浪费掉。
- 如果S1对象消亡，析构函数将释放 S1.str指向的空间，则S2消亡时还 要释放一次，但一片被new出来的空间只能被delete一次。
- 另外，如果执行 S1 = "other"；会导致S2.str指向的地方被delete

因此要在 class String里添加成员函数:

```cpp
String& operator=(const String& s)
{
    delete[]str;
    str=new char[strlen(s.str)+1];
    strcpy(str,s.str);
    return *this;
}
```

但是这样还不够，考虑下面的语句

> String s; 
>
> s = "Hello";
>
> s = s;

如果等号两边的对象一样，=应该什么都不做。所以重载“=”应为：

```cpp
String& operator=(const String& s)
{
    if(this==&s)
        return *this；
        
    delete[]str;
    str=new char[strlen(s.str)+1];
    strcpy(str,s.str);
    return *this;
}
```

上面的重载即实现了**深拷贝**。

扩展：https://www.zhihu.com/question/36370072/answer/67181275

> 那么如果原来的物体销毁了，但是现在拷贝的物体还在，那么这时候你拷贝后的物体的成员指针就是一个悬挂指针，指向了不再存在的物体，那么你访问的话，那就不知道会发生什么了。
>
> 而对于深拷贝，这一个勤奋的人，他不会只做表面，他会把每一个细节都照顾好。于是，当他遇到指针的时候，他会知道new出来一块新的内存，然后把原来指针指向的值拿过来，这样才是真正的完成了克隆体和原来的物体的完美分离，如果物体比作人的话，那么原来的人的每一根毛细血管都被完美的拷贝了过来，而绝非只是表面。所以，这样的代价会比浅拷贝耗费的精力更大，付出的努力更多，但是是值得的。当原来的物体销毁后，克隆体也可以活的很好。



### 对operator=返回值的讨论

对运算符进行重载的时候，好的风格应该**尽量保留运算符原本的特性**

- 返回值为什么不能是void？

  ``a=b=c``等价于``a.operator=(b.operator=(c))``

  若``b.operator=(c)``返回值为void，则a=void，不可

- 返回值为什么不能是String？

  ``(a=b)=c``等价于``(a.operator=(b)).operator=(c)``

  若``a.operator=(b)``返回值是a，下一步就会让a=c的值。也就是这句话先让a=b的值，再让a=c的值，最终b并没有等于a和c，不可

  

### 复制构造函数的相同困境

为 String类编写复制构造函数的时候，会面临和 = 同样的问 题，用同样的方法处理。

```cpp
String( String & s) {
     str = new char[strlen(s.str)+1];              strcpy(str,s.str);
}
```



## 运算符重载为友元函数

1. 为什么要将运算符重载为友元？

   有时，重载为成员函数不能满足使用要求，重载为普通函数，又不能访问类的私有成员，所以需要将运算符重载为友元。

   **例子**：

   ```c++
   class Complex {
        double real,imag; 
        public: Complex( double r, double i):real(r),imag(i){ };              Complex operator+( double r );
   };
   Complex Complex::operator+( double r ) 
   { //能解释 c+5 
       return Complex(real + r,imag);
   }
   ```

    经过上述重载后：

   > Complex c ; 
   >
   > c = c + 5; //有定义，相当于 c = c.operator +(5); 
   >
   > c = 5 + c; //编译出错

   

   所以，为了使得上述的表达式能成立，需要将 + 重载为普通函数。

   ```cpp
   Complex operator+ (double r,const Complex & c) 
   {
   //能解释 5+c 
       return Complex( c.real + r, c.imag);
   }
   ```

   但是普通函数又不能访问私有成员，所以，需要将运算符 + 重载为友元。

   ```cpp
   class Complex {
       double real,imag; 
       public:
              Complex( double r, double i):real(r),imag(i){ };              Complex operator+( double r ); 
       friend Complex operator + (double r,const Complex & c);
   };
   ```

   

## 实例：可变长数组的实现

要编写可变长整型数组类，使之能如下使用

```cpp
int main()
{
    CArray a;//开始时数组是空的
    for(int i=0;i<5;++i)
        a.push_back(i);
    //❗要用动态分配的内存来存放数组元素，需要一个指针成员变量

    CArray a2,a3;
    a2=a;
    //❗要重载“=”
    for(int i=0;i<a.length();++i)
        cout<<a2[i]<<" ";
    //❗要重载中括号[]
    a2=a3;//a2变成空的
    for(int i=0;i<a2.length();++i)//此时a2.length返回0
        cout<<a2[i]<<" ";
    cout<<endl;
    
    a[3]=100;//将数组a的第三个数改为100
    CArray a4(a);
    //❗要自己写一个复制构造函数，不能用缺省的
    for(int i=0;i<a4.length();++i)
        cout<<a4[i]<<" ";
    
    return 0;
}
```

Output:

```cpp
0 1 2 3 4 
0 1 2 100 4
```

**该怎么写这个数组类？？**

```cpp
class CArray
{
    int size;//数组元素的个数
    int* ptr;//指向动态分配的数组
    public
        CArray(int s=0);//构造函数，s代表数组元素的个数
        CArray(CArray& a);//复制构造函数
        ~CArray();//析构函数
        void push_back(int v);//用于在数组尾部添加一个元素v
        CArray& operator=(const CArray& a);//用于数组对象间的赋值
        int length(){return size;}//返回数组元素个数
        
        int& CArray::operator[](int i){return ptr[i];}
        //用于支持根据下标访问数组元素，如n=a[i]和a[i]=4这样的语句
        //❗❗❗对于返回值的解释，看下面解释
}
```

对于``int& CArray::operator[](int i){return ptr[i];}``要注意：

**返回值类型必须是``int&``，不能是``int``！！！<font color=red>这是因为如果一个函数的返回值不是引用，不能将它写在等号左边</font>，所以``a[i]=4``这句话将编译出错**



```cpp
/**********************构造函数**********************************/
CArray::CArray(int s):size(s)
//这个初始化列表用s初始化size，s的缺省值是0(即不给参数时使用的s值)
{
    if(s==0)
        ptr=NULL;
    else
        ptr=new int[s];
}

/**********************复制构造函数*******************************/
CArray::CArray(CArray& a)
{
    //如果a.ptr指向空数组，就令ptr指向空数组
    if(!a.ptr){
        ptr=NULL;
        size=0;
        return;
    }
    //如果a.ptr指向非空数组，就创建一个同样大小的空间复制上a.ptr的内容并将地址赋给ptr
    ptr=new int[a.size];
    memcpy(ptr,a.ptr,sizeof(int)*a.size);
    size=a.size;
}//上面这个复制构造函数完成了深拷贝的工作


/**********************析构函数**********************************/
CArray::~CArray()
{
    if(ptr)
        delete[]ptr;
}


/**********************“=”的重载函数*****************************/
//赋值号的作用是使“=”左边对象里存放的数组，大小和内容都和右边的对象一样
CArray& CArray::operator=(const CArray& a)
{
    if(ptr==a.ptr)//防止a=a这样的赋值出错
        return *this;
    if(a.ptr==NULL){//如果a里面的数组是空的
        if(ptr)//如果ptr指向的数组不是空的
            delete[]ptr;
        ptr=NULL;
        size=0;
        return *this;
    }
    if(size<a.size){//如果原有空间不够用，就需要分配新的空间
        if(ptr)
            delete[]ptr;
        ptr=new int[a.size];
    }
    memcpy(ptr,a.ptr,sizeof(int)*a.size); 
    //如果原有空间够大，就不分配新的空间
    size=a.size;
    return *this;
}

/**********************push_back函数*****************************/
void CArrary::push_back(int v)
{
    /*下面做分配空间的工作*/
    if(ptr){//原数组不空
        int* tmpPtr=new int[size+1];//创造一个比原数组多一个空间的数组
        memcpy(tmpPtr,ptr,sizeof(int)*size);//拷贝原数组到tmpPtr里
        delete[]ptr;//原数组被复制好后就可以删除释放空间
        ptr=tmpPtr;//现在的ptr指向的空间比原来的大1
    }
    else//原数组是空的
        ptr=new int[1];
    
    /*下面做加入新的数组元素的工作*/
    ptr[size++]=v;
}
```



## 流插入运算符和流提取运算符的重载

### 流插入运算符(左移运算符)：<<

cout是在iostream中定义的，**ostream类的对象**。“<<”能用在cout上是因为再iostream中对“<<”进行了重载。

1. **ostream类中对<<的重载（头文件中别人已经写好的代码）**

   > 考虑怎么重载才能使得下列语句成立：

   > cout<<5;
   >
   > cout<<"this";
   >
   > cout<<5<<"this";

   按照以下方式重载成**ostream类的成员函数**，**返回值是ostream类的引用**

   ```cpp
   ostream& ostream::operator<<(int n)
   {
       ...//输出n的代码
       return *this；
   }
   
   ostream& ostream::operator<<(const char* s)
   {
       ...//输出s的代码
       return *this；
   }
   ```

   ``cout<<5<<"this"``等价于``cout.operator<<(5).operator<<("this")``



2. **将<<重载为全局函数(需要自己写的）**

   > 假定下面程序输出为5hello，我们该如何补写？
   >
   > ```cpp
   > class CStudent{ 
   >     public:int nAge; 
   > };
   > int main()
   > { 
   >     CStudent s ; 
   >     s.nAge = 5; 
   >     cout << s <<"hello"; 
   >     return 0;
   > }
   > ```

   ```cpp
   ostream& ostream<<(ostream& o,const CStudent& s)
   {
       o<<s.nAge;
       return o;
   }
   ```

   正如：

   > 重载成普通函数，参数个数为运算符目数；
   >
   > 重载成成员函数，参数个数为运算符目数-1

   <<被重载成全局函数，第一个参数就是``cout``，因此第一个参数类型必须为``ostream``或``ostream&``

   由于我们需要继续输出“hello”，因此返回值必须为``cout``，故返回值类型为``ostream&``

   

3. **将<<重载为全局函数，且定义成相关类的友元函数（需要自己写的）**

   这样可以访问指定类的私有成员

   > 假定c是Complex复数类的对象，现在希望 写``cout << c;``，就能以``a+bi``的形 式输出c的值，写``cin>>c;``，就能从键 盘接受``a+bi``形式的输入，并且使得
   > ``c.real = a,c.imag = b``
   >
   > ```cpp
   > int main()
   > {
   >  Complex c;
   >  int n;
   >  cin>>c>>n;
   >  cout<<c<<","<<n;
   >  return 0;
   > }
   > ```
   >
   > 示例输入/输出
   >
   > ```cpp
   > input:13.2+133i 87
   > output:13.2+133i,87
   > ```

   

   我们编写Complex类如下：

   ```c++
   #include<iostream>
   #include<string>
   #include<cstdlib>
   using namespace std;
   class Complex{
       double real,imag;
       public:
              Complex(double r=0,double i=0):real(r),imag(i){};
       friend ostream& operator<<(ostream& os,const Complex& c);
       friend istream& operator>>(istream& is,const Complex& c);
       //上面语句将<<,>>重载为Complex类的友元，可以访问Complex类的私有成员real，imag
   };
   
   /****************对<<的重载***********************************/
   ostream& operator<<(ostream& os,Complex& c)
   {
       os<<c.real<<"+"<<c.imag<<"i";//以“a+bi”的形式输出
       return os;
   }
   
   /****************对>>的重载***********************************/
   istream& operator>>(istream& is,Complex& c)
   {
       //将“a+bi”作为字符串读入，“a+bi”中间不能有空格
       string s;
       is>>s;
       
       //确定实部和虚部的分界点
       int pos=s.find("+",0);
       
       //分离出代表实部的字符串
       string sTmp=s.substr(0,pos);
       c.real=atof(sTmp.c_str());
       //atof库函数能将const char*指针指向的内容转换成float
       
       //分离出代表虚部的字符串
       sTmp=s.substr(pos+1,s.length()-pos-2);
       c.imag=atof(sTmp.c_str());
       
       return is;
   }
   ```

   ❗❗❗``c_str()``：

   该函数返回一个指向正规C字符串的指针常量, 内容与本string串相同。 这是为了与c语言兼容，在c语言中没有string类型，故必须通过string类对象的成员函数c_str()把string 对象转换成c中的字符串 

## 类型转换运算符的重载

```cpp
#include<iostream>
using namespace std;
class Complex
{
    double real,imag;
    public:
        Complex(double r=0,double i=0):real(r),imag(i){};
        operator double(){return real;}
        //重载了 强制类型转换运算符 double
};

int main()
{
    Complex c(1.2,3.4);
    
    /*显式转换*/
    cout<<(double)c<<endl;//输出1.2
    
    /*隐式转换*/
    double n=2+c;//等价于double n=2+c.operator double()
    cout<<n;//输出3.2
}
```

## 自增自减运算符的重载

1. 如何将前置/后置的++，--区分开？

   自增运算符++、自减运算符--有前置/后置之分，为了区分所重载的是前置运算符还是后置运算符，C++规定：

   - 前置运算符作为**一元运算符**重载

     - 重载为**成员函数**
        ```cpp
        T& operator++()
        T& operator--()
        ```
     - 重载为**全局函数**
        ```cpp
        T1& operator++(T2);
        T1& operator--(T2);
        //重载为全局函数时需要的参数个数比成员函数时多一个
        ```

   - 后置运算符作为**二元运算符**重载,**要多写一个没用的参数**

     - 重载为**成员函数**
        ```cpp
        T operator++(int);
        T operator--(int);
        ```
     - 重载为**全局函数**
        ```cpp
        T1 operator++(int,T2);
        T1 operator--(int,T2);
        //重载为全局函数时需要的参数个数比成员函数时多一个
        ```

2. 重载运算符的返回值
    - 重载的原则:**对运算符的重载要尽量维持运算符原本的属性**
    - c++中内置的++a返回值是**a的引用**, a++返回值是临时变量**a**
    这也是为什么可以有``(++a)=1``,但不能有``(a++)=1``,(函数的返回值如果不是引用,不能放在等好的左边)
    - 为了维持上面那种性质,前置运算符的返回值是对象,后置运算符的返回值是临时变量

**例子**:
```cpp
int main()
{
    CDemo d(5);
    cout<<(d++)<<",";
    cout<<d<<",";

    cout<<(++d)<<",";
    cout<<d<<"endl";

    cout<<(d--)<<",";
    cout<<d<<",";

    cout<<(++d)<<",";
    cout<<d<<"endl";
    return 0;
}
```
要求输出结果为
>5,6,7,7
7,6,5,5

该如何编写CDemo?

```c++
class Demo
{
    private:
        int n;
    public:
        CDemo(int i=0):n(i){}//初始化列表用i初始化n
        operator int(){return n;}s.int()
        //强制类型转换运算符的重载,使得(int)s等价于s.int()
        //类型强制转换运算符重载时不能写返回值类型,实际上其返回值类型就是该运算符代表的类型
        
        CDemo& operator++();//前置成员
        CDemo operator++(int)//后置成员
 friend CDemo& operator--(CDemo&);//前置全局
 friend CDemo operator--(CDemo&,int);//后置全局
};
/*************************++a重载为成员函数*********************************/
CDemo& CDemo::operator++()
{
    n++;//这个n是operator++()作用的那个对象的私有变量n
    return *this;//返回修改后的对象的引用
}
//++s等价于s.operator++()

/*************************a++重载为成员函数*********************************/
CDemo CDemo::operator++(int k)//k是一个没用的参数
{
    CDemo tmp(*this);//用复制构造函数构造一个临时对象,将修改前的对象的n值赋给他
    n++;
    return tmp;//返回修改前的对象
}//s++等价于s.operator++(0)

/*************************--a重载为全局函数*********************************/
CDemo& operator--(CDemo& d)//对一个全局函数,传进来的参数必须是引用才能修改他的值
{
    d.n++;
    return d;
}//--s等价于operator--(s)

/*************************a--重载为全局函数*********************************/
CDemo operator--(CDemo& d)
{
    CDemo tmp(d);
    n++;
    return tmp;
}//s--等价于operator--(s,0)
```

## 注意事项
1. C++不允许定义新的运算符 ；
2. 重载后运算符的含义应该符合日常习惯:
   - complex_a + complex_b 
   - word_a > word_b
   - date_b = date_a + n
3. 运算符重载不改变运算符的优先级；
4. 以下运算符不能被重载：“.”、“.*”、“::”、“?:”、sizeof； 
5. 重载运算符()、[]、->或者赋值运算符=时，运算符重载函数必须声明为
类的成员函数。

# 继承

- 继承:

  - 在定义一个新的类B时,如果该类与某个已有的类A相似(指的是B拥有A的全部特点),那么可以把A作为一个**基类**而把B作为基类的一个**派生类**

  <font color=red>这是为了避免重复定义相似的类的麻烦</font>

  

- 派生类的性质:

  - 派生类中可以添加新的成员变量和成员函数

  - 派生类一经定义可以独立使用

  - 派生类拥有基类的全部成员**(但是依旧不能访问private)**



- 例子-学生管理系统

  ```c++
  class CStudent{
      private:
      string sName;
      int nAge;
      
      public:
      bool IsThreeGood(){};
      void SetName(const string &name)//&表示引用
      {sName=name;}
  };
  
  //派生类的写法:类名:public基类名
  class CundergraduateStudent:public CStudent{
      private:
      int nDepartement;
      
      public:
      bool IsThreeGood(){...};//这个新的成员函数将基类的覆盖了
      bool CanBaoYan(){...};
  };
  
  class CGraduatedStudent:public CStudent{
      private:
      int nDepartement;
      char szMentorName[20];
      
      public:
      int CountSalary(){...};
  };
  ```

  

- 派生类对象的内存空间:

  ```派生类对象体积=基类对象体积+派生类对象自己的成员变量体积```

  基类对象的存储位置位于派生类对象新增成员变量之前

  ```c++
  class CBase{
      int v1,v2;
  };
  
  class CDerived:public CBase{
      int v3;
  }
  
  //CDerived体积为12个字节
  ```



- 继承示例程序:学籍管理

  ```c++
  #include<iostream>
  #include<string>
  
  using namespace std;
  
  class CStudent{
      private:
      string name;
      string id;
      char gender;
      int age;
      
      public:
      void PrintInfo();
      void Setnfo(const string & name_,const string & id_,int age_.char gender_);
      //&参数是引用
      string GetName(){return name;}
  };
  
  class CUndergraduateStudent:public CStudent{
      private:
    string department;
      
      public:
      void QualifiedForBaoyan(){
          cout<<"qualified for baoyan"<<endl;
      }
      
      //PrintInfo对于基类的同名函数是覆盖的关系
      void PrintInfo(){
          CStudent::PrintInfo();//调用基类的
          cout<<"Department:"<<departement<<endl;
          
          void SetInfo(const string& name_,const string& id_,int age_,char gender_,const string& department_){
              CStudent::SetInfo(name_,id_,age_,gender_);//调用基类的
              department=department_;
          }
      }
  };
  ```

  

## 继承关系和复合关系

- 继承:"**是**"关系

  A是基类,B是A的派生类

  逻辑上要求:**一个B对象也是一个A对象**

- 复合:"**有**"关系

  逻辑上要求:**A对象是B对象的成员变量**

  例子:几何形体程序中,需要写"点"类,也需要写"圆"类,两者的关系就是复合关系,每一个圆对象内都包含**有**一个点对象,这个点对象就是圆心

```c++
class CPoint{
    double x,y;
    friend class CCircle;
    //便于CCircle类操作其圆心
};

class CCircle{
    double r;
    CPoint center;
};
```

- **复合关系的使用**

  如果要写一个小区养狗管理程序， 需要写一个“业主”类，还需要写一个“狗”类。
   而狗是有 “主人” 的，主人当然是业主(假定狗只有
  一个主人，但一个业主可以有最多10条狗）

1. 凑合的写法

   ```c++
   //为狗类设一个业主类的对象指针
   //为业主类设一个狗类的对象数组
   
   class CMaster;
   //CMaster必须提前声明,不能先写CMaster再写CDog类
   
   class CDog{
       CMaster* pm;
   };
   class CMaster{
       CDog dogs[10];
   };
   /*这种写法的缺陷:
   1.对象的成员变量理论上应是该对象的不可分割的组成部分,但主人对于狗并不是这种关系
   2.所有的狗对象都被放在一个数组中,对狗的操作必须通过主人来进行
   ```

   

2. 正确的写法

   ```c++
   //为狗类设一个业主类对象指针
   //为业主类设一个狗类对象指针数组
   
   class CMaster;
   
   class CDog{
       CMaster* pm;
   };
   class CMaster{
       CDog* dogs[10]
   };
   ```

   ![image-20210304144737070](image-20210304144737070.png)





## 覆盖和保护成员

**覆盖**

派生类可以定义一个和基类成员同名的成员,这叫做**覆盖**

在派生类中访问这类成员时,**缺省的情况是访问派生类中定义的成员**

要在派生类中访问由基类定义的同名成员时,要使用**作用域符号::**



**类的保护成员**

- 基类的private成员：可以被下列函数访问 

  – 基类的成员函数 

  – 基类的友元函数

- 基类的public成员：可以被下列函数访问 

  – 基类的成员函数 

  – 基类的友元函数 

  – 派生类的成员函数 

  – 派生类的友元函数 

  – 其他的函数

- 基类的protected成员：可以被下列函数访问 

  – 基类的成员函数 

  – 基类的友元函数

  – 派生类的成员函数可以访问**当前对象**的基类的保护成员



## 派生类的构造函数

```c++
class Bug{
    private:
    int nlegs;
    int ncolor;
    
    public:
    int ntype;
    Bug(int legs,int color);
    void PrintBug(){};
};

class FlyBug:public Bug{
    private:
    int nwings;
    
    public:
    FlyBugs(int legs,int color,int wings);
}

Bug::Bug(int legs,int color)
{
    nlegs=legs;
    ncolor=color;
}
```

错误的FlyBug构造函数写法:

```c++
FlyBug::FlyBug(int legs,int color,int wings)
{
    nlegs=legs;//不能访问
    ncolor=color;//不能访问
    //上面的操作是错误的!!!!nlegs,ncolor是基类的私有成员,不能被派生类的成员函数访问!
    ntypes=1;//okk
    nwings=wings;
}
```

正确的FlyBug构造函数写法:

```c++
FlyBug::FlyBug(int legs,int color,int wings):Bug(legs,color)
//初始化列表
{
    nlegs=legs;
    ncolor=color;
    //上面的操作是错误的!!!!nlegs,ncolor是基类的私有成员,不能被派生类的成员函数访问!
    
    nwings=wings;
};
```

- 在创建派生类的对象时，需要调用基类的构造函数：初始化派生类对象中从基类继承的成员。在执行一个派生类的构造函数 之前，总是先执行基类的构造函数。

- 调用基类构造函数的两种方式

  - 显式方式：在派生类的构造函数中，为基类的构造函数提供参数. 

    ```derived::derived(arg_derived-list):base(arg_base-list)```

  - 隐式方式：在派生类的构造函数中，省略基类构造函数时， 派生类的构造函数则自动调用基类的默认构造函数.

- 派生类的析构函数被执行时，执行完派生类的析构函数后，自动调用基类的析构函数。



## 公有继承的赋值兼容规则

公有继承:class derived: **public** base{ };

```c++
class base{};
class derived:public base{};
base b;
derived d;
```

**规则**:

1. 派生类的对象可以赋值给基类对象

   ```c++
   b=d;
   ```

   

2. 派生类对象可以初始化基类的引用

   ```c++
   base & br=d;
   ```

   

3. 派生类对象的地址可以赋值给基类指针

   ```c++
   base * pb=&d;
   ```

   

**直接基类和间接基类**:

![image-20210307215222740](image-20210307215222740.png)

- 声明派生类时,**只需要列出其直接基类**

- 派生类沿着类的层次向上自动继承他的间接基类

- 派生类的成员包括:

  - 直接基类的成员

  - **所有间接基类的所有成员**

  - 自己的成员

    

```c++
#include<iostream>
using namespace std;

class base{
    public:
    int n;
    base(int i):n(i){
        //构造函数有参数,也有初始化列表
        //n是成员变量,i是参数表
        cout<<"base"<<n<<"constructed"<<endl;
    }
    ~base(){
        cout<<"base"<<n<<"destructed"<<endl;
    }
};

class derived:public base{
    public:
    derived(int i):base(i){
        //构造函数有参数,也有初始化列表
        cout<<"derived constructed"<<endl;
    }
    ~derived(){
        cout<<"derived destructed"<<endl;    
    }
};
    
class morederived:public derived{
    public:
    morederived():derived(4){
        //构造函数有参数,也有初始化列表
        //只需要直接基类的初始化列表
        cout<<"morederived constructed"<<endl;
    }
    ~morederived(){
        cout<<"morederived destructed"<<endl;    
    }
};
    
int main()
{
    morederived obj;
    return 0;
}
```

output:

```c++
base4constructed
derived constructed
morederived constructed
morederived destructed
derived destructed
base4destructed
```



# 多态

## 虚函数和多态

- **虚函数**

  在类的定义中,前面有virtual关键字的成员函数

  ```c++
  class base{
      virtual int get();
  };
  ```

  ❗❗<font color=red>virtual关键字只用在类定义里的函数声明中,写函数体时候不用</font>

  ```c++
  int base::get()//不需要在get前面加virtual!!!
  ```

  ❗❗**构造函数和静态成员函数**不能是虚函数

  ❗❗虚函数和普通函数的本质差别:**虚函数可以参与多态,静态函数不能**



- **多态的表现形式一:指针**

  - 派生类的对象可以赋给基类指针
  - 通过基类指针调用基类和派生类中的**同名虚函数**时候:

    - 若指针指向一个基类的对象,则被调用的是基类的**虚函数**

    - 若指针指向一个派生类的对象,则被调用的是派生类的**虚函数**


   ```c++
  class Cbase(){
    public:
          virtual void SomeVirtualFunction(){}
  };
  
  class Cderived:public Cbase(){
    public:
          virtual void SomeVirtualFunction(){}
  };
  
  
  int main(){
    Cderived Oderived;
    Cbase *p= &Oderived;
    p->SomeVirtualFunction();
    //p指向派生类的对象,调用的是派生类的虚函数
    return 0;
  }
   ```



- **多态的表现形式二:对象**
  - 派生类的对象可以赋给基类引用
  - 通过基类引用调用基类和派生类中的同名虚函数时:
    - 若该引用引得是一个基类的对象,那么被调用的是基类的虚函数
    - 若该引用引得是一个派生类的对象,那么被调用的是派生类的虚函数

```c++
class Cbase(){
    public:
    virtual void SomeVirtualFunction(){}
};

class Cderived(){
    public:
    virtual void SomeVirtualFunction(){}
};

int main(){
    Cderived Oderived;
    Cbase &r=Oderived;
    r.SomeVirtualFunction();//!!!引用调用函数时用"."!!!
    //r引用的是派生类的对象,调用派生类的虚函数
}

```

- 多态的作用

  增强程序的可扩充性**(程序需要修改或增加功能的时候,需要改动和增加的代码较少)**

  

## 使用多态的游戏程序示例

## 几何形体处理程序

```c++
#include<iostream>
#include<stdlib.h>
#include<math.h>

using namespace std;

//定义基类
class CShape{
    public:
    virtual double Area()=0;
    //后面写了一个"=0",说明这是纯虚函数,连函数体都没有
    virtual void PrintInfo()=0;
    //因为我们要处理的图形只有圆形三角矩形几种,不存在CShape这样一种抽象的形状,所以不需要为CShape编写这两程序,这两程序在派生类中会分别定义
};

//定义派生类
class CRectangle:public CShape{
    public:
    int w,h;
    virtual double Area();
    virtual void PrintInfo();
};

class CCircle:public CShape{
    public:
    int r;
    virtual double Area();
    virtual void PrintInfo();
};

class CTriangle:public CShape{
    public:
    int a,b,c;
    virtual double Area();
    virtual void PrintInfo();
};


//实现派生类的成员函数

double CRectangle::Area(){
    return w*h;
}
void CRectangle::PrintInfo(){
    cout<<"Rectangle:"<<Area()<<endl;//输出的末尾要加endl!!
}

double CCircle::Area(){
    return 3.14*r*r;
}
void CCircle::PrintInfo(){
    cout<<"Circle:"<<Area()<<endl;
}

double CTriangle::Area(){
    double p=(a+b+c)/2.0;
    return sqrt(p*(p-a)*(p-b)*(p-c));
}
void CCircle::PrintInfo(){
    cout<<"Triangle:"<<Area()<<endl;
}

//存放不同类型的几何形体
CShape *pShapes[100];
int MyCompare(const void *s1,const void *s2);
//用三个数组来存浪费空间,难以增改,难以实现排序等进阶功能
//所以我们使用多态来存储
//pShapes数组中的元素都是基类指针,由于基类指针能够指向派生类对象,我们可以在后面把指针指向new出来的派生类对象


//主函数
int main(){
    int i;int n;
    CRectangle *pr;CCircle *pc;CTriangle *pt;
    
    cin>>n;
    for(i=0;i<n;i++){
        char c;
        cin>>c;
        switch(c){
            case'R':
                    pr=new CRectangle();
                    cin>>pr->w>>pr->h;
                    pShapes[i]=pr;
                    break;
            case 'C':
                    pc=new CCircle();                                   cin>>pc->r;
                    pShapes[i]=pc;
                    break;
            case'T':
                pt=new CTriangle();
                cin>>pt->a>>pt->b>>pt->c;
                pShapes[i]=pt;
                break;
                    
        }
    }
    qsort(pShapes,n,sizeof(CShape*),MyCompare);
    for(i=0;i<n;i++)
        pShapes[i]->PrintInfo();
    return 0;
}
//为什么变量前面要加关键字void*?然后可以将指向任何类型数据的指针赋给这个void*类型指针
int MyCompare(const void *s1,const void *s2){
    double a1,a2;
    CShape**p1;
    CShape**p2;
    //一定要写两个*,不能用"*s1"来取得s1指向的内容
    p1=(CShape**)s1;
    p2=(CShape**)s2;
    //s1,s2指向数组中的元素,数组中元素的类型是CShape*
    //因此p1,p2是指向指针的指针,类型为CShape**
    a1=(*p1)->Area();//*p1的类型是CShape*是基类指针
    a2=(*p2)->Area();
    if(a1<a2)
        return -1;
    else if(a2<a1)
        return 1;
    else
        return 0;
}
```



*下面是 ```qsort() ```函数的声明。

```
void qsort(void *base, size_t nitems, size_t size, int (*compar)(const void *, const void*))
```

- **base** -- 指向要排序的数组的第一个元素的指针。
- **nitems** -- 由 base 指向的数组中元素的个数。
- **size** -- 数组中每个元素的大小，以字节为单位。
- **compar** -- 用来比较两个元素的函数。



## 虚析构函数,纯虚函数,抽象类

**虚析构函数**:

```c++
class son{
    public:
    ~son(){cout<<"bye from son"<<endl;}
};

class grandson:public son{
    public:
    ~grandson(){cout<<"bye from grandson"<<endl;}
};

int main()
{
    son *pson;
    pson=new grandson();
    //new一个对象的时候,如果对象的构造函数不需要参数就要写一对括号,跟new一个数据不一样
    delete pson;
    return 0;
}
```

output:

```c++
bye from son
```

**<font color =red>new出来的grandson没有被删除!!!!!,反而执行了son的析构函数,然而我们并没有定义son类的对象</font>**

解决办法:

**<font color =red>把基类son的析构函数变成虚函数</font>**



**纯虚函数和抽象类**:

- 包含纯虚函数的类就是抽象类

- 抽象类不能创建自己的对象
- 抽象类的指针/引用可以指向其派生类的对象
- 在抽象类的成员函数内可以调用纯虚函数，但是**在构造函数或析构函数内部 不能调用纯虚函数**。
- 如果一个类从抽象类派生而来，那么当且仅当它实现了基类中的所有纯虚函数，它才能成为非抽象类。

# 输入输出和模板

## 函数模板

- 例子-求数组最大元素的函数模板

```c++
template<class T>
T MaxElement(T a[],int size){
    T tmpMax=a[0];
    for(int i=1;i<size;i++)
        if(tmpMax<a[i])
            tmpMax=a[i]
    return tmpMax;        
}
```

- 例子-不通过参数实例化函数模板

```c++
#include<iostream>
using namespace std;
template<class T>
T Inc(T n){
    return 1+T;
    //由于Inc的返回值是T类型的,这里要对+进行重载
}

int main(){
    cout<<Inc<double>(4)/2;
    //用<double>直接规定T是double类型
}
```

- 函数模板是可以**重载**的,只要他们的形参表和类型参数表不一样即可

- 函数模板和函数的**次序**:

  参数匹配+普通函数----->参数匹配模板函数----->参数经自动类型转换后能匹配的普通函数

## 类模板

编写模板是为了实现**泛型程序设计**,即写出一个类/函数后,可以作用与多种**数据类型**



**类模板的定义**

```c++
template<typename 类型参数,typename 类型参数,...>
class 模板名字
{
    成员变量;
    成员函数;
}
```

**类模板内成员函数定义**

```c++
template<typename 类型参数,typename 类型参数,...>
(返回值)类型参数 模板名字 <类型参数名字,类型参数名字,...>::成员函数名字(参数表)
{
    ...
}
```

**类模板定义对象的写法**

```c++
模板名字<实际类型参数表> 对象名(构造函数实参表);
```

**示例:Pair类模板**

```c++
#include<iostream>
#include<string.h>
using namespace std;
//类型参数就是这个类需要的参数的类型
//其个数就该类需要的参数个数
template<typename T1,typename T2>
class Pair
{
    public:
    T1 key;//关键字
    T2 value;//值
    Pair(T1 k,T2 v):key(k),value(v){};
    //构造函数及其初始化列表
    bool operator < (const Pair<T1,T2>&p)const;
    //对"<"进行重载,其形参是pair模板定义的类的常引用
    //我们希望这个"<"是不变的,最后加个const
};

template<typename T1,typename T2>
bool Pair<T1,T2>::operator < (const Pair<T1,T2>&p)const
{
    return key<p.key;
}

int main()
{
    Pair<string,int> student("Tom",19);
    //对象的名字是:student
    //注意:由类模板"pair"生成类"pair<string,int>",再由这个类生成对象"student"
     cout<<student.key<<" "<<student.value;
     return 0;
}
```

output:

```c++
Tom 19
```

- **类模板的实例化**:编译器由类模板生成类的过程

- 同一个模板实例化出的类是不兼容的,他们根本就是两个类

  ```c++
  Pair<string,int> *p;
  Pair<string,double> a;
  p=a;//报错
  ```

  

**函数模板作为类模板成员**

```c++
#include<iostream>
using namespace std;

template<typename T>
class A
{
    public:
    
    //注意这里不能写T,因为这里成员函数的类型参数相对类的类型参数来说是形参
    
    template<typename T2>
    void Func(T2 t){cout<<t;}
    
};

int main()
{
    A<int> a;
    //类模板名: A
    //类名: A<int>
    //对象名: a,其需要的参数类型为int(虽然下面的定义里我没有用这个参数)
    
    a.Func('K');//成员函数模板Func被实例化,其参数类型为char
    a.Func("hello");//成员函数模板Func再次被实例化,其参数类型为const char*
    return 0;
}
```

output:

```c++
Khello
```

- ```A<int>```这个类通过上面对函数模板```Func```的两次实例化有了两个名为``Func``的成员函数:

  ```c++
  Func(char c);//赋给该函数的值是:K
  Func(const char* string);//赋给该函数的值是:指向字符串"hello"的指针
  ```

  

**类模板的类型参数表中可以出现非类型参数**:

![image-20210308125420974](image-20210308125420974.png)

# 标准模板库STL

## 概述

![image-20210318145622473](image-20210318145622473.png)

![image-20210318145709507](image-20210318145709507.png)

![image-20210318145751989](image-20210318145751989.png)

## 迭代器

- 用于指向顺序容器/关联容器中的元素

- 用法和指针类似

- 有const和非const两种

- 通过迭代器可以读取其指向的元素

- 通过非const迭代器可以修改其指向的元素

  

**代码**:

定义一个容器类的迭代器的方法:

```c++
容器类名::iterator 变量名;
容器类名::const_iterator 变量名;
```

访问一个迭代器指向的元素:

```c++
*迭代器变量名
```



**迭代器示例**:

```c++
#include<vector>
#include<iostream>
using namespace std;
int main()
{
    
    vector<int> v;
    //v是一个存放int类型元素的动态数组,一开始里面没有元素
    v.push_back(1);
    v.push_back(2);
    v.push_back(3);
    v.push_back(4);
    
    
    //正向迭代器
    vector<int>::const_iterator i;
    //常量迭代器,只能读取元素,不能修改元素
    for(i=v.begin();i!=v.end();i++)
    //begin()获取容器第一个元素的位置
    //end()获取容器最后一个元素后面的位置
        cout<<*i<<",";
    cout<<endl;
    
    //反向迭代器
    vector<int>::reverse_iterator r;
    for(r=v.rbegin();r!=v.rend;r++)
    //rbegin()获取容器最后一个元素的位置
    //rend()获取容器第一个元素前面的位置
        cout<<*r<<",";
    cout<<endl;
    
    //非常量迭代器
    vector<int>::iterator j;
    for(j=v.begin();j!=v.end();j++)
        *j=100;//用非常量迭代器修改指向的元素
    
    for(i=v.begin();i!=v.end();i++)
        cout<<*i<<",";//用常量迭代器读取指向的元素
    cout<<endl;    
}
```



**两种不同的的迭代器**：

- 双向迭代器p&p1

  - ++p，p++

  - --p，p--

  - *p**（实际上返回值是p指向的对象的引用）**

  - p=p1

  - p==p1；p！=p1

    

- 随即访问迭代器p&p1

  - 双向迭代器的所有操作
  - p+=i（将p向后移动i个元素）
  - p-=i
  - p+i （p+i的值=指向p后面第i个元素的迭代器）
  - p-i
  - p[i]（p[i]的值=p后面第i个元素的引用）
  - p<p1;p<=p1;p>p1;p>=p1

## 容器

### 顺序容器

#### vector(动态数组)

**vector上的操作**

```c++
#include<iostream>
#include<vector>
using namespace std;
template<class T>
void PrintVector(T s,T e)
{
    for(;s!=e;++s)
        cout<<*s<<" ";
    cout<<endl;
}

int main()
{
    int a[5]={1,2,3,4,5};
    vector<int> v(a,a+5);
    //将数组a中下标从0到4的元素都拷贝到v里面
    
    cout<<v.end()-v.begin();
    //vector的迭代器是随即迭代器可以相减
    //上面输出的v中元素的个数:5
    
    v.insert(v.begin()+2,13);
    PrintVector(v.begin(),v.end());
    //在v下标为2的位置插入一个元素13,后面的元素全部后推
    
    v.erase(v.begin()+2);
    PrintVector(v.begin(),v.end());
    //删除位于v中下标为2的元素
    
    vector<int> v2(4,100);
    //v2有4个元素,都是100
    v2.insert(v2.begin(),v.begin()+1,v.begin()+3);
    //将v的一段插入v2开头
    //v下标为1的元素到v下标为3的元素(不包括v下标为3的元素!!!)
    PrintVector(v2.begin(),v2.end());
    
    v.erase(v.begin()+1,v.begin()+3);
    PrintVector(v.begin(),v.end());
    //删除v上的一个区间,即2,3
    
    return 0;
}
```

**用vector实现二维数组**

```c++
#include<iostream>
#include<vector>
using namespace std;
int main()
{
    vector<vector<int> >v(3);
    //v中有3个元素,每个元素都是vector<int>空容器
    //注意vector<int>后面一定要加一个空格,不然编译器会把两个尖括号当成右移运算符
    
    //下面往v的元素里添加元素
    for(int i=0;i<v.size();++i)
        for(int j=0;j<4;++j)
            v[i].push_back(j);
    
    //下面显示v的元素里的元素
    for(int i=0;i<v.size();++i){
        for(int j=0;j<v[i].size();++j)
            cout<<v[i][j]<<" ";
        cout<<endl;
    }
    return 0;
}
```

#### deque(双向队列)

- 所有vector的操作都适用于deque

- deque多了:

  ``push_front``:将元素插入到前面

  ``pop_front``:删除最前面的元素



#### list(双向列表)

**成员函数**

![image-20210318143445110](image-20210318143445110.png)

**list上的操作**

```c++
#include<list>
#include<iostream>
#include<algorithm>
using namepsace std;

class A
{
    private:
    int n;
    
    public:
    A(int n_){n=n_;}
    friend bool operator<(const A & a1,const A & a2);
    friend bool operator==(const A & a1,const A & a2);
    friend ostream & operator << (ostream & o,const A & a);
}
bool operator<(const A & a1,const A & a2)
{return a1.n<a2.n}
bool operator==(const A & a1,const A & a2)
{return a1.n==a2.n}
ostream & operator << (ostream & o,const A & a)
{o<<a.n;return o;}

//下面定义一个函数模板,其参数是"类型可变列表的引用"
template <class T>
void PrintList(const list<T> & lst)//参数是"类型可变列表的引用"
{
    typename list<T>::const_iterator i;
    //typename用来说明"list<T>::const_iterator"描述的是个类型
    i=lst.begin();
    for(i=lst.begin();i!=lst.end();i++)
        cout<<*i<<",";
}

int main()
{
    list<A> lst1,lst2;
    //定义了两个元素为A类对象的列表
    
    ...
        
    PrintLsit(lst1);
}
```



### 容器适配器

容器适配器上没有**迭代器**

#### stack

- 是**后进先出**的数据结构，

- 可以

  - ``push``插入
  - ``pop``删除
  - ``top``返回栈顶元素的引用

- 可用vector，list，deque来实现，**缺省情况下，用deque实现**。（vector、deque实现的性能比list好）

  ```c++
  template<class T,class Container=deque<T>>
  //第一个类型参数是栈里的元素类型
  //第二个类型参数是栈用什么容器来实现，并表示缺省情况下是deque
  class stack{
      ...
  };
  ```

  

#### queue

- 是**先进先出**的数据结构，

- 可以

  - ``push``插入（发生在队尾）
  - ``pop``删除
  - ``front``返回栈顶元素的引用
  - ``back``返回队尾元素的引用

- 可用list，deque来实现，**缺省情况下，用deque实现**。（vector、deque实现的性能比list好）

  ```c++
  template<class T,class Container=deque<T>>
  //第一个类型参数是容器适配器里的元素类型
  //第二个类型参数是容器适配器用什么容器来实现，并表示缺省情况下是deque
  class stack{
      ...
  };
  ```

  

#### priority_queue

- 可用vector和deque实现，**缺省情况下用vector实现**

  ```c++
  template<class T,class Container=vector<T>,class Compare=less<T>>
  //第一个类型参数是容器适配器里的元素类型
  //第二个类型参数是容器适配器用什么容器来实现，并表示缺省情况下是vector
  //第三个类型参数是优先队列的元素比较器用什么来实现，并表示缺省情况下是小于号
  class priority_queue;
  ```

- priority_queue通常用**堆排序**技术实现，保证最大的元素总是在最前面，即执行pop操作时，删除的是最大的元素；执行top操作时，返回的是最大元素的引用，默认的元素比较器是less

- ``push``,``pop``时间复杂度：O（logn）

- ``top``时间复杂度：O（1）



所有容器适配器中都有成员函数：

1. empty（）：用于判断适配器是否为空
2. size（）：用于返回适配器中元素个数

## 算法

### 类型参数Pred

大多重载的算法都是有两个版本的：

1. 一个是
   - 用“==”判断元素是否相等
   - 用“<”来比较大小
2. 另一个多出来一 个类型参数``Pred``，以及函数形参``Pred op``
   - 表达式“op(x,y)”的返回值是ture，则x等于y
   - 表达式“op(x,y)”的返回值是false，则x小于y

### 不变序列算法

- 不会修改算法作用的容器或对象
- 适用于顺序容器和关联容器
- 时间复杂度：O（n）

![image-20210315213723396](image-20210315213723396.png)

![image-20210315213758362](image-20210315213758362.png)

![image-20210315213935514](image-20210315213935514.png)

- ``find``很重要

  

### 排序算法

- 需要随机访问迭代器的支持
- 不适用于关联容器和list
- 时间复杂度：O(log(n))

![image-20210315215204555](image-20210315215204555.png)

![image-20210315215228540](image-20210315215228540.png)



**sort：快速排序**

- 模板

  ```c++
  template<class Ranlt>
  void sort(Ranlt first,Ranlt last);
  ```

  1. 按升序排列
  2. 判断x是否应该比y靠前，就看x<y是否为true

  ```c++
  template<class Ranlt,class Pred>
  void sort(Ranlt first,Ranlt last,Pred pr);
  ```

  1. 按升序排列
  2. 判断x是否应该比y靠前，就看pr(x<y)是否为true  



**应用：**

```c++
#include<iostream>
#include<algorithm>
using namespace std;

//定义Pred pr
class MyLess
{
    public：
    bool operator()(int n1,int n2)
    {
        return(n1%10)<(n2%10);
    }   
};

int main()
{
    int a[]={14,2,9,111,78};
    int i;
    
    sort(a,a+5,MyLess());//按个位数大小排序
    for(i=0;i<5;i++)
        cout<<a[i]<<" ";
    cout<<endl;
    
    sort(a,a+5,greater<int>());//按降序排序
    for(i=0;i<5;i++)
        cout<<a[i]<<" ";
}
```



**注意：**

- sort实际上是快速排序，时间复杂度为：O(n*log(n))
  - 平均性能最优
  - 最坏的情况性能非常差

- stable_sort实际上是归并排序
  - 能保证相等元素之间的先后次序
  - 存储空间足够时，时间复杂度：n*log(n)
  - 存储空间不够时，时间复杂度：n*log(n)\*log(n)

- list只能使用内置的排序算法：``list::sort``

