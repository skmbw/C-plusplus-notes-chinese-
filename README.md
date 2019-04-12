# C++
C++笔记
## 什么是多态
* 多态：同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果。在运行时，可以通过指向基类的指针，来调用实现派生类中的方法。
* C++中，实现多态有以下方法：**虚函数，抽象类，覆盖，模板（重载和多态无关）。**

## 什么是重载（todo）

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

# 13类继承
## 基类和派生类的特殊关系
* 1.派生类对象可以使用非私有的基类方法
* 2.基类指针（引用）可以在不进行显示转换的情况下指向（引用）派生类对象（**反过来不行**）；**基类指针或引用只能用来调用基类方法，不能用来调用派生类方法。**
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
* 虚函数的作用：基类指针（引用）指向（引用）派生类对象，会发生自动向上类型转换，即派生类升级为父类，虽然子类转换成了它的父类型，但却可正确调用属于子类而不属于父类的成员函数。这是虚函数的功劳。

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
### 效率：为使程序能够在运行阶段进行决策，必须采取一些方法来跟踪基类指针和指向引用对象的对象类型，这增加了额外的处理开销
* 例如，如果类不会用作基类，则不需要动态联编。
* 同样，如果派生类不重新定义基类的任何方法，也不需要动态联编。
* 通常，编译器处理函数的方法是：给每个对象添加一个隐藏成员--指向函数地址数组的**指针**(vptr)
>使用虚函数时，在内存和执行速度上有一定的成本，包括：
a.每个对象为存储地址的空间；
b.对于每个类，比那一期都将创建一个虚函数地址表（数组）vtbl；
c.对于每个函数调用，都需要执行一项额外的操作，到表中查找地址。虽然非虚函数的效率比虚函数稍高，但不具有动态联编的功能
### 总结：
* 在基类方法的声明中使用关键字virtual可使该方法在基类以及所有的派生类（包括从派生类派生出来的类）中是虚的。
* 如果使用指向对象的引用或指针来调用虚方法，程序将使用为对象类型定义的方法，而不是用为引用或者指针类型定义的方法。这个成为动态联编或者晚期联编。这种行为非常重要。因为这样基类指针或引用可以指向派生类对象。
* 如果定义的类将被用作基类，则应该将那些在派生类中重新定义的类方法生命为虚的。

## 虚函数细节
* 1.构造函数不能是虚函数，派生类不能继承基类的构造函数，将类构造函数声明为虚没什么意义。
* 2.析构函数应当是虚函数，除非类不用作基类。
> 1.当子类指针指向子类是，析构函数会先调用子类析构再调用父类析构，释放所有内存。
> 2.当父类指针指向子类时，只会调用父类析构函数，子类析构函数不会被调用，会造成内存泄漏。（基类析构函数声明为虚，可以使得父类指针能够调用子类虚的析构函数）**所以我们需要虚析构函数，将父类的析构函数定位为虚，那么父类指针会先调用子类的析构函数，再调用父类析构，使得内存得到释放**。
* 3.友元不能是虚函数，因为友元不是类成员，只有类成员才是虚函数。
* 4.如果派生类没有重新定义函数。将使用该函数的基类版本。
* 5.**重新定义将隐藏方法**不会生成函数的两个重载版本，而是隐藏基类版本。如果派生类位于派生链中，则使用最新的虚函数版本，例外的情况是基类版本是隐藏的。总之，重新定义基本的方法并不是重载。如果重新定义派生类中的函数，将不只是使用相同的函数参数列表覆盖其基类声明，无论参数列表是否相同，该操作将隐藏所有的同名方法。
## 两条经验规则
* 1.如果重新定义继承的方法，应确保与原来的原型完全相同，但是**如果返回类型是积累的引用或指针，则可以修改为指向派生类的引用或指针（只适用于返回值而不适用于参数）**，这种例外是新出现的。这种特性被称为**返回类型协变（convariance of return type）**，因此返回类型是随类类型变化的。
```
//基类
class Dwelling
{
public:
virtual Dwelling & build(int n);
}
//派生类
class Hovel:public Dwelling
{
public:
virtual Hovel & build(int n);
}
```
* 2.如果基类声明被重载了，则应该在派生类中重新定义所有基类版本。
```
//基类
class Dwelling
{
public:
//三个重载版本的showperks
virtual void showperks（int    a）const；
virtual void showperks（double a）const；
virtual void showperks（        ）const；
}
//派生类
class Hovel:public Dwelling
{
public:
//三个重新定义的的showperks
virtual void showperks（int    a）const；
virtual void showperks（double a）const；
virtual void showperks（        ）const；
}
```
**如果只重新定义一个版本，则另外两个版本将被隐藏，派生类对象将无法使用它们，**
注意，如果不需要修改，则新定义可知调用基类版本：
```
void Hovel::showperk()const
{
Dwelling::showperks();
}
```
## 访问控制：protected
* 1.关键字protected与private相似，在类外，只能用公有类成员来访问protected部分中的类成员。
* 2.private和protected之间只有在基类派生的类才会表现出来。派生类的成员可以直接访问基类的保护成员，但是不能直接访问基类的私有成员。
### 提示：
* 1.最好对类的数据成员采用私有访问控制，不要使用保护访问控制。
* 2.对于成员函数来说，保护访问控制很有用，它让派生类能够访问公众不能使用的内部函数。

