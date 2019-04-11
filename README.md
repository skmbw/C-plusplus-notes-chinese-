# C++
C++笔记
## 默认初始化
### a.内置类型的变量初始化
如果定义变量时没有指定初始值，则变量被默认初始化。默认值由变量类型和定义变量的位置决定。
* 如果是内置类型的变量未被显示初始化，它的值由定义位置决定。定义于任何函数体外的变量被初始化为0。
```
//不在块中
int i;//正确，i会被值初始化为0，也称为零初始化
```
* 定义于函数体内部的内置类型变量将不被初始化（uninitialized）。一个未被初始化的内置类型变量的值是未定义的，如果试图拷贝或其他形式的访问此类型将引发错误
```
1 {//在一个块中
2 int i;//默认初始化，不可直接使用
3 int j=0;//值初始化
4 j=1;//赋值
5 }
```
### b.类类型的对象初始化
类通过一个或几个特殊的成员函数来控制其对象的初始化过程，这些函数叫做构造函数（constructor）。构造函数的任务是初始化类对象的数据成员。
由编译器提供的构造函数叫（合成的默认构造函数）。
合成的默认构造函数将按照如下规则初始化类的数据成员。
* 如果存在类内初始值（__C++11新特性__），用它来初始化成员。
```
class CC
{
public:
    CC() {}
    ~CC() {}
private:
    int a = 7; // 类内初始化，C++11 可用
}
```
* 否则，没有初始值的成员将被默认初始化。

## C++为类自动提供了下面这些成员函数
* 默认构造函数，如果没有定义构造函数；记得自己定义了构造函数时，编译器不会再提供默认构造函数，记得自己再定义一个默认构造函数。
* 默认析构函数，如果没有定义；用于销毁对象
* 复制（拷贝）构造函数，如果没有定义；用于将一个对象赋值到**新创建**的对象中。用于初始化的过程中，而不是常规的赋值过程
* 赋值运算符，如果没有定义；将已有的对象赋值给另外一个对象时，使用赋值运算符。
* 地址运算符，如果没有定义；
* 移动构造函数(move construtor)，如果没有定义；
* 移动赋值运算符（move assignment operator）。

## 基类和派生类的特殊关系
* 1.派生类对象可以使用非私有的基类方法
* 2.基类指针（引用）可以在不进行显示转换的情况下指向（引用）派生类对象；积累指针或引用只能用来调用基类方法，不能用来调用派生类方法。
* 3.不可以将基类对象和地址赋给派生类对象引用和指针。

## 派生类构造函数要点
1.首先创建基类对象；
2.派生类构造函数应通过成员初始化列表将基类信息传递给基类构造函数。
3.派生类构造函数应初始化新增的数据成员。
注意：可以通过初始化列表语法知名指明要使用的基类构造函数，否则使用默认的基类构造函数。派生类对象过期时，程序**将首先调用派生类的析构函数，然后在调用基类的析构函数**。
```
RetedPlayer::RetedPlayer(unsigned int r,const string & fn,const string &ln, bool ht)//:TableTennisPlayer()等价于调用默认构造函数
{
rating = r;
}
```
## 虚方法
* 经常在基类中将派生类会重新定义的方法声明为**虚方法**。方法在基类中被声明为虚的后。它在派生类中将**自动生成**虚方法。然而，在派生类中使用关键字virtual来指出哪些函数是虚函数也不失为一个好方法。
* 如果要在派生类中重新定义基类的方法，通常将基类方法声明为虚。这样，**程序根据对象类型而不是引用或指针类型来选择方法版本**，为基类声明一个虚的析构函数也是一种惯例，为了确保释放派生类对象时，按正确的顺序调用析构函数。

## 派生类方法可以调用公有的基类方法
在派生类方法中，标准技术是使用作用域解析运算符来调用基类方法，如果没有使用作用域解析符，有可能创建一个不会终止的递归函数。如果派生类没有重新定义基类方法，那么代码不必对该方法是用作用域解析符（即该方法只有在基类中有）。

## 静态联编和动态联编
**函数名联编**（binding）：将代码中的函数调用解释为执行特定的代码块。
* 在C语言中，这非常简单，因为每个函数名都对应一个不同的函数。
* 在C++中，由于函数重载的缘故，这个任务更繁杂，编译器必须查看函数参数以及函数名才能确定使用哪个函数。

>静态联编(static binding)
* 在编译过程中进行联编，又称为早期联编
>动态联编(dynamic binding)
* 编译器在程序运行时确定将要调用的函数，又称为晚期联编

### 什么时候使用静态联编，什么时候使用动态联编？
* 编译器对虚方法使用静态联编，因为方法是非虚的，可以根据指针类型关联到方法。
* 编译器对虚方法使用动态联编，因为方法是虚的，程序运行时才知道指针指向的对象类型，才来选择方法。（引用同理）




