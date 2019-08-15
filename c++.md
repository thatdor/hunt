[TOC]
# C/C++
## const
1. 修饰变量，说明该变量不可以被改变；
2. 修饰指针，分为指向常量的指针和指针常量；
3. 常量引用，经常用于形参类型，即避免了拷贝，又避免了函数对值的修改；
4. 修饰成员函数，说明该成员函数内不能修改成员变量。
```
// 类
class A
{
private:
    const int a;                // 常对象成员，只能在初始化列表赋值

public:
    // 构造函数
    A() { }
    A(int x) : a(x) { }        // 初始化列表

    // const可用于对重载函数的区分
    int getValue();             // 普通成员函数
    int getValue() const;       // 常成员函数，不得修改类中的任何数据成员的值
};

void function()
{
    // 对象
    A b;                        // 普通对象，可以调用全部成员函数
    const A a;                  // 常对象，只能调用常成员函数、更新常成员变量
    const A *p = &a;            // 常指针
    const A &q = a;             // 常引用

    // 指针
    char greeting[] = "Hello";
    char* p1 = greeting;                // 指针变量，指向字符数组变量
    const char* p2 = greeting;          // 指针变量，指向字符数组常量
    char* const p3 = greeting;          // 常指针，指向字符数组变量
    const char* const p4 = greeting;    // 常指针，指向字符数组常量
}

// 函数
void function1(const int Var);           // 传递过来的参数在函数内不可变
void function2(const char* Var);         // 参数指针所指内容为常量
void function3(char* const Var);         // 参数指针为常指针
void function4(const int& Var);          // 引用参数在函数内为常量

// 函数返回值
const int function5();      // 返回一个常数
const int* function6();     // 返回一个指向常量的指针变量，使用：const int *p = function6();
int* const function7();     // 返回一个指向变量的常指针，使用：int* const p = function7();
```
## static
1. 修饰普通变量，修改**变量的存储区域**和**生命周期**，使变量存储在静态区，在 main 函数运行前就分配了空间，如果有初始值就用初始值初始化它，如果没有初始值系统用默认值初始化它。
2. 修饰普通函数，**表明函数的作用范围**，仅在定义该函数的文件内才能使用。在多人开发项目时，为了防止与他人命令函数重名，可以将函数定位为 static。
3. 修饰成员变量，修饰成员变量使所有的对象**只保存一个该变量**，而且**不需要生成对象**就可以访问该成员。
4. 修饰成员函数，修饰成员函数使得**不需要生成对象**就可以访问该函数，但是在 static 函数内**不能访问非静态成员**。

## this指针
1. `this`指针是一个隐含于**每一个非静态成员函数**的特殊指针。它指向正在被该成员函数操作的那个对象。
2. 当对一个对象调用成员函数时，编译程序先将对象的地址赋给`this`指针，然后调用成员函数。每次成员函数存取数据成员时，都隐含使用`this`指针。
3. 当一个成员函数被调用时，自动向它传递一个隐含参数，该参数是一个指向该成员函数所在对象的指针。
4. `this`指针被隐含地声明为`ClassName *const this`，意味着不能给`this`指针赋值；在`const`成员函数中，`this`指针的类型为`const ClassName* const`，说明`this`所指对象是不可修改的（即不能对该对象的数据成员进行赋值操作）。
5. `this`不是常规变量，而是个**右值**，所以不能取`this`地址。
6. 在以下场景中，经常显示引用`this`指针：
    - 为实现对象的链式引用；
    - 为避免对同一对象进行赋值操作；
    - 在实现一些数据结构时，如list。
## inline内联函数
### 特征
- 相当于把内联函数里面的内容写在调用内联函数处；

- 相当于不用执行进入函数的步骤，直接执行函数体；
- 相当于宏，却比宏多了类型检查，真正具有函数特性；
- 不能包含循环、递归、switch 等复杂操作；
- 在类声明中定义的函数，**除了虚函数的其他函数**都会自动隐式地当成内联函数。
### 使用
```
// 声明1（加 inline，建议使用）
inline int functionName(int first, int secend,...);

// 声明2（不加 inline）
int functionName(int first, int secend,...);

// 定义
inline int functionName(int first, int secend,...) {/****/};

// 类内定义，隐式内联
class A {
    int doA() { return 0; }         // 隐式内联
}

// 类外定义，需要显式内联
class A {
    int doA();
}
inline int A::doA() { return 0; }   // 需要显式内联
```
### 编译器对inline函数的处理步骤
1. 将inline函数体复制到inline函数调用点处
2. 为所用inline函数中的局部变量分配内存空间
3. 将inline函数的入参和返回值映射到调用方法的局部变量空间中
4. 如果inline函数有多个返回点，将其转变为inline函数代码块末尾的分支（使用GOTO）

### 优缺点
#### 优点
1. inline函数如同宏函数一样将在被调用处进行**代码展开**，省去了**参数压栈**、**栈帧开辟与回收**、**结果返回**等，从而提高程序**运行速度**。
2. 相比宏函数来说，在代码展开时会进行**安全检查**或**自动类型转换**（同普通函数），而宏定义不会。
3. 在类中声明同时定义的成员函数，自动转换为内联函数，因此内联函数可以访问类的成员变量，宏定义不能。
4. 内联函数在运行时可调试，宏定义不能。
#### 缺点
1. **代码膨胀**。内联是以代码膨胀（复制）为代价，消除函数调用带来的开销。如果执行函数体内代码的时间，相比于函数调用的开销较大，则效率的收获会很少。另一方面，每一处内联函数的调用都要复制代码，将使程序总代码量增大，消耗更多内存空间。
2. inline函数无法随着函数库升级而升级。inline函数的改变需要重新编译，不像non-inline可以直接链接。
3. 是否内联，程序员不可控。inline只是对编译器的建议，是否对函数内联，决定权在于编译器。
#### 虚函数（virtual）可否内联
+ 虚函数可以内联，但是当虚函数表现多态性的时候不能内联。
+ 内联是在编译期建议compiler内联，而虚函数的多态性在运行期，编译器无法知道运行期调用哪个代码，因而表现多态性时（运行期）不可内联。
+ `inline virtual` 唯一可以内联的时候是：编译器知道所调用的对象是哪个类（如 `Base::who()`），这只有在编译器具有实际对象而不是对象的指针或引用时才会发生。
#### 虚函数内联使用
```
#include <iostream>  
using namespace std;
class Base
{
public:
    inline virtual void who()
    {
        cout << "I am Base\n";
    }
    virtual ~Base() {}
};
class Derived : public Base
{
public:
    inline void who()  // 不写inline时隐式内联
    {
        cout << "I am Derived\n";
    }
};

int main()
{
    // 此处的虚函数 who()，是通过类（Base）的具体对象（b）来调用的，编译期间就能确定了，
    // 所以它可以是内联的，但最终是否内联取决于编译器。 
    Base b;
    b.who();

    // 此处的虚函数是通过指针调用的，呈现多态性，需要在运行时期间才能确定，所以不能为内联。  
    Base *ptr = new Derived();
    ptr->who();

    // 因为Base有虚析构函数（virtual ~Base() {}），所以 delete 时，
    // 会先调用派生类（Derived）析构函数，再调用基类（Base）析构函数，防止内存泄漏。
    delete ptr;
    ptr = nullptr;

    system("pause");
    return 0;
} 
```