## 抽象基类（abstract base class）ABC->至少包含一个纯虚函数
* 在一个**虚函数的声明语句**的分号前加上 =0 ；就可以将一个虚函数变成**纯虚函数**，其中，=0只能出现在类内部的虚函数声明语句处。
* 纯虚函数只用声明，而不用定义，其存在就是为了提供接口，含有纯虚函数的类是抽象基类。我们不能直接创建一个抽象基类的对象，但可以创建其指针或者引用。
* 值得注意的是，你也可以为纯虚函数提供定义，**不过函数体必须定义在类的外部**。但此时哪怕在外部定义了，也是纯虚函数，不能构建对象。  
* **派生类构造函数只直接初始化它的直接基类。多继承的虚继承除外。**

### 抽象类应该注意的地方
* 抽象类不能实例化，所以抽象类中不能有构造函数。
* 纯虚函数应该在派生类中重写，**否则派生类也是抽象类，不能实例化。**

### 抽象基类的作用
* C++通过使用纯虚函数（pure virtual function）提供未实现的函数。纯虚函数声明的结尾处为=0，
```
virtual double Area() const=0;//=0指出类是一个抽象基类，在类中可以不定义该函数 
```
* 可以将ABC看作是一种必须的接口。ABC要求具体派生类覆盖其纯虚函数---迫使派生类遵顼ABC设置的接口规则。简单来说是：因为在派生类中必须重写纯虚函数，否则不能实例化该派生类。所以，派生类中必定会有重新定义的该函数的接口。
* 从两个类(**具体类concrete**)（如：Ellipse和Circle类）中抽象出他们的共性，将这些特性放到一个ABC中。然后从该ABC派生出的Ellipse和Circle类。
**这样，便可以使用基类指针数组同时管理Ellipse和Circle对象，即可以使用多态方法**



## 友元
* 就像友元关系不能传递一样，友元关系同样不能继承，基类的友元在访问派生类成员时不具有特殊性，类似的，派生类的友元也不能随意访问基类的成员。

## 继承和动态内存分配(todo)
* 只有当一个类被用来做基类的时候才会把析构函数写成虚函数。
* 当基类和派生类都采用动态内存分配是，派生类的析构函数，复制构造函数，赋值运算符都必须使用相应的基类方法来处理基类

# C++中的代码重用（公有继承，包含对象的类，私有继承，多重继承，类模板）
## 包含（containment）：包含对象成员的类
本身是另外一个类的对象。这种方法称为包含（containment），组合（composition），或层次化（laying）
## 私有继承（还是has-a关系）
基类的公有成员和保护成员都将成为派生类的私有成员。和公有继承一样，基类的私有成员是会被派生类继承但是不能被派生类访问。基类方法将不会成为派生类对象公有接口的一部分，但可以在派生类中使用它们。
* 1.初始化基类组件
>和包含不同，对于继承类的新版本的构造函数将使用成员初始化列表语法，它使用类名（std::string，std::valarry）而不是成员名来表示构造函数
```
//Student类私有继承两个类派生而来,本来包含的时候两个基类分别是name和score
class Student:private std::string,private std::valarry<double>
{
public:
......
};
//如果是包含的构造函数
Student(const char *str,const double *pd,int n):name(str),score(pd,n)
{
}
//继承类的构造函数 
Student(const char *str,const double *pd,int n):std::string(str),std::valarry<double>(pd,n)
{
}
```
* 2.访问基类的方法
> a.包含书用对象（**对象名**）来调用方法

