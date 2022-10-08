# C++学习笔记 
个人通过学习C++，整理出C++的重难点，包括封装继承多态等面向对象的思想，持续更新
# Clion 的简单注意事项
1. .h做声明，.cpp做实现。main文件include .h文件即可
2. 想在clion中include库文件，需要修改CMakeLists，具体来说
- 先声明库文件路径
- 库文件
- 还需要链接target_link_libraries()
```
//假设项目名叫unix_networks，项目中主文件叫main.c
cmake_minimum_required(VERSION 3.21)//clion默认，不管
project(unix_networks C)//clion默认，不管

set(CMAKE_C_STANDARD 99)//clion默认，不管
#声明头文件的路径
set(INC_DIR  ./)

#声明链接库的路径

set(LINK_DIR  ./)

#引入头文件

include_directories(${INC_DIR})

#引入库文件

link_directories(${LINK_DIR})


add_executable(unix_networks main.c)
add_executable(client client.c)
target_link_libraries(unix_networks libunp.a)
target_link_libraries(client libunp.a)
```
3. 如何在clion中添加新的可执行文件
```
//client.c是想要运行的可执行文件
add_executable(client client.c)
target_link_libraries(client libunp.a)//把库文件链接上
```

# 第一章
## 1. 双冒号作用域运算符
::代表作用域  如果前面什么都不添加 代表全局作用

## 2. namespace命名空间
- 命名空间用途：解决**名称冲突**（解决全局和局部的关系）
- 命名空间下可以存放 ： 变量、函数、结构体、类…
- 命名空间必须要声明在**全局作用域**
- 命名空间可以**嵌套**命名空间
- 命名空间是开放的，可以随时将新成员添加到命名空间下（**不会覆盖**）
- 命名空间可以不取名字，如果不取名字就有点像static，全局直接用，不需要写作用于::
- 命名空间可以起别名(namespace newname=oldname)

## 3. using声明以及using编译指令
using声明：声明接下来的代码我都将使用这个作用于下的这个变量，using 			KingGlory::sunwukongId。

using编译指令：
- 声明接下来我都将使用这个这个命名空间的所有内容。using namespace KingGlory;
- 当using编译指令  与  就近原则同时出现，优先使用就近
- 当using编译指令有多个，需要加作用域 区分

## 4. const 链接属性
C语言下const修饰的全局变量默认是外部链接属性,可以直接在本项目的其他文件调用。
C++下const修饰的全局变量默认是内部链接属性，需要添加extern才能给别的文件调用

## 5. 引用
1. 引用就是起别名：类型 &别名=原名。引用的本质是指针常量
```cpp
int& aRef = a; 
//自动转换为 int* const aRef = &a;这也能说明引用为什么必须初始化
```
2. 建立对数组引用：
- 直接建立引用：int arr[10];	int(&pArr)[10] = arr;
- 先定义数组类型（typedef），再通过类型 定义（不学）

3. 引用的函数参数传递：void function(int &a)

# 第二章
## 1. 内联函数
c++从c中继承的一个重要特征就是效率。假如c++的效率明显低于c的效率，那么就会有很大的一批程序员不去使用c++了。在c中我们经常把一些短并且执行频繁的计算写成宏，而不是函数，这样做的理由是为了执行效率，宏可以避免函数调用的开销，这些都由预处理来完成。为了保持预处理宏的效率又增加安全性，而且还能像一般成员函数那样可以在类里访问自如，c++引入了**内联函数**(inline function). 内联函数为了继承宏函数的效率，没有函数调用时开销，然后又可以像普通函数那样，可以进行参数，返回值类型的安全检查，又可以作为成员函数。**内联函数是直接跑源码，不通过函数的调用**。
```cpp
inline int func(int a){return ++;}
```

函数的声明和实现都必须加关键字inline，才算内联函数。内联函数的确占用空间，但是内联函数相对于普通函数的优势只是省去了函数调用时候的压栈，跳转，返回的开销。我们可以理解为内联函数是以**空间换时间**。
注：类内部定义函数都会自动成为内联函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/fbda2ea61a744d4ca84377933d76ea3a.png)
<font color='red'>内联仅仅只是给编译器一个建议，编译器不一定会接受这种建议，如果你没有将函数声明为内联函数，那么编译器也可能将此函数做内联编译。一个好的编译器将会内联小的、简单的函数。 </font>
## 2. 占位参数
c++在声明函数时，可以设置占位参数。占位参数只有参数类型声明，而没有参数名声明。一般情况下，在函数体内部无法使用占位参数。
```cpp
void TestFunc01(int a,int b,int)
```

## 3. 函数重载
通过函数重载的条件，可以同时写多个同名函数，生成不同效果。
函数重载的条件：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f80e9bb69b394034a7937556c766143d.png)
extern “C”：由于C++对每个函数都会重新取名字（函数重载的原理），但是C不会，所以当我们在**C++想引用C写的函数**时，我们就得加上**extern “C”**告诉编译器用C语言方式作链接。
&ensp; &ensp; 在C的文件中加入：
![在这里插入图片描述](https://img-blog.csdnimg.cn/536bd119568847ebb585218a345ba262.jpeg)
## 4. class和struct的区别（访问权限的讨论）
c++中struct也可以使用函数，他们的唯一区别：
- struct默认的访问权限是public
- class默认的访问权限是private

![访问权限的说明](https://img-blog.csdnimg.cn/a9bcaab66dd64512a464fc86caa8f55e.png)
protected和private的区别：子类不可以访问父类private的内容，但是可以访问父类protected的内容。

**尽量将成员变量设置成私有，设置公共接口来让别人设置**:好处是自己可以控制读写的权限，并且可以对设置进行有效性的验证。
# 第三章
## 1. 构造函数和析构函数
对象的初始化和清理也是两个非常重要的安全问题，一个对象或者变量没有初始时，对其使用后果是未知，同样的使用完一个变量，没有及时清理，也会造成一定的安全问题。c++为了给我们提供这种问题的解决方案，**构造函数**和**析构函数**，这两个函数将会被编译器自动调用，分别完成**对象初始化**和**对象清理**工作。
![在这里插入图片描述](https://img-blog.csdnimg.cn/55ab689dc6a04a519546f616a25bd7fb.png)
**调用构造函数**或者说**创建对象**的四种方法：
1. 括号法：Person p1(10);// Person p1;
2. 显示法: Person p1=Person(10);//Person p1=Person();
3. 指针法: Person *p1 = new Person(10);//Person *p1 = new Person();
4. 隐式法: Person p1=10;//一般不用

注：不要用括号法调用无参构造函数  Person p3();  编译器认为代码是函数的声明。改成：Person p3=Person()

匿名对象：Person(10);
	特点：执行完立即释放

注意，拷贝构造函数的输入参数是const引用类型
Person(**const** Person **&** p);//这是一个拷贝构造函数
## 2. 构造函数调用规则
1. 默认情况下，c++编译器至少为我们写的类增加3个函数
- 默认构造函数(无参，函数体为空)
- 默认析构函数(无参，函数体为空)
- 默认拷贝构造函数，对类中所有的的非静态成员进行简单的值拷贝

2. 但是如果定义了普通构造函数，系统就不会默认提供无参构造函数，但是会提供默认拷贝构造函数
3. 但是如果提供了拷贝构造函数，则有参和无参的构造函数都不提供。

```cpp
//拷贝构造函数的语法
class Person
{
public:
    Person(char * name, int age)//构造函数
    {
        m_name=(char *) malloc(strlen(name)+1);
        strcpy(m_name,name);
        m_age=age;
    }
    Person(const Person & p)//拷贝构造函数
    {
        m_name=(char *) malloc(strlen(p.m_name)+1);
        strcpy(m_name,p.m_name);
        m_age=p.m_age;
    }
    ~Person()
    {
        cout<<"析构函数已调用"<<endl;
        free(m_name);
        m_name=NULL;
    }

    int m_age;
    char * m_name;
};

void test3()
{
    Person p1=Person("卢锦荣",20);
    Person p2=p1;
    cout<<p1.m_name<<"\n"<<p1.m_age<<endl;
    cout<<p2.m_name<<"\n"<<p2.m_age<<endl;
}
```
## 3. 深拷贝和浅拷贝
当你利用系统默认的拷贝构造函数时，可能会出错：
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;类中有成员是指针变量，并且指针指向动态分配的内存空间，当你用系统默认的拷贝构造，**会把两个对象的同一个成员指向同一个堆区（浅拷贝）**，所以当析构函数释放内存时，会把一个堆区释放两次
![在这里插入图片描述](https://img-blog.csdnimg.cn/ccc8dfa8fe1f4427b63de218fcb4cb5e.png)

## 4. 初始化列表
构造函数名称(值1，值2)：属性1(值1), 属性2(值2)
```cpp
//传统方式初始化
	Person(int a,int b,int c){
		mA = a;
		mB = b;
		mC = c;
	}
//初始化列表方式初始化
Person(int a, int b, int c):mA(a),mB(b),mC(c){}//mA、mB、mC为成员变量，abc为用于输入的值

```
## 5. 类对象做其他类的成员
当其他类对象作为本类成员，**会先构造其他类对象**，再构造自身，析构顺序和构造顺序相反（栈的原理）
## 6. explicit
禁止用户调用构造函数的隐式调用。
&ensp;&ensp;&ensp;隐式法: Person p1=10;

## 7. new与delete
new:在堆区开辟一片空间（和malloc一样）

c++不用malloc，用new。
new会调用构造函数，malloc不会
new返回的是地址，要用指针类型接收。

堆区开辟对象，一定会调用对象的无参构造函数，如果对象存在有参构造函数，而没有无参构造函数，则会报错（因为一定会调用对象的无参构造函数，而你没有）。

利用new来创建n个数组对象：
```cpp
Person* person = new Person[10];
delete person;//error
delete [] person;//对！如果在new表达式中使用[]，必须在相应的delete表达式中也使用[]
```
# 第四章
## 1. static
static（静态）: 
	1. 被static修饰的类型，空间将在**程序的生命周期内分配**。（记住这个结论！很关键，不管是变量，对象的变量，对象的函数都是如此）
	2. 还可以限定访问范围 static还有限定访问范围的作用（类似于匿名名字空间），用得少。

**1. 静态成员变量：**
- 静态成员变量**类内声明**，需要**类外全局初始化 int A::m_static=10**;
- 可以通过**对象/类名调用**

**2. 静态成员函数：**
- 可以通过对象/类名调用
- 　<font color='red'> **静态成员函数只能访问静态成员函数**</font>


## 2. 单例模式
一个类只能**实例化出一个对象**，称单例模式。具体实现：

- 私有化构造函数和拷贝构造函数，不允许创建对象。
- 在**私有化**中声明一个指向该对象的指针，类外进行初始化。目的：只能通过该指针访问这个类，并且限制可读不可写
- 该指针如何可读？public上给出一个静态对象函数返回该指针，限制可读

```cpp
class ChairMan
{
public:
    static ChairMan * GetInstance()//利用接口提供可读。
    //注意：静态成员变量只能通过静态成员函数调用
    {
        return singleMan;
    }
private:
    ChairMan(){}//将构造函数私有化，不允许创建对象
    ChairMan(const ChairMan & ){}//将拷贝构造函数私有化，不允许创建对象
    static ChairMan * singleMan;//放在private中，为了限制可读不可写
};

ChairMan* ChairMan::singleMan=new ChairMan;//给指针类型为主席的赋予一个空间。注：静态成员变量只能全局初始化

void test2()
{
    ChairMan * c1=ChairMan::GetInstance();

}
```
## 3. C++对象模型初探&ensp;
1. 一个空类的对象占用地址是1字节，原因是：
  &ensp;&ensp;&ensp;每个对象都在内存上保存一个地址，因此给空对象分配一个字节地址空间
2. **类中的成员变量 和 成员函数  是分开存储的**。**成员函数不存储在对象内部**，**并且和static一样，只存储一份**。只有非静态成员变量属于类对象上
## 4. this指针&ensp;
通过上例我们知道，c++的数据和操作也是分开存储，并且每一个非内联成员函数(non-inline member function)**只会诞生一份函数实例**，也就是说多个同类型的对象会共用一块代码。
![在这里插入图片描述](https://img-blog.csdnimg.cn/61beaaf4ab5c4794b49d185832a02d95.png)
&ensp;&ensp;&ensp;&ensp;那么问题是：这一块代码是如何区分那个对象调用自己的呢？利用this指针。

**this指针指向被调用的成员函数所属的对象。**
```cpp
public:
	Person(int age)
	{
		age=age;//error
		this->age = age;//正确写法
	}
	int age;
```

this指针的本质： 
```cpp
Person * const this;
```
**即 const修饰指针，所以this不可以更改!而指针this指向的值可以修改**
## 5.	空指针访问成员函数&ensp;
什么是空指针:
```cpp
Person*p=NULL;//或者 Person p;
```
如果成员函数中没有用到this指针，可以用空指针调用成员函数

## 6.	常对象和常函数&ensp;
4.5 结尾说到：**即 const修饰指针，所以this不可以更改!而指针this指向的值可以修改**。
但是如果想某个类的函数不允许修改成员变量该怎么办（**即指针this指向的值也不可以修改**）？ **使用常函数**

```cpp
//某个类中的函数
class Person
{
public:
void show_age() const //常函数修饰成员函数中的this指针，让指针指向的值不可以修改
{
	this->m_age=10;//error
	cout<<"age"=<<this->m_age<<endl;
}
int m_age;
}
```
常对象同理，构造对象时，给对象加上const属性，对象的成员变量就不可以更改。并且**常对象只能调用常函数**，不允许调用普通函数（因为普通函数可能会修改成员变量的值，所以不允许常对象调用普通函数）。
```cpp
const Person p=Person();
```

但是如果你想在**常函数或者常对象**中，让某些**特殊的属性仍然可以更改**，可以给变量加上关键字**mutalbe**
```cpp
mutable int m_A
```
## 7.	额外补充：类内声明类外实现的例子&ensp;
```cpp
//类内声明，类外实现
class Person1
{
public:
    Person1(int age);//构造函数
    void change_age(int age);
    void show_age();
    int M_age;
};
//构造函数的实现
Person1::Person1(int age)
{
    this->M_age=age;
}

void Person1::change_age(int age)
{
    this->M_age=age;
}
void Person1::show_age()
{
    cout<<"age="<<M_age<<endl;
}


void test4()
{
    Person1 p=Person1(11);
    p.show_age();
}
```

## 8. 额外补充：分文件的类成员函数的声明与实现
对于普通类，如果我们想分文件来声明与实现，一般是
头文件person.h(假设类名是Person)中声明，
person.cpp中#include "person.h" 并实现函数。
在主要运行程序 main.c中 #include "person.h"

代码实例:
person.h
```cpp
//.h文件只需要声明
#include <iostream>
using namespace std;

class Person
{
public:
    Person(string name, int age);
    void showPerson();
    string m_name;
    int m_age;
};
```

person.cpp
```cpp
//.cpp文件只需要实现
#include "person.h"
Person::Person(string name, string age)
{
this->m_name=name;
this->m_age=age;
}

void Person::showPerson()
{
    cout<<"姓名"<<this->m_name<<"年龄"<<this->m_age<<endl;
}
```

## 9.	友元 friend&ensp;
类的主要特点之一是数据隐藏，即类的私有成员无法在类的外部(作用域之外)访问。但是，有时候需要在**类的外部访问类的私有成员**，怎么办？
解决方法是使用**友元函数**，友元函数是一种**特权函数**，c++**允许这个特权函数访问私有成员**。

**全局函数**、**某个类中的成员函数**、甚至**整个类**声明为友元。

三种类型声明为友元的例子:
```cpp
class A{}//类做友元的例子

class B//类中某个成员函数做友元例子
{
void visit();//可以访问building私有
void visit2();//不可以访问building私有
}

class Building//主要做例子的类
{
	//利用friend关键字让全局函数 goodGay作为本类的友元函数，可以访问私有属性m_BedRoom
	friend void goodGay(Building * buliding);
	//类做友元
	friend class A;//A是一个类
	//类中成员函数做友元
	friend void B::visit();
private:
	string m_BedRoom; //私有成员变量
public:
	Building()//无参构造函数
	{
		this->m_BedRoom = "卧室";
	}


};

void goodGay(Building * buliding)
{
	cout << buliding->m_BedRoom << endl;
}
```
# 第五章(关于运算符重载那些事)
## 1. 运算符重载基本概念
运算符重载，就是对**已有的运算符重新进行定义**，赋予其另一种功能，以适应不同的数据类型。
 &emsp; &emsp;注意： 运算符重载只是一种”语法上的方便”,也就是它只是另一种函数调用的方式。在c++中，可以定义一个处理类的新运算符。这种定义很像一个普通的函数定义，只是函数的名字是关键字**operator@**,这里的@代表了被重载的运算符。

## 2. 重载+号运算符:
可以全局函数定义，也可以类内定义。
```cpp
Person operator+(Person &p1,Person&p2)
{
    Person temp;
    temp.m_A=p1.m_A+p2.m_A;
    temp.m_B=p1.m_B+p2.m_B;
    return temp;
}
Person p3=p1+p2;//本质：Person p3 = operator+(p1,p2)
```

## 3.重载<<左移运算符:
左移运算符作cout输出操作。对于想要输出对象的成员变量的重载<<，只能使用全局函数定义。
```cpp
//重载左移运算符，使其能输出对象的每个成员变量。
ostream & operator<<<<(ostream &cout ,Person &p1)
{
    cout<<p1.m_A<<p1.m_B;
    return cout;
}
Person p1;//构造一个对象p1
cout<<p1<<endl;
```

## 4. 重载前置++运算符
对对象中的成员变量做前置++操作。
```cpp
class MyInter
{
private:
    int m_Num;
public:
    MyInter()//构造函数
    {
        m_Num=0;
    }
    MyInter & operator++()//重载前置++运算符
    {
        this->m_Num++;
        return *this;
    }
};

MyInter MyInt;//构造一个对象MyInt
++MyInt;//重载前置++运算符
```
## 重载后置++运算符
对对象中的成员变量做后置++操作。后置++比前置++复杂
```cpp
    MyInter operator++(int)//int为占位参数，不代表具体含义，但是可以让编译器知道是后置++
    {
        //思路： 先保存修改前的变量，然后++，返回修改前的变量。
        MyInter temp=*this;
        this->m_Num++;
        return temp;//注意 返回的事值，不能是引用，原因比较复杂
    }
MyInter MyInt;//构造一个对象MyInt
MyInt++;//重载前置++运算符
 ```
## 重载函数调用运算符
小括号重载

```cpp
class MyPrint
{
public:
    void operator()(string  str)
    {
        cout<<str<<endl;
    }

};

void test3()
{
    MyPrint myPrint;
    myPrint("heelo");//重载函数调用运算符，称仿函数
}
```
## 重载指针运算符
## 重载赋值运算符
## 重载[]运算符
## 重载关系运算符
上面四章内容未看，先看day6继承先，有空再回看这四章内容。  



# 第六章 (开始继承！)
## 1. 强化训练-字符串类封装
05-06没看，晚上编一下代码实现它
## 2. 继承
### 2.1 继承的基本语法
继承就是把共同的共性的东西做父类，子类中额外构建一些自己的特性。避免代码的冗余。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1da8689c8b3a4893839bf2a39e737eec.png)
```cpp
//基本语法：class 子类：继承方式 父类
class News: public BasePage//BasePage是父类，News是子类
{
public:
		void content(){};//自己除了拥有父类所有的功能外，自己新增加的功能。
}
```
### 2.2 继承方式
三种继承方式：
- public ：    公有继承
- private ：   私有继承
- protected ： 保护继承

具体的区别如下图所示
![在这里插入图片描述](https://img-blog.csdnimg.cn/4664254cfb46468b99cba8d8ac9daab8.png)
总结：
-  父类的私有成员无论哪种继承方式均无法访问
- **公有继承**的前两个权限不变
- **保护继承**的前两个权限都是保护
- **私有继承**的前两个权限都是私有
### 2.3 继承中的对象模型
父类中的私有属性，子类其实继承了，只是被编译器隐藏了，子类访问不到，实际上私有属性还是会占用地址空间。
### 2.4 继承中的构造和析构顺序
**父类的构造/析构函数是不会被子类继承的，<font color='red'> 只会在构造子类的时候先调用父类的继承/析构函数 </font>。**

子类继承中：
&emsp;&emsp;**先调用父类的构造函数**，**再调用子类的构造函数**（先有爸爸再有儿子）。析构顺序与构造相反（栈）


进一步的，假设子类中调用了其他类：
&emsp;&emsp;先调用父类的构造函数，再调用其他类的构造函数，最后调用子类的构造函数。（注意，其他类的构造函数一定优先于本类构造函数）

![在这里插入图片描述](https://img-blog.csdnimg.cn/af4c003f692b44e4b07def421469fba3.png)


注意：当子类不存在有参构造函数，而只有无参构造函数，而父类中只存在有参构造函数，而不存在无参构造函数。构造子类对象时会报错（因为会调用父类的无参构造函数），有两种解决方法：
- 父类写一个无参构造函数
- 可以用初始化列表调用父类中的有参构造函数

```cpp
class Base
{
public:
	Base(int a)
	{
		this->m_A = a;
		cout << "这是一个有参构造函数" << endl;
	}
	int m_A;
};
class Son :public Base
{
public:
	Son2(int a) :Base2(a)  //利用初始化列表，调用父类的有参构造函数
	{
		cout << "这是子类的构造函数" << endl;
	}
};
Son son=Son(100);
```

父类中的构造、析构、拷贝构造、operator=都不会被子类继承。

### 2.5 继承中的同名成员处理
同名成员包括：成员变量、成员函数。

**当子类成员和父类成员同名时，子类依然从父类继承同名成员**。只是访问父类同名成员的方式不同了：
- 如果子类有成员和父类同名，子类**默认访问的是子类的成员**(本作用域，就近原则)
- 在子类**通过作用域::进行父类同名成员**的调用。

```cpp
void test1()
{
    Son s1;//构造一个子类
    cout<<s1.m_A<<endl;//子类的同名成员变量
    cout<<s1.Base::m_A<<endl;//父类的同名成员变量

	s1.fun1();//子类的同名成员函数
	s1.Base::fun1();//父类的同名成员函数
}
```

此外对于同名函数而言：如果子类重新定义了父类的同名成员函数，那么子类就会自动**隐藏**掉**父类**中所有**重载版本的同名成员**，但是还是可以通过作用域的方式来调用。（这行话如果看不太懂就不看了，反正意思就是调用父类同名成员都得加上作用域，不管有没有父类是否重载；但是如果不同名，不需要作用域就可以直接调用）

**关于static同名静态成员的特点：**
和前面的结论其实一样，也是要通过   &emsp;s1.Base::变量/函数 &emsp;来调用；

但是静态成员因为是单独分配内存（静态成员的空间将在**程序的生命周期内分配**），所以可以直接通过**类名调用**。
```cpp
Son s1=Son()
Son.m_A;//调用子类同名静态变量
s1.m_A;//和上面一样

Base.m_A//调用父类同名静态变量
s1.Base::m_A//调用父类同名静态变量

//同名成员函数不再做示范，同理。
```

### 2.6 多继承
多继承概念：
&emsp;&emsp;一个子类可以拥有多个父类。但是由于多继承是非常受争议的，从多个类继承可能会导致函数、变量等同名导致较多的歧义，只能利用声明作用域的方式解决。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/55c1c606712a4df99a1b9f9e7154855f.png)

### 2.7  菱形继承和虚继承
菱形继承的概念：
&emsp;&emsp;两个派生类继承同一个基类，而又有某个类** **，这种继承被称为**菱形继承**，或者钻石型继承。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/0bb1475913fa4177858b190fa7ab923b.png)
这种菱形继承带来的问题：
- 草泥马**继承自动物的函数和数据继承了两份**，造成资源的浪费。

解决方法：
	&emsp;&emsp;利用虚继承(virtual)解决菱形继承问题
```cpp
//动物类
class Animal
{
public:
	int m_Age; // 年龄
};

//羊类
class Sheep : virtual public Animal{};
//驼类
class Tuo : virtual public Animal{};

//羊驼类
class SheepTuo : public Sheep, public Tuo
{
};
//此时如果不用虚继承，那么羊驼类会存在两个m_Age成员
```
当发生虚继承后，sheep和tuo类中继承了一个  **vbptr指针---虚基类指针**  ，指向的是一个 **虚基类表  vbtable**。虚基类表中记录了  偏移量 ，通过偏移量 可以找到唯一的一个m_Age，具体来说，利用地址偏移找到 vbtable中的偏移量 并且访问数据

# 第七章 (开始多态！)
## 1 多态
### 1.1 多态的概念
c++支持**静态多态**(编译时多态)和**动态多态**(运行时多态)，运算符重载和函数重载就是编译时多态，而派生类和虚函数实现运行时多态。

静态多态和动态多态的区别就是函数地址是早绑定(静态联编)还是晚绑定(动态联编):
- 如果函数的调用，在编译阶段就可以确定函数的调用地址，并产生代码，就是静态多态(编译时多态)，就是说地址是早绑定的。
- 而如果函数的调用地址不能编译不能在编译期间确定，而需要在运行时才能决定，这这就属于晚绑定(动态多态,运行时多态)。

此外：**有父子关系的两个类的指针或者引用，是可以直接转换的，不需要我们人为转换**。也就是可以直接父类的引用接收子类的对象
```cpp
void doSpeak(Animal & animal)
{
	animal.speak();
}
Cat cat;//Cat为Animal的子类
doSpeak(cat);//正确。但是调用的是Animal::speak,而不是Cat::speak
```
**重点：**
由于父类指针或者引用能指向子类对象，所以上述代码会产生一个问题：**他输出的是父类的函数，而不是子类的函数**，造成这个问题的原因是由于早绑定引起的，因为编译器在只有Animal地址时并不知道要调用的正确函数。<font color='red'> **编译器根据指向对象的指针或引用的类型来选择函数调用** </font>。这个时候由于doSpeak的参数类型是Animal&,编译器确定了应该调用的speak是Animal::speak的，而不是真正传入的对象Cat::speak。

解决此方法是通过虚函数virtual，由此产生了多态。

多态产生的条件：
1. 先存在**继承**关系
2. 父类中有**虚函数**(virtual)，并且子类**重写父类中的虚函数**。
3. 父类的指针或者引用，指向子类的对象(Animal * animal=new Cat)。最关键:只有当此条件发生时，才产生多态。

&emsp;&emsp;注意：**重写**的定义：**子类重写**父类中的**虚函数**，函数返回值相同，形参相同


总结：
&emsp;&emsp;当父类写了虚函数后，类的内部结构发生了改变，多了一个叫vfptr虚函数表指针，指针指向vftable虚函数表。虚函数表内部记录着虚函数的入口地址。**当父类的指针或者引用指向子类对象时，发生多态**。调用虚函数的时候从虚函数表中找到函数的入口地址。如果子类重写了父类的虚函数，那么虚函数表中原本记录着父类的函数入口地址将被替换成子类的函数入口的地址。
```cpp
class Animal
{
public:
    virtual void speak()//虚函数
    {
        cout<<"动物在说话"<<endl;
    }
};

class Cat:public Animal
{
public:
	//子类重写了父类的虚函数。所以当父类指针指向子类对象时，
    //虚函数表中的函数入口地址是该地址，而不是父类的函数地址。
    //子类的speak()函数的virtual可加可不加
    void speak()
    {
        cout<<"小猫在说话\n"<<endl;
    }
};

void test1()
{
    Animal * animal=new Cat;//父类指针指向子类对象

    animal->speak();
}
```

输出结果：	
```
小猫在说话
Process finished with exit code 0
```


案例：用多态实现两数相加或者相减（案例看懂他，就能明白多态的意义）
```cpp


class AbstractCalculator //父类
{
public:
	virtual int getResult()//抽象了一个函数，留着给子类继承
	{
		return 0;
	}
}
//父类指针指向子类对象，并且子类重写了父类的虚函数，发生多态！
AbstractCalculator * calculator=new AddCalculator;//加法类，继承自AbstractCalculator
calculator.getResult(1,5);//结果为6

delete calcultor;//清空加法对象的空间

//父类指针指向子类对象，并且子类重写了父类的虚函数，发生多态！
calculator=new SubCalculator;//减法类
calculator.getResult(1,5);//结果为-4
```

<font color='red'> 这就是多态的意义：当用同样的父类指针指向不同的子类对象时，调用同样的函数，输出却是完全不同的，这就是多态的意义。 </font>

这个意义就是所提倡的设计原则：开闭原则，对扩展进行开放，对修改进行关闭。

所以多态的好处：
- 代码可读性强
- 组织结构清晰
- 扩展性强

### 1.2 纯虚函数和抽象基类

在1.1 最后一个案例《用多态实现两数相加或者相减》中，给父类的虚函数写了一段对这个函数函无意义的代码（return 0;）。其实我们根本没有必要写这个“return 0;”，只需要改成纯虚函数就行。

**纯虚函数**: 纯虚函数不需要写实现，只需要提供接口。
```cpp
class A//抽象类
{
public:
	virtual int getResult()=0;//纯虚函数
}
```
如果一个类中包含了纯虚函数，那么该类就**无法实例化对象**。 该类通常被称为**抽象类**。
抽象类的子类，必须**重写**父类的纯虚函数，否则也属于抽象类。

纯虚函数的意义在于：继承抽象类的子类必须要重写纯虚函数，不然就没办法实例化对象！

这个纯虚函数被称为公共的接口。

在设计时，**常常希望基类仅仅作为其派生类的一个接口**，而不希望用户实际的创建一个基类的对象。

多态中抽象类与纯虚函数的实际案例：
假设要设计两套动作：冲咖啡和冲茶叶，其实整体流程大同小异，只是具体实现上有细微差别。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7b414adfcc8042298e1be2d529f89acf.png)
```cpp
//抽象制作饮品
class AbstractDrinking//抽象类
{
public:
	//烧水
	virtual void Boil() = 0;//纯虚函数
	//冲泡
	virtual void Brew() = 0;
	//倒入杯中
	virtual void PourInCup() = 0;
	//加入辅料
	virtual void PutSomething() = 0;
	//规定流程
	void MakeDrink(){
		Boil();
		Brew();
		PourInCup();
		PutSomething();
	}
};

//制作咖啡
class Coffee : public AbstractDrinking{
public:
	//烧水
	virtual void Boil(){//这里写不写virtual都可以。 
		cout << "煮农夫山泉!" << endl;
	}
	//冲泡
	virtual void Brew(){
		cout << "冲泡咖啡!" << endl;
	}
	//倒入杯中
	virtual void PourInCup(){
		cout << "将咖啡倒入杯中!" << endl;
	}
	//加入辅料
	virtual void PutSomething(){
		cout << "加入牛奶!" << endl;
	}
};

//制作茶水
class Tea : public AbstractDrinking{
public:
	//烧水
	virtual void Boil(){
		cout << "煮自来水!" << endl;
	}
	//冲泡
	virtual void Brew(){
		cout << "冲泡茶叶!" << endl;
	}
	//倒入杯中
	virtual void PourInCup(){
		cout << "将茶水倒入杯中!" << endl;
	}
	//加入辅料
	virtual void PutSomething(){
		cout << "加入食盐!" << endl;
	}
};

//业务函数
void DoBussiness(AbstractDrinking* drink){
	drink->MakeDrink();
	delete drink;
}

void test(){
	DoBussiness(new Coffee);//多态的实现
	cout << "--------------" << endl;
	DoBussiness(new Tea);//多态的实现
}
```

### 1.3 虚析构和纯虚析构
**父类的构造/析构函数是不会被子类继承的，<font color='red'> 只会在构造子类的时候先调用父类的继承/析构函数 </font>。**

为什么需要虚析构：
&emsp;&emsp;当**使用多态**（前提）时，即父类指针指向子类对象；**当delete时，父类指针只会调用父类析构函数，不会调用子类析构函数**。因此需要将父类的析构函数变为虚析构函数，以此才调用子类的析构函数。

```cpp
class Animal1//父类
{
public:
    virtual void speak()
    {
        cout<<"动物在说话"<<endl;
    }
    virtual ~Animal1()//虚析构函数。
    {
    cout<<"Animal1的析构函数调用"<<endl;
	}
};

```

纯虚析构和其他的纯虚函数不同，除了有声明(~A()=0)，他还需要实现，所以类内声明，类外实现。

```cpp
class Animal1//父类
{
public:
    virtual void speak()
    {
        cout<<"动物在说话"<<endl;
    }
    virtual ~Animal1()=0;//纯虚析构函数。
};

Animal1::~Animal1()//类内声明，类外实现。
{
    cout<<"Animal1的析构函数调用"<<endl;
}
```
&emsp;&emsp;注意：如果类的目的不是为了实现多态，作为基类来使用，就不要声明虚析构函数，反之，则应该为类声明虚析构函数。

### 1.4	向上类型转换和向下类型转换
父转子：   向下类型转换   不安全（地址越界）
子转父：   向上类型转换   安全
如果发生多态，那么转换永远都是安全的
结合这张图理解。
![在这里插入图片描述](https://img-blog.csdnimg.cn/70ce04e1a7614daa8159dd76010e204b.png)
### 1.5	重载、重写、重定义
重新回顾下三个定义

1. 重载，同一作用域的同名函数
- 同一个作用域
- 参数个数，参数顺序，参数类型不同
- 和函数返回值，没有关系
- const也可以作为重载条件  //do(const Teacher& t){}  do(Teacher& t)
2. 重定义（隐藏）
- 有继承
- 子类（派生类）重新定义父类（基类）的同名成员（非virtual函数）
3. 重写（覆盖）
- 有继承
- 子类（派生类）重写父类（基类）的virtual函数
- 函数返回值，函数名字，函数参数，必须和基类中的虚函数一致

### 1.5	电脑组装案例实现
案例分析：假设一个电脑由三个零件组成，内存显卡CPU，三个零件可能由不同厂商构成，所以需要我们构建三个父类的抽象类，只需要提供接口（虚函数）即可。

具体代码实现
```cpp
//抽象化三个类
class CPU
{
public:
    virtual void calculate()=0;
};

class VideoCard
{
public:
    virtual void display()=0;
};

class Memory
{
public:
    virtual void storage()=0;
};
//电脑类
class Computer
{
public:
    Computer(CPU * cpu, VideoCard* videocard, Memory * memory)
    {
        m_cpu=cpu;
        m_videocard=videocard;
        m_memory=memory;
    }
    CPU *m_cpu;
    VideoCard * m_videocard;
    Memory * m_memory;
    void dowork()
    {
        m_cpu->calculate();
        m_videocard->display();
        m_memory->storage();
    }
};
//定义不同厂商CPU、显卡、内存条
class InterCPU:public CPU
{
    virtual void calculate()
    {
        cout<<"这是因特尔cpu在计算"<<endl;
    }
};

class AppleCPU:public CPU
{
    virtual void calculate()
    {
        cout<<"这是苹果cpu在计算"<<endl;
    }
};

class InterVideoCard:public VideoCard
{
    virtual void display()
    {
        cout<<"这是因特尔显卡在显示"<<endl;
    }
};

class AppleVideoCard:public VideoCard
{
    virtual void display()
    {
        cout<<"这是苹果显卡在显示"<<endl;
    }
};

class InterMemory:public Memory
{
    virtual void storage()
    {
        cout<<"这是因特尔内存在存储"<<endl;
    }
};

class AppleMemory:public Memory
{
    virtual void storage()
    {
        cout<<"这是苹果内存在存储"<<endl;
    }
};

void test4()
{
	//苹果配件
    VideoCard * appleVC=new AppleVideoCard;
    CPU * applecpu=new AppleCPU;
    Memory * applememory= new AppleMemory;
	//Inter配件
    VideoCard * interVC=new InterVideoCard;
    CPU* intercpu=new InterCPU;
    Memory * intermemory=new InterMemory;
    
    Computer c1 =Computer(applecpu,interVC,applememory);
    c1.dowork();

}
```

运行结果：
```
这是苹果cpu在计算
这是因特尔显卡在显示
这是苹果内存在存储
```

# 第八章 (C++模版)
## 1. 函数模板
c++提供两种模板机制:**函数模板**和**类模板**
### 1.1 函数模板的概念
c++提供了函数模板(function template.)所谓函数模板，实际上是建立一个通用函数，其函数类型和**形参类型**不具体制定，**用一个虚拟的类型来代表**。这个通用函数就成为函数模板。凡是函数体相同的函数都可以用这个模板代替，不必定义多个函数，只需在模板中定义一次即可。在调用函数时系统会根据实参的类型来取代模板中的虚拟类型，从而实现不同函数的功能。


```cpp
//利用模板实现通用交换函数
template <typename T>//T代表一个通用的数据类型
//typename也可以用class
void mySwap(T&a,T&b)
{
    T temp=a;
    a=b;
    b=temp;
}

void test1()
{
	int a=10,b=20;
	//1、自动类型推导，必须推导出一致的数据类型，才可以使用模板
	mySwap(a,b);
	//2、显示指定类型
	mySwap<int>(a,b);
}
```
两种方式使用函数模板：
1. 自动类型推导，必须推导出一致的数据类型，才可以使用模板
2. 显示指定类型

注意：template \<typename T>是告诉 编译器**紧跟着的下一个函数或者类**中出现T不要报错（记住是紧跟着下一个）。 而且**必须通过上述两种方式的任意一种告诉编译器T的数据类型。**

泛型编程 – 模板技术 特点：类型参数化

### 1.2 当存在模板函数和普通函数时的规则
当写了一个函数模板，又重载了一个普通函数时，就会发生函数模板和普通函数都存在的情况。
```cpp
template <class T>
void myPrint(T a, T b){}
void myPrint(int a, int b){}
```

**区别**：
函数模板：如果使用自动类型推导，是不可以发生隐式类型转换的（char-> int）
普通函数：可以发生隐式类型转换

**调用规则**：
- 如果函数模板和普通函数都可以调用，那么优先调用普通函数
- 如果想强制调用函数模板，可以使用空模板参数列表
&emsp;&emsp;myPrint **<>** (a, b);
- 函数模板也可以发生函数重载
- 如果函数模板能产生更好的匹配，那么优先使用函数模板

### 1.3 函数模板实现机制
函数模板机制结论：
- 编译器并不是把函数模板处理成能够处理任何类型的函数
- **函数模板通过具体数据类型产生不同的函数**，具体来说，如果你传入的参数是int，则函数模板会根据你传入的参数产生一个新的函数----接收int类型的函数，通过函数模板产生的函数称为模板函数。
- 编译器会对函数模板进行**两次编译**，在声明的地方对模板代码本身进行编译，在调用的地方对参数替换后的代码进行编译。
### 1.4 函数模板局限性
前面结论说到，编译器并不是把函数模板处理成能够处理任何类型的函数。比如交换两个数的函数模板，如果传入的是结构体，或者数组，甚至一个类，模板就解决不了。这就是模板的局限性

那么如何解决模板的局限性呢？
&emsp;&emsp;为了解决这种问题，可以利用**重载** 或者**具体化技术**，为这些特定的类型提供具体化的模板。

具体化技术：
**template<>** bool myCompare(Person & p1, Person & p2);
//其实不加template<>好像也可以


## 2. 类模板
### 2.1 类模板的概念
类模版与函数模板的区别：
1. 类模板不可以使用自动类型推导，只能用显示指定类型
2. 类模板 可以有默认参数

```cpp
//类模板
template <class T1, class T2>
//template <class NAMETYPE, class AGETYPE=int>//可以有默认参数
class Person1
{
public:
    Person1(T1 name, T2 age)
    {
        this->m_name=name;
        this->m_age=age;
    }
    void showPerson()
    {
    	cout<<"名字"<<this->m_name<<this->m_age<<endl;
    }
    T1 m_name;
    T2 m_age;
};

void test3()
{
    Person1<string, int> p1=Person1<string, int>("小卢",13);//显示指定类型
    //Person1<string> p1=Person1<string>("小卢",13);//有默认参数情况下的
    cout<<p1.m_name<<endl;
}
```

### 2.2 类模板的成员函数是什么时候创建的
类模板的成员函数不是一开始创建好的，只有在运行阶段，确定了T的数据类型，才会创建。

### 2.3 类模板做函数参数

当dowork函数想调用类模板作为函数的参数，有以下三种方式
1. 指定传入类型（最常用）
void doWork(**Person <string, int>**&p)
2. 参数模板化
template<class T1, class T2>
void doWork2(Person <T1, T2>&p)
3. 整个类 模板化
template<class T>
void doWork3( T &p)


```cpp
//类模板
template <class T1, class T2>
class Person1
{
public:
    Person1(T1 name, T2 age)
    {
        this->m_name=name;
        this->m_age=age;
    }
    T1 m_name;
    T2 m_age;
    void showPerson(){}
};

//1 指定传入类型（最常用）
void doWork(Person <string, int>&p)
{
p.showPerson()
}
//2 参数模板化
template<class T1, class T2>
void doWork2(Person <T1, T2>&p)
{
p.showPerson()
}
//3 整个类 模板化
template<class T>
void doWork3( T &p)
{
p.showPerson()
}
```
### 2.4 类模板遇到继承的问题
继承时，必须指定出父类中的T数据类型，才能给子类分配内存。
有两种方式
1. 在子类中明确指定出父类的数据类型（int / char 等）
2. 在子类中，再创造类模板
```cpp
template <class T>
class Base
{
public:
	T m_A;
};
//方法1：指定出明确的数据类型
class Son1 : public Base <int>//如果要继承，必须指定出数据类型
{
};
//方法2
//或者 子类也可以不写死数据类型，再指定一个类模板
template <class T1, class T2>
class Son2: public Base<T2>
{
public:
	T1 m_B;
}
```

### 2.5 类模板的类内声明，类外实现
```cpp
//类模板的类内声明，类外实现
template <class T1, class T2>
class Person2
{
public:
	//类内声明，类外实现。
    Person2(T1 name, T2 age);
    void showPerson();
    T1 m_name;
    T2 m_age;
};

template <class T1, class T2>
Person2<T1,T2>::Person2(T1 name, T2 age)
{
this->m_name=name;
this->m_age=age;
}

template <class T1, class T2>
 //就算没有用到T1和T2，但是类模板的类外实现就得加上
void Person2<T1,T2>::showPerson()
{
    cout<<"姓名"<<this->m_name<<"年龄"<<this->m_age<<endl;
}
```
### 2.6 类模板的成员函数分文件声明与实现
存在问题：
类模板的成员函数分文件声明与实现与普通类有个区别，就是在主函数main.cpp中不能 #include <类.h>, 会报错，除非#include <类.cpp>。但是一般别人不会给你cpp源码，

解决方法：
**所以一般类模板不会分文件声明与实现**，一般声明与实现会写在一个person.h的头文件中，但是正经的.h文件不能有实现，**所以后缀会改成person.hpp。** 特指类模板的声明与实现才会用到后缀 **.hpp**。

### 2.7 类模板与友元函数

友元函数分为类内实现和类外实现。
类外实现比较复杂，讨论类内实现
```cpp
//友元函数类内实现案例
template <class T1, class T2>
class Person
{
public:
	//这其实是一个全局函数，只是类内实现的。
	friend void printPerson(Person<T1, T2>&p)
	{
		cout<<m_name<<endl;
	}
    Person(T1 name, T2 age);
private:
    T1 m_name;
    T2 m_age;
};
```

# 第九章（类型转换、异常）
## 1. C++类型转换
**一般情况下，尽量少的去使用类型转换，除非用来解决非常特殊的问题。**

使用C风格的强制转换可以把想要的任何东西转换成我们需要的类型。那为什么还需要一个新的C++类型的强制转换呢？

新类型的强制转换可以提供更好的控制强制转换过程，允许控制各种不同种类的强制转换。C++风格的强制转换其他的好处是，它们能更清晰的表明它们要干什么。程序员只要扫一眼这样的代码，就能立即知道一个强制转换的目的。

### 1.1 静态转换 
语法：static_cast<目标类型>(原变量/原对象)
1. 用于基类（父类）和派生类（子类）之间指针或引用的转换。
- 进行上行转换（把派生类的指针或引用转换成基类表示）是安全的；
- 进行下行转换（把基类指针或引用转换成派生类表示）时，由于没有动态类型检查，所以是不安全的。
2. 允许内置数据类型转换，如把int转换成char，把char转换成int。
```cpp
char a='a';
double d = static_cast<double>(a);
//把'a'转为double型，并让d接收。
```

### 1.2 动态转换 
语法：dynamic_cast<目标类型>(原变量/原对象)

和静态转换差不多，但是比静态转换安全，具有检查的功能。如果发现不安全，就不给你转。

与静态转换的区别：
- 不允许内置数据类型转换
- 不允许子转父（下行转换，不安全）

目前看来，只允许父转子。

### 1.3 常量转换
语法：const_cast<目标类型>(原变量)
常量转换用来修改指针/引用类型的const属性。

- 常量指针被转化成非常量指针，并且仍然指向原来的对象；
- 常量引用被转换成非常量引用，并且仍然指向原来的对象；

注意:不能将非指针和非引用的变量使用const_cast操作符去移除它的const.

```cpp
const int * p = NULL;
int *np= const_cast<int *>(p);
//移除const属性
```
## 2异常
### 2.1异常的基本概念

处理异常是一种思想：让一个函数在发现了自己无法处理的错误时抛出（throw）一个异常，然后它的（直接或者间接）调用者能够处理这个问题。在所有支持异常处理的编程语言中（例如java），要认识到的一个思想：在异常处理过程中，由问题检测代码可以抛出一个对象给问题处理代码，通过这个对象的类型和内容，实际上完成了两个部分的通信，通信的内容是“出现了什么错误”。当然，各种语言对异常的具体实现有着或多或少的区别，但是这个通信的思想是不变的。

一句话总结：**异常处理就是处理程序中的错误。所谓错误是指在程序运行的过程中发生的一些异常事件（如：除0溢出，数组下标越界，所要读取的文件不存在,空指针，内存不足等等）。**

C语言的异常处理机制：
- 在C语言的世界中，对错误的处理总是围绕着两种方法：一是使用整型的返回值标识错误；二是使用errno宏（可以简单的理解为一个全局整型变量）去记录错误。当然C++中仍然是可以用这两种方法的。
- 这两种方法最大的缺陷就是会出现不一致问题。例如有些函数返回1表示成功，返回0表示出错；而有些函数返回0表示成功，返回非0表示出错。        
- 还有一个缺点就是函数的返回值只有一个，你通过函数的返回值表示错误代码，那么函数就不能返回其他的值。当然，你也可以通过指针或者C++的引用来返回另外的值，但是这样可能会令你的程序略微晦涩难懂。

c++异常机制相比C语言异常处理的优势?
- 函数的返回值可以忽略，但异常不可忽略。如果程序出现异常，但是没有被捕获，程序就会终止，这多少会促使程序员开发出来的程序更健壮一点。而如果使用C语言的error宏或者函数返回值，调用者都有可能忘记检查，从而没有对错误进行处理，结果造成程序莫名其面的终止或出现错误的结果。
- 整型返回值没有任何语义信息。而异常却包含语义信息，有时你从类名就能够体现出来。
- 整型返回值缺乏相关的上下文信息。异常作为一个类，可以拥有自己的成员，这些成员就可以传递足够的信息。
- 异常处理可以在调用跳级。这是一个代码编写时的问题：假设在有多个函数的调用栈中出现了某个错误，使用整型返回码要求你在每一级函数中都要进行处理。而使用异常处理的栈展开机制，只需要在一处进行处理就可以了，不需要每级函数都处理。

### 2.2 异常的基本语法
知识点：
1. 异常的处理关键字：
try &emsp;&emsp; throw &emsp;&emsp; catch
2. 把可能出现异常的代码，放入try块。
3. catch（类型）。如果想捕获任意类型的可以catch(...)
4. 如果捕获的异常在当前不想处理，可以继续向上跑出，利用throw
5. 异常必须要有函数进行处理，如果没有任何处理，程序会直接中断。
6. 抛出的异常可以是自定义数据类型

**c++异常处理使得异常的引发和异常的处理不必在一个函数中**，这样底层的函数可以着重解决具体问题，而不必过多的考虑异常的处理。上层调用者可以在适当的位置设计对不同类型异常的处理。

异常代码示例
```cpp
#include <iostream>
#include <string>
using namespace std;

class MyException//自己定义一个异常
{
public:
    void printError()
    {
        cout<<"我自己的异常"<<endl;
    }
};

int myDivision(int a, int b)//该函数作为抛出异常的函数
{
    if(b==0)
    {
//        throw 1;//抛出int类型的异常
//        throw 'a';//抛出char类型的异常
//        throw MyException();//抛出匿名对象
        throw 3.14;//抛出double异常
    }
    return a/b;
}

void test()
{
    int a=10,b=0;
    try
    {
        myDivision(a,b);
    }
    catch(int)
    {
        cout<<"整数类型的异常捕获"<<endl;
    }
    catch(char)
    {
        cout<<"char类型的异常捕获"<<endl;
    }
    //抛出的是 throw MyException();
    //catch(MyException e)会调用拷贝构造函数，效率会低，
    //改成catch(MyException &e)会好一点，具体看2.4
    catch(MyException e)//抛出的异常可以是自定义数据类型
    {
        e.printError();
    }
    catch(...)//捕获任意类型的异常
    {
        cout<<"其他类型的捕获"<<endl;
        throw;//如果捕获的异常在当前不想处理，可以继续向上跑出，利用throw
    }
}
int main() {
    try
    {
        test();
    }
    catch(...)//下层不想处理的异常，抛给当层做处理
    {
        cout<<"其他类型异常捕获"<<endl;
    }
    return 0;
}
```

### 2.2 栈解旋
从进入try块起，到异常被抛掷前，这期间在栈上构造的所有对象，都会被自动析构。析构的顺序与构造的顺序相反，这一过程称为栈的解旋

### 2.3 异常的接口声明
为了加强程序的可读性，可以在函数声明中列出可能抛出异常的所有类型，例如：void func() throw(A,B,C);这个函数func能够且只能抛出类型A,B,C及其子类的异常。

一个不抛任何类型异常的函数可声明为:void func() throw()

如果一个函数抛出了它的异常接口声明所不允许抛出的异常,unexcepted函数会被调用，该函数默认行为调用terminate函数中断程序。

```cpp
//异常接口声明
void func() throw(int , double)
{
	throw 3.14;
}
```

### 2.4 异常类的生命周期
- 抛出的是 throw MyException();  catch (MyException e) 调用拷贝构造函数 效率低（构造了两个对象，一个匿名对象立刻释放，一个复制的对象）
- **抛出的是 throw MyException();  catch (MyException &e)  只调用默认构造函数 效率高 推荐**（只构造出一个对象匿名，用一个别名接一个匿名对象，所以不会立即释放）
- 抛出的是 throw &MyException(); catch (MyException *e) 对象会提前释放掉，不能再继续操作。（构造了一个匿名对象，出了作用域就释放）
- 抛出的是 new MyException();   catch (MyException *e) 只调用默认构造函数 自己要管理delete（在堆区构建了一个对象，用完要自己delete）

### 2.5 利用多态实现异常类

直接看案例：该案例也可以看出多态的好处！
需求：提供父类异常类，并在子类中重写virtual void printError()。通过调用父类的同一个虚函数来打印出不同的异常的结果（<font color='red'> **同一个函数由于父类指向的子类对象的不同，而打印出不同的内容，这就是多态** </font>）

```cpp
class BaseException
{
public:
    virtual void printError()=0;
};

class NULLPointerException:public BaseException
{
public:
    virtual void printError()
    {
        cout<<"空指针异常"<<endl;
    }
};
class OutOfRangeException:public BaseException
{
public:
    virtual void printError()
    {
        cout<<"越界异常"<<endl;
    }
};

void dowork()//用来抛异常的函数
{
//    throw NULLPointerException();
    throw OutOfRangeException();
}
int main()
{
    try
    {
        dowork();
    }
    catch(BaseException & e)//父类引用指向子类对象
    {
        e.printError();
    }
}
```
### 2.6 系统标准异常
#include \<stdexcept\>

标准库中也提供了很多的异常类，它们是通过类继承组织起来的。异常类继承层级结构图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/b3d02ff14cdb4c469e72e9eb21e3b983.png)
不用看，大概知道就行。

调用系统异常示例：对类中的年龄做一个判断，若超出限制就抛出系统异常
```cpp
#include <iostream>
#include <string>
#include <stdexcept>
using namespace std;

//需求：对类中的年龄做一个判断，若超出限制就抛出系统异常
class Person
{
public:
    Person(int age)
    {
        if (age<0 || age>150)
        {
            throw out_of_range("年龄超出界限");
        }
        else
        {
            m_Age=age;
        }
    }
    int m_Age;
};
void test3()
{
    try
    {
        Person p=Person(151);
    }
    catch(exception &e)//用基类的exception接住就行
    {
        cout<<e.what()<<endl;
    }
}
```

### 2.7 编写自己的异常
需求：继承系统提供的父类异常类exception，编写自己的异常类。对类中的年龄做一个判断，若超出限制就抛出自己写的异常。

继承父类异常类exception只需要重写.what()函数就好，但是要重写.what()还是有一些细节要写

```cpp
//需求：继承系统提供的父类异常类exception，编写自己的类。
class MyOutOfRangeException:public exception
{
public:
    MyOutOfRangeException(const char * str)//构造函数
    {
        //char*可以隐式转化为string，反之不行
        this->m_errorInfo= str;
    }
    MyOutOfRangeException(string str)//重载构造函数
    {
        this->m_errorInfo= str;
    }
    virtual const char* what() const//重写.what()函数
    {
        //将string转为const char *
        return m_errorInfo.c_str();
    }
    string m_errorInfo;
};
class Person1
{
public:
    Person1(int age)
    {
        if (age<0 || age>150)
        {
            throw MyOutOfRangeException("年龄超出界限");
        }
        else
        {
            m_Age=age;
        }
    }
    int m_Age;
};
int main ()
{
    try
    {
        Person1 p1=Person1(151);
    }
    catch (exception &e)
    {
        cout<<e.what()<<endl;
    }
}
```
```
输出结果：
年龄超出界限
```