> b.私有继承时，**将使用类名和作用域解析运算符来调用方法**
```
double Student::Average() const
{
if(ArrayDb::size()>0）//ArrayDb typedef为std::valarry<double>
  return ArrayDb::sum()/ArrayDb::size();
else
  return 0;
}
```
* 3.访问基类对象

使用私有继承时，该string对象没有名称。**那么，student类的代码如何访问内部string对象呢？** **强制类型转换!**
> **本来**子到父自动类型提升,不需要强制类型转换。父到子才需要强制类型转换。**但是**下面是强制类型转换，原因在第4点那里写着。

> 由于Student类是从string类派生而来的，因此可以通过强制类型转换，将Student对象转换为S=string对象
```
//成员方法：打印出学生的名字
//因为不是包含，只能通过强制类型转换
const string & Student::Name()const
{
retrun (const string &) *this;
}  
```
* 4.访问基类友的元函数

用类名显式地限定函数名不适合友元函数，**因为友元不属于类**。不能通过这种方法访问基类。

解决：**通过显示地转换为基类来调用正确的函数**
```
osstream & operator<<(ostream & os,const Student & stu)
{
os << "Score for "<<(const String &) stu <<":\n";//显式地将stu转换为string对象引用，进而调用基类方法
}
```
**引用不会自动转换为string引用**
原因：
> * a.在私有继承中，未进行显示类型转换的派生类引用或指针，无法赋值给基类的引用或指针。
> * b.即使这个例子使用的是公有继承，也必须使用显示类型转换。原因之一是，如果不使用类型转换，下述代码将无法与函数原型匹配从而导致递归调用，os<<stu
> * c.由于这个类使用的是多重继承，编译器将无法确定应转换成哪个基类，如果两个基类都提供函数operator<<()。

* 5.使用包含还是私有继承？
通常，应使用包含来建立has-a关系；如果新需要访问原有的保护成员，或重新定义虚函数，则应使用私有继承。
* 6.保护继承
>* 基类的公有成员和保护成员都将成为派生类的保护成员。
>* 共同点：和私有继承一样，基类的接口在派生类中也是可用的，但在继承和结构之外是不可用的。
>* 区别：使用私有继承时，第三代类将不能使用基类的接口，这是因为公有方法在派生类中将变成私有方法；使用保护继承时，基类的公有方法在第二代将变成保护的，因此第三代派生类可以使用它们。

| 特征 | 公有继承 | 保护继承 |私有继承 |
| ------ | ------ | ------ |------ |
| 公有成员变成 | 派生类的公有成员 | 派生类的保护成员 |派生类的私有成员 |
| 保护成员变成 | 派生类的保护成员 | 派生类的保护成员 |派生类的私有成员 |
| 私有成员变成 | 只能通过基类接口访问 |只能通过基类接口访问|只能通过基类接口访问 |
| 能否隐式向上转换 | 是 | 是（但只能在派生类中） |否 |

* 7.使用using重新定义访问权限
使用派生或私有派生时，基类的公有成员将成为保护成员或私有成员，假设要让基类方法在派生类外面可用
> * 方法1，定义一个使用该基类方法的派生类方法
```
double Student::sum() const
{
return std::valarray<double>::sum();
}
```
> * 方法2，将函数调用包装在另外一个函数调用中，即使用一个using声明（就像空间名称一样）
```
class Student::private std::string,private std::valarray<double>
{
...
public:
  using std::valarray<double>::min;
  using std::valarray<double>::max;
}
```
//using声明只适用于继承，而不适用于包含
//using声明只使用成员名---没有圆括号，函数特征表和返回类型
## 多重继承

## 类模板

