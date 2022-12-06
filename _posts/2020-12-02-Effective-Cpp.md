---
title: Effective C++ - notes
author: lambdaxing
date: 2020-12-02 20:00:00 +0800
categories: [Notes, Cpp]
tags: [Cpp]
---

&emsp;&emsp;这是一份我在阅读《Effective C++》过程中记录的笔记，也仅仅只是笔记。其中大部分内容节选自原文，少部分内容由我自己叙述和添加。  
&emsp;&emsp;公开阅读笔记似乎是一种对原作的侵权行为，但看到很多人写过类似的笔记，我不太清楚这是否属于 “有法必依，执法必严”。如有侵权，请联系我删除。如有错误或不当之处，欢迎大家指正，谢谢！  

## 零. Introduction

&emsp;&emsp;在选择点上做出正确决定很重要，因为一个不良的决定有可能不至于很快带来影响，却在发展后期才显现恶果，那时候再来矫正往往即困难又耗时间，而且代价昂贵。  
&emsp;&emsp;软件设计和实现是复杂的差使，被硬件、操作系统、应用程序的约束条件涂上五颜六色，这儿仅提供指南，让你得以创造出更棒的程序。惟有了解条款背后的基本原理，你才能够决定是否将它套用于你所开发的软件，并奉行其所昭示的独特约束。  

## 一. Accustoming Yourself to C++

&emsp;&emsp;让自己习惯 C++ 。  
&emsp;&emsp;本章是一些最基础的东西。

### 01 view C++ as a federation of languages

&emsp;&emsp;视 C++ 为一个语言联邦。  
&emsp;&emsp;本条款将 c++ 主要的次语言分为了四个：C , Object-Oriented C++ , Template C++ , STL 。从某个次语言切换到另一个，高效编程守则需改变。对内置类型（C-like）类型而言 pass-by-value 通常比 pass-by-refrence 高效，从 C part of C++ 移往 Object-Oriented C++ , pass-by-reference-to-const 往往更好。运用 template 时尤为如此，因为此时甚至不知道所处理对象的类型。跨入 STL 时，迭代器和函数对象都是在C指针之上塑造出来的，旧式的 C pass-by-value 守则再次适用。  

### 02 Prefer consts,enums,and inlines to #defines

&emsp;&emsp;尽量以 const,enum,inline 替换 #define 。  
&emsp;&emsp;此条款主要讲了3点。  

+ “宁可以编译器替换预处理器”，`#define` 不被视为语言的一部分，用常量替代相关的宏（`#define`）：常量定义式通常被放在头文件（以便被不同的源码含入），因此有必要将指针（而不只是指针所指之物）声明为 `const` 。  
+ “enum hack”：

```c++
class GamePlayer {
    private:
    //  static const int NumTurns = 5;
        enum { NumTurns = 5 };      // "the enum hack"--令NumTurns成为5的一个记号名称
        int scores[NumTurns];       // it's ok.
    ...
};
```

&emsp;&emsp;enum hack 的行为像 `#define` 而不像 `const`：它不会导致非必要的内存分配，取一个 `enum` 的地址不合法。当编译器不允许 “static 整数型 class 常量” 完成 “in class 初值设定” 时，“the enum hack”是一个补偿做法：“一个属于枚举类型（enumerated type）的数值可权充 ints 被使用”，有时这可能正是你想要的。  

+ 以 template inline 函数替换形似函数的宏，既能获得宏带来的效率以及一般函数的所有可预料行为和类型安全性（type safety）。

### 03 Use const whenever possible

&emsp;&emsp;尽可能使用 const 。  
&emsp;&emsp;该条款主要讲了 `const` 如何用、怎么用以及何时用，大部分都十分了解了。“成员函数是 `const`” 讲述了两个流行概念：bitwise constness（又称 physical constness）和 logical constness。bitwise constness 说成员函数只有在不更改对象之任何成员变量（`static` 除外）时才可以说是 `const`，也就是说它不更改对象内任何一个bit。但是当只有指针隶属于对象（而非其所指之物），成员函数更改了“指针所指物”时不会引发编译器异议，这导致了反直观的结果。logical constness 主张 `const` 成员函数可以修改它所处理的对象内的某些 bits，但只有在客户端侦测不出来的情况下才得如此。但在 `const` 成员函数内部编译器坚持 bitwise constness，此时利用 C++ 的一个与 `const` 相关的摆动场：`mutable`：释放掉 non-static 成员变量的 bitwise constness 约束。编译器强制实施 bitwise constness，但编写程序时应该使用“概念上的常量性“（conceptual constness）”。  
&emsp;&emsp; 一个非常重要的C++特性：两个成员函数如果只是常量性（constness）不同，可以被重载。重载 `const` 成员函数时，实现函数（如 `operator[]`）的机能一次并使用它两次，即令其中一个调用另一个，好处大大的，这促使我们将常量性转除（casting away constness）。有时候 const 版本完全做掉了 non-const 版本该做的一切，唯一的不同是其返回类型多了一个 `const` 修饰。这种情况下将返回值的 `const` 转除是安全的，因为不论谁调用 non-const 函数，都首先有一个 non-const 对象，否则就不能够调用 non-const 函数。所以令 non-const 调用其 const 兄弟是一个避免代码重复的安全做法，即使过程中需要一个转型动作。下面是代码：

```c++
class TextBlock {
    public:
    ...
    const char& operator[] (std::size_t position) const // 一如既往
    {
        ...
        ...
        ...
        return text[position];
    } 
    char& operator[] (std::size_t position)
    {
        return 
            const_cast<char&>(          // 将 op[] 返回值的 const 转除
                static_cast<const TextBlock&>(*this) //为*this加上const
                [position]                           // 调用 const op[]
            );
    }
    ...
};
```

&emsp;&emsp;为了避免无穷递归，必须明确指出调用的是 `const operator[]`，将 `*this` 从其原始类型 `TextBlock&` 转型为 `const TextBlock&` 。反向做法：令 const 版本调用 non-const 版本很明显是一种错误行为，将 `*this` 身上的 const 性质解放是乌云罩顶的前兆。

### 04 Make sure that objects are initialized before thry're used

&emsp;&emsp;确定对象被使用前已先被初始化。  
&emsp;&emsp;C++ 对于初始化这件事反复无常，而读取未初始化的值会导致不明确的行为。C part of C++ 不保证发生初始化， non-C parts of C++ 却不一样，比如 array （来自 C part of C++，如 `int a[5]`）不保证其内容被初始化，而 `vector`、`array` （来自STL part of C++，如 `array<int, 5> a`）却有此保证。  
&emsp;&emsp;永远在对象使用之前呢将它初始化。内置类型需手工完成，而除内置类型以外，初始化的责任落在构造函数（constructors）身上：确保**每一个构造函数**都将对象的**每一个成员**初始化。C++ 规定，对象的成员变量的初始化动作发生在进入构造函数本体之前。因此，较佳的构造函数写法是使用所谓的 member initialization list（成员初始值列表）替换构造函数体内部的赋值动作。同样道理，当想要 default 构造一个成员变量，使用成员初始值列指定 nothing（`()`） 作为初始化实参即可。虽然编译器会为在“成员初始值列”中没有指定初值的成员变量自动调用 `default` 构造函数，但是在初始值列中列出所有成员变量是有用的，以免记住哪些成员变量可以无需初值。若内置类型被“成员初始值列”遗漏，则可能开启“不明确行为”的潘多拉盒子。特别注意：某些成员变量是 const 或 references ,它们就一定需要初值，不能被赋值。总之，**总是使用成员初始值列表**是最简单的做法。这样做有时候绝对必要，又往往比赋值更高效。  
&emsp;&emsp;条款介绍了为避免构造函数中重复无聊的工作，将“赋值表现像初始化一样好”的成员变量改用赋值操作，移往某个函数（通常是private）供所有构造函数调用。C++11 的委托构造函数应该可以实现同样的效果，且无需赋值操作吧。  
&emsp;&emsp;C++ 有着十分固定的“成员初始化次序”，按其成员声明次序初始化成员。因此，在成员初始值列表中列各个成员时应以其声明次序为序。  
&emsp;&emsp;“不同编译单元内定义之 non-local static 对象”的初始化次序。不同编译单元内的 non-local static 对象的初始化顺序无明确定义，我们应该通过将其变为函数内的 local static 对象来用，即以 “函数调用” （返回一个 reference 指向 local static 对象）替换 “直接访问 non-local static 对象”,这样就保证了获得的 reference 指向一个历经初始化的对象。

## 二. Constructors, Destructors, and Assignment Operators

&emsp;&emsp;构造/析构/赋值运算。  
&emsp;&emsp;把这些函数良好地集结在一起，形成 classes 的脊柱。  

### 05 Know what functions C++ silently writes and calls

&emsp;&emsp;了解 C++ 默默编写并调用哪些函数。  
&emsp;&emsp;当我们未声明一个 copy 构造函数、一个 copy assignment 操作符 和 一个析构函数以及任何构造函数时，编译器会**声明**相应的函数（`public` 且 `inline`）。惟有当这些函数被需要（调用）时，编译器才会将它们**创建**出来。这里面的规则远不止这一句话，《C++ Primer》讲了很多，只有当成员和基类相应的函数可见时，编译器才会为其生成合成的版本。注意：只有当无任何构造函数时，编译器才会生成合成的默认构造函数，可通过 `=default` 显式地使编译器生成合成的默认构造函数。  
&emsp;&emsp;很明显，class 内的 `const` 成员不支持赋值操作，编译器无法为其生成合成的赋值操作，必须自己定义 copy assignment 操作符号。“内含 reference 成员”的类也是如此，当赋值 reference 改变的是引用所引的对象还是引用所引的对象的值？众所周知，引用所引的对象绑定后就无法更改。

### 06 Explicitly disallow the use of compiler-generated functions you do not want

&emsp;&emsp;若不想使用编译器自动生成的函数，就该明确拒绝。  
&emsp;&emsp;C++11 引入了 `=delete`。当 class 不需要相关的拷贝控制函数时，需要拒绝编译器为其生成合成的版本。条款介绍了 C++11 之前的两个方法：  

+ 将成员函数声明为 `private` 而且故意不实现它们。
+ 使用像 `Uncopyable` 这样的 base class。在其中将 copying 操作声明为 `private`，基类继承其后便丢失了 base class 的 copying 操作，当编译器试着生成合成的操作时，找不到 base class 的对应操作，则这些调用会被拒绝。

### 07 Declare destructors virtual in polymorphic base classes

&emsp;&emsp;为多态基类声明 virtual 析构函数。  
&emsp;&emsp;欲实现出 `virtual` 函数，对象必须携带某些信息，主要用来在运行期决定哪一个 `virtual` 函数被调用。这份信息通常是由一个所谓 vptr （virtual table pointer）指针指出。vptr 指向一个由函数指针构成的数组，称为 vtbl （virtual table）；每一个带有 virtual 函数的 class 都有一个相应的 vtbl 。当对象调用某一个 virtual 函数，实际被调用的函数取决于该对象的 vptr 所指的那个 vtbl —— 编译器在其中寻找适当的函数指针。  
&emsp;&emsp;析构函数的运作方式是，最深层派生（most derived）的那个 class 其析构函数最先被调用，然后是其每一个 base class 的析构函数被调用。因此必须为 virtual destructor 提供一份定义（在 abstract class 中的pure virtual destructor 通常是空 `{ }`）。  
&emsp;&emsp;polymorphic（带多态性质的）base classes 应该声明一个 `virtual` 析构函数。如果 class 带有任何 virtual 函数，它就应该拥有一个 `virtual` 析构函数。Classes 的设计目的如果不是作为 base classes 使用，或不是为了具备多态性（polymorphically），就不该声明 `virtual` 析构函数。  
&emsp;&emsp;`final`可以实现“禁止派生”机制。STL 没有多态，没有虚函数，并且没有使用 `final` 来禁止继承，并不是所有继承都是为了多态。[知乎上有一个相关的问题](https://www.zhihu.com/question/430866456)。

### 08 Prevent exceptions from leaving destructors

&emsp;&emsp;别让异常逃离析构函数。  
&emsp;&emsp;析构函数绝对不要吐出异常。如果一个被析构函数调用的函数可能抛出异常，析构函数应该捕捉任何异常，然后吞下它们（不传播）或结束程序。  
&emsp;&emsp;如果客户需要对某个操作函数运行期间抛出的异常做出反应，那么 class 应该提供一个普通函数（而非在析构函数中）执行该操作。还有一个方案，提供普通函数给予客户一个机会去处理可能发生的异常，客户未处理时 class 才处理。此时异常带来的“过早结束程序”或“发生不明确行为”的风险，客户已然知晓。

```c++
class DBConn{
public:
    ...
    void close()
    {
        db.close();         // 供客户使用的函数
        closed = true;
    }
    ~DBconn()
    {
        if(!closed){
            try{
                db.close();      // 关闭连接（如果客户未那么做的话）
            }
            catch(...) {
                ...              // 关闭动作失败，记录下来并结束程序或吞下异常
            }
        }
    }
private:
    DBConnection db;
    bool closed;
};
```

&emsp;&emsp;把调用 `close` 的责任从 `DBConn` 析构函数手上移到 `DBConn` 客户手上（但 `DBConn` 析构函数仍内含一个“双保险”调用）。

### 09 Never call virtual functions during construction or destruction

&emsp;&emsp;绝不在构造和析构过程中调用 virtual 函数。  
&emsp;&emsp;不该在构造函数和析构函数期间调用 virtual 函数。base class 构造函数的执行早于 derived class 构造函数，当 base class 构造函数执行时，derived class 的成员变量尚未初始化。如果此期间调用的 virtual 函数下降至 derived classes 阶层，derived class 的函数几乎必然取用 local 成员变量（未初始化），C++禁止这种错误发生。在 derived class 对象的 base class 构造期间，对象的类型是 base class 而不是 derived class。不只 virtual 函数会被编译器解析（resolve to）base class，若使用运行期类型信息（runtime type information，例如 `dynamic_cast`（见条款27）和 `typeid`），也会把对象视为 base class 类型。  
&emsp;&emsp;相同道理也适用析构函数。一旦 derived class 析构函数开始执行，对象内的 derived class 成员变量便呈现未定义值，所以 C++ 使它们仿佛不存在。进入 base class 析构函数后对象就成为一个 base class 对象，而 C++ 的任何部分包括 virtual 函数、dynamic_cast 等等也就那么看待它。  
&emsp;&emsp;注意：确定构造函数和析构函数都没有（在对象被创建和销毁期间）调用 virtual 函数，**而它们调用的所有函数也都服从同一约束**（在构造析构中，通过非 virtual 函数调用 virtual 函数。这一做法通常不会引发任何编译器和连接器的抱怨，但可能会造成事与愿违的程序结果。）

&emsp;&emsp;无法使用 virtual 函数从 base class 向下调用，在构造函数期间可以通过 “令 derived classes 将必要的构造信息向上传递至 base class 构造函数” 加以弥补。通过层层的构造函数向上传参至 base class，中途可使用 derived classes 的 static 函数作为辅助函数。`static` 函数不可能意外指向“初期未成熟的 derived classes 对象内尚未初始化的成员变量”。因此，不同于使用 virtual 函数，这是可行的。

### 10 Have assignment operators return a reference to *this

&emsp;&emsp;令 operato= 返回一个 reference to *this 。  
&emsp;&emsp;为了实现“连锁赋值”，赋值操作符必须返回一个 reference 指向操作符的左侧实参。这是为 classes 实现赋值操作符时应该遵循的协议。该协议不仅适用于标准赋值形式（`=`），也适用于所有赋值相关运算（`+=`，`-=`，`*=`，...）

```c++
    int x, y, z;
    x = y = z = 15;
```

### 11 Handle assignment to self in operator=

&emsp;&emsp;在 operator= 中处理 “自我赋值” 。  
&emsp;&emsp;C++ 存在很多“别名”（aliasing）,带来了很多并不明显的自我赋值。导致当我们尝试自行管理资源时，可能会掉进 “在停止使用资源之前意外释放了它” 的陷阱。在 `operator=` 中处理“自我赋值”，条款给出了三个方案。

```c++
class Bitmap{ ... };
class Widget{
    ...
private:
    Bitmap* pb;
};
```

```c++
Widget& Widget::operator=(const Widget& rhs)
{
    delete pb;
    pb = new Bitmap(*rhs.pb);
    return *this;
}
//一份不安全（自赋值与异常安全性均不具备）的 operator= 实现版本
```

```c++
Widget& Widge::operator=(const Widget& rhs)
{
    if(this == &rhs) return *this;      // 证同测试（identity test）
                                        // 如果是自我赋值，不做任何事
    delete pb;
    pb = new Bitmap(*rhs.pb);
    return *this;
}
// 这一版本检验了“自我赋值”，但存在异常安全性，如果 “new Bitmap” 导致异常（分配时内存不足或Bitmap的copy构造函数抛出异常），Widget最终会持有一个指针指向一块被删除的Bitmap。
```

```c++
Widget& Widget::operator=(const Widget& rhs)
{
    Bitmap* pOrig= pb;              // 记住原先的 pb
    pb = new Bitmap(*rhs.pb);       // 令 pb 指向 rhs.pb 的一个副本；这儿发生异常时，pb 仍指向原来的 Bitmap
    delete pOrig;                   // 删除原先的 pb
    return *this;
}
```

```c++
class Widget{
...
    void swap(Widget& rhs);     // 交换 *this 和 rhs 的数据;详见条款 29
...
}

Widget& Widget::operator=(Widget rhs)
{// rhs 是被传对象的一份副本（pass by value）
    swap(rhs);      // 将 *this 的数据和副本的数据互换
    return *this;
}
// copy and swap 技术。将“copying动作”从函数本体内移至“函数参数构造阶段”可令编译器有时生成更高效的代码。
```

&emsp;&emsp;确定任何函数如果操作一个以上的对象，而其中多个对象是同一个对象时，其行为仍然正确。

### 12 Copy all parts of an object

&emsp;&emsp;复制对象时勿忘其每一个成分。  
&emsp;&emsp;编写 copying 函数（copy constructor or copy assignment），确保（1）复制所有 local 成员变量，（2）让 derived classes 的 copying 函数调用所有 base classes 内适当的 copying 函数。  
&emsp;&emsp;不要尝试以某个 copying 函数实现另一个 copying 函数。应该将共同机能放进第三个函数中，并由两个 copying 函数共同调用。

## 三. Resource Management

&emsp;&emsp;资源管理。  
&emsp;&emsp;无论哪一种资源，当你不使用它时，必须将它还给系统。  

### 13 Use objects to manage resources

&emsp;&emsp;以对象管理资源。  
&emsp;&emsp;当需要管理资源时，无论哪一种资源（动态内存也好，数据库连接也罢，文件描述器、互斥锁、网络sockets等等），使用对象来管理它们是十分有好处的。我们可以依赖 C++ 的 “析构函数自动调用机制” 确保资源被释放，这避免了手动释放资源的步骤被异常或程序错误等干扰无法执行而导致的资源泄露问题，当对象被销毁（例如离开作用域时），其析构函数自然会被自动调用使得资源被释放掉。“以对象管理资源”也称为 RAII （Resource Acquisition Is Initialization） —— “资源取得时机便是初始化时机”，几乎总是在获得一笔资源后于同一语句内以它初始化某个管理对象。  
&emsp;&emsp;书中谈到 `shared_ptr` 在其析构函数内部做 `delete` 而不是 `delete []` 动作，意味着它并不适合用来管理动态分配的数组，不过使用容器代替动态分配的数组就没问题了。

### 14 Think carefully about copying behavior in resource-managing classes

&emsp;&emsp;在资源管理中小心 copying 行为。  
&emsp;&emsp;当一个 RAII 对象被复制时，会发生什么事？ 条款给出了几种可能和它们所适用的场合。

+ 禁止复制。有时候复制动作对 RAII class 并不合理，应该禁止 copying 操作。例如 [`unique_ptr`](https://jiangcaixing.github.io/posts/Cpp-smart-pointer/#unique_ptr)，但它可以使用 move 操作。
+ 对底层资源使用 “引用计数法”（reference-count）。例如 [`shared_ptr`](https://jiangcaixing.github.io/posts/Cpp-smart-pointer/#shared_ptr)，我们还可以指定删除器（deleter） —— 一个函数或函数对象（function object）。
+ 复制底部资源。此情况下复制资源管理对象，同时复制其所包含的资源 —— “深度拷贝”。例如字符串。
+ 转移底部资源的拥有权。例如 `auto_ptr`。

&emsp;&emsp;**资源的 copying 行为决定 RAII 对象的 copying 行为。**

### 15 Provide access to raw resources in resource-managing classes

&emsp;&emsp;在资源管理类中提供对原始资源的访问。  
&emsp;&emsp;RAII 的世界并不完美，许多 APIs 直接涉指资源，因此我们得提供方法绕过资源管理对象直接访问原始资源。条款提到了两个方法：

+ 显式转换。提供某个接口，像是 `shared_ptr` 的 `get()`。比较安全，不过当我们需要非常多的这种转换时，这就有点倒人胃口了。
+ 隐式转换。提供 `operator type()` 隐式转换函数。这可能增加错误发生的机会。
  
&emsp;&emsp;选择哪种方法，主要取决于 RAII class 被设计执行的特定工作以及它被使用的情况。直接访问原始资源并不与 “封装” 矛盾，RAII classes 并不是为了封装某物而存在 —— 为了确保一个特殊行为（资源释放）会发生。`shared_ptr` 封装了其引用计数机制，但还是让外界访问其所内含的原始指针。**多数设计良好的 classes，隐藏了客户不需要看的部分，但备妥客户需要的所有的东西** 。

### 16 Use the same form in corresponding uses of new and delete

&emsp;&emsp;成对使用 new 和 delete 时要采取相同的形式。  
&emsp;&emsp;数组所用的内存通常还包括“数组大小”的记录，以便 delete 知道调用多少次析构函数，单一对象却没有这笔记录。很显然，使用 `delete` 释放数组内存或使用 `delete[]` 释放单一对象是相当有害。特别注意，有时候我们使用了 `typedef` 声明了一个新的数组类型（它看起来可能并不像它原来的样子），我们使用 `new` 创建这种类型的对象，必须使用对应的 `delete[]` 释放它。最好尽量不要对数组形式做 typedefs 动作。

### 17 Store newed objects in smart pointers in standalone statements

&emsp;&emsp;以独立语句将 newed 对象置入智能指针。  
&emsp;&emsp;我们经常写下面这样的代码：

```c++
    int priority();
    void processWidget(std::shared_ptr<Widget> pw, int priority);
    ...
    processWidget(new Widget, priority());  // 这无法通过编译，shared_ptr的构造函数是个 explicit 函数。下面这样可以通过编译：
    processWidget(std::shared_ptr<Widget>(new Widget), priority());
```

&emsp;&emsp;C++ 编译器以什么样的次序完成 processWidget() 调用中的这些操作？弹性很大。`new Widget` 肯定先于 `shared_ptr`，但 `priority()` 的调用却不确定了。当对 `priority()` 的调用发生异常，而此时编译器只执行了 `new Widget`，资源泄露发生了，`new Widget` 返回的指针遗失了。**在 “资源被创建” 和 “资源被转换为资源管理对象” 两个时间点之间有可能发生异常干扰**。使用分离语句，编译器对于“跨越语句的各项操作”没有重新排列的自由（只有在语句内它才拥有那个自由度）。

## 四. Designs and Declarations

&emsp;&emsp;设计与声明。  
&emsp;&emsp;本章对良好 C++ 接口的设计和声明发起攻势。  

### 18 Make interfaces easy to use correctly and hard to use incorrectly

&emsp;&emsp;让接口容易被正确使用，不易被误用。  
&emsp;&emsp;欲开发一个“容易被正确使用，不容易被误用”的接口，首先必须考虑客户可能做出什么样的错误。

+ 对于接口的一些特别参数，明智而审慎地导入新类型对预防“接口被误用”有神奇疗效。正确的类型限制类型上的操作，束缚对象值，从而更好地预防客户错误。“除非有好理由，否则应该尽量令你的 types 的行为与内置 types 一致”，“一致性” 更能导致 “接口容易被被正确使用”，“不一致性” 加剧接口的恶化。不一致性对开发人员造成的心理和精神上的摩擦与争执，没有任何一个IDE可以完全抹除。
+ 任何接口如果要求客户必须记得做某些事情，就是有着“不正确使用”的倾向，因为客户可能会忘记做那件事。例如，返回资源的接口要求客户自己释放资源，哪怕客户在拿到资源的第一时间就将其托付给资源管理对象，这也是一个并不佳的接口设计。较佳接口的设计原则是先发制人，消除客户的资源管理责任，交予客户资源管理对象，而不是资源本身。
+ `shared_ptr` 如此容易消除某些客户错误，值得我们核计其使用成本。`shared_ptr` 是原始指针（raw pointer）的两倍大，以动态分配内存作为薄记用途和“删除器之专属数据”，以 virtual 形式调用删除器，并在多线程程序修改引用次数时蒙受线程同步化（thread synchronization）的额外开销。

### 19 Treat class design as type design

&emsp;&emsp;设计 class 犹如设计 type 。  
&emsp;&emsp;定义一个新 class，也就定义了一个新 type。应该带着和“语言设计者当初设计语言内置类型时”一样的谨慎来研讨 class 的设计。

+ 新 type 的对象应该如何被创建和销毁？
+ 对象的初始化和对象的赋值该有什么样的差别？ 别混淆了“初始化”和“赋值”。
+ 新 type 的对象如果被 passed by value，意味着什么？ passed by value ——> copy constructor
+ 什么是新 type 的“合法值”？这决定了 class 必须维护的约束条件（invariants），也就是大多数函数所要进行的错误检查工作和抛出的异常以及函数异常明细列等。
+ 你的新 type 需要配合某个继承图系（inheritance graph）吗？新 class 作为 base class 会影响到 derived class，尤其是其析构函数；作为 derived class 会受到 base classes 设计的束缚，特别是它们那些 virtual 和 non-virtual 函数。
+ 你的新类型需要什么样的类型转换？ 隐式 —— 类型转换操作符（type conversion operators）或 non-explicit-one-argument-constructor （可被单一实参调用的构造函数）；显式 —— 提供某个接口专门负责执行转换。
+ 什么样的操作符和函数对此新 type 而言是合理的？哪些操作符？哪些函数？member or non-member？等等...
+ 什么样的标准函数应该驳回？我的理解是哪些操作应该是被禁止的或应该提供某种保护机制。
+ 谁该取用新 type 的成员？涉及接口的权限 —— 访问控制符、友元声明等。
+ 什么是新 type 的“未声明接口”（undeclared interface）？
+ 你的新 type 有多么一般化？ 你是否需要的是定义一整个 types 家族，而非一个新 type。class template 或许更合适。
+ 你真的需要一个新 type 吗？还是 derived class 、 non-member 函数 、template，更能达到目标。

&emsp;&emsp;Class 的设计就是 type 的设计，设计像内置类型一样好的用户自定义（user-defined） classes。

### 20 Prefer pass-by-reference-to-const to pass-by-value

&emsp;&emsp;宁以 pass-by-reference-to-const 替换 pass-by-value 。  
&emsp;&emsp;pass by reference 的效率很高，没有任何构造函数和析构函数被调用，因为没有任何新对象被创建。const 向读者和客户保证传递的参数不会做任何改变，同时扩大了传入参数的覆盖面，常量、非常量都能匹配。by reference 也可以避免 slicing（对象切割）问题 —— derived class 对象以 by value 方式传递并被视为一个 base class 对象，base class 的 copy 构造函数被调用，derived class 的特化性质被切割了，留下一个 base class 对象。by reference-to-const 是解决切割问题的好办法。  
&emsp;&emsp;C++ 编译器的底层往往以指针实现 references，对于内置类型，pass-by-value 有时是更好的选择，STL的迭代器和函数对象也同样如此，习惯上它们都被设计为 pass-by-value。迭代器和函数对象的实践者有责任看看它们是否高效且不受切割问题的影响。  
&emsp;&emsp;编译器对待“内置类型”和“用户自定义类型”的态度截然不同，纵使两者拥有相同的底层描述（underlying representation）。编译器可能拒绝将小型对象放进缓存器，却会将指针（references 的实现体）放进缓冲器。用户自定义类型的大小容易有所改变，在不同时间或空间都可能大不相同。

### 21 Don't try to return a reference when you must return an object

&emsp;&emsp;必须返回对象时，别妄想返回其 reference 。  
&emsp;&emsp;函数创建新对象的途径有二：在 stack 空间或在 heap 空间创建之。定义一个 local 变量就是在 stack 创建对象。很明显，返回一个 reference 指向 stack 中的 local 变量是通往“无定义行为”的路途，返回指针也是如此。Heap-based 对象由 `new` 创建，返回一个 reference 指向 heap 中的对象好像没有什么问题，但却潜藏了巨大的危害。除了丢给客户 `delete` 的职责以外，还埋下了内存泄露的隐患，如果函数返回的 reference 以临时对象的身份参与到其他运算或函数中，reference 背后隐藏的 heap 对象在没有释放的情况下便丢失了。返回 pointer 或 reference 指向一个 local static 对象，要记住所有的操作都发生在这一个对象身上，条款4也有相关说明和实例。  
&emsp;&emsp;有时候我们需要承受函数返回值（或传入值）的构造成本和析构成本，长远来看那只是为了获得正确行为而付出的一个小小代价。像是 `operator*`、`operator+` ... 等操作，某些情况下返回值的构造和析构可被编译器安全地消除，这样你的程序即保有原有的行为，执行也会快于预期。

### 22 Declare data memebers private

&emsp;&emsp;将成员变量声明为 private 。  
&emsp;&emsp;成员变量应该是 `private`：

+ 语法一致性，即客户访问数据的一致性。客户唯一能够访问对象的办法就是通过成员函数（带括号 `()`），而不是直接访问它（带点 `.`）。
+ 通过成员函数实现可细微划分访问控制，允诺约束条件获得保证。
+ 将成员变量隐藏在函数接口的背后，可以为“所有可能的实现”提供弹性。被广泛使用的 classes 是最需要封装的一个族群，因为它们最能够从“改采用一个较佳实现版本”中获益。
+ `public` 成员变量完全没有封装性。`protected` 成员变量同样如此（derived class 往往也是个不可知的大量）。一旦将一个成员变量声明为 `public` 或 `protected` 而客户开始使用它，就很难改变那个成员变量所涉及的一切。大多代码需要重写、重新测试、重新编写文档、重新编译。从封装角度看，其实只有两种访问权限：`private`（提供封装） 和其他（不提供封装）。

### 23 Prefer non-member non-friend functions to member functions

&emsp;&emsp;宁以 non-member、no-friend 替换 member 函数。  
&emsp;&emsp;面向对象守则要求，数据以及操作数据的那些函数应该被捆绑在一块，这意味着 member 函数是较好的选择。但是面向对象守则同样要求数据应该尽可能被封装，member 函数（可以访问 `private` 数据、函数、`enums`、`typedef`s 等等） 比 non-member 函数（它无法访问上述任何东西）的封装性低。对于 friend 和 non-friend 同样如此。当两者提供相同的机能，选用更大封装性的版本更好。C++ 比较自然的做法是以 non-member 函数实现机能并且位于 class 所在的同一个 namespace 内。namespace 可以跨越多个源码文件而 classes 不能，当一个 class 需要多个类别的 non-member 函数作为便利函数时，将各类别相关的便利函数声明于各自的头文件内但隶属于同一个命名空间，客户也可以轻松扩展这一组便利函数。class 的 member 函数无法提供这一点，因为 class 的定义式对客户而言是不能扩展的。  
&emsp;&emsp;这正是 C++ 标准程序库的组织方式。标准程序库并不是拥有单一、整体、庞大的 `<C++StandardLibrary>` 头文件并在其中内含 `std` 命名空间内的每一样东西，而是有非常多的头文件，每个头文件声明 std 的某些机能。这使得客户只对他们所用的那一小部分系统形成编译相依（见条款31）。一个 class 必须整体定义，以此种方式切割机能并不适用于 class 成员函数。

### 24 Declare non-member functions when type conversions should apply to all parameters

&emsp;&emsp;若所有参数皆需要类型转换，请为此采用 non-member 函数。  
&emsp;&emsp;如果你需要为某个函数的所有参数（包括被 `this` 指针所指的那个隐喻参数）进行类型转换，那么这个函数必须是个 non-member。只有当参数被列于参数列（parameter list）内，这个参数才是隐式类型转换的合格参与者。地位相当于“被调用成员函数所隶属的那个对象” —— 即 `this` 对象的那个隐喻参数绝不是隐式转换的合格参与者。  
&emsp;&emsp;像 `operator*` 这样的函数是否应该成为 class 的一个 friend 函数呢？有时候这些函数的机能完全可以由 class 的 `public` 接口完成任务。member 的反面是 non-member，而不是 friend 函数。无论何时可以避免 friend 函数就该避免，因为就像真实世界一样，朋友带来的麻烦往往多过其价值。条款46对于 class template 有更多的讨论。

### 25 Consider support for a non-throwing swap

&emsp;&emsp;考虑写出一个不抛异常的 swap 函数。  
&emsp;&emsp;`swap` 原本只是 STL 的一部分，后来成为异常安全性编程（exception-safe programming，见条款29）的脊柱，以及用来处理自我赋值可能性（见条款11）的一个常见机制。缺省情况下的标准程序库的 `swap` 算法：

```c++
namespace std {
    template<typename T>
    void swap(T& a, T& b)
    {
        T temp(a);
        a = b;
        b = temp;
    }
}
```

&emsp;&emsp;缺省版本有时候对 class 或 class template 提供可接受的效率。若 class 或 template 使用了某种 pimpl （“以指针指向对象，内含真正数据” —— “pointer to implementation”）手法，则 `swap` 缺省实现版本往往效率不足。以下是 `swap` 的实现方案和理由：

+ 提供一个 `public` `swap` 成员函数，高效地置换你的类型的两个对象值。这个函数绝不该抛出异常！ 因为它的一个最好应用就是为 classes（和 class templates）提供强烈的异常安全性保障（条款29详述）。`swap` 缺省版以 copy constructor 和 copy assignment 为基础，一般情况下两者都允许抛出异常。高效率的 swaps 几乎总是基于对内置类型的操作（例如 pimple 手法的底层指针），而内置类型上的操作绝不会抛出异常。
+ 在 class 或 template 所在命名空间内提供一个 non-member swap, 并令它调用上述 `swap` 成员函数。
+ 为 class （而非 class template）全特化（total template specilize） `std::swap`，令它调用 class 的 `swap` 成员函数。C++ 允许我们为标准 templates（如 swap）制造特化版本，使它专属于我们自己的 classes。但是对于 class template，就变成了我们企图偏特化（partially specialize）一个 function template （`std::swap`），这是 C++ 不允许的（在 function templates 身上偏特化），C++ 只允许对 class templates 偏特化。那 templates 的 `swap` 怎么办呢？上一步提供的 non-member swap 解决了这个问题。让我们来看看 C++ 的名称查找法则（name lookup rules）遇到一个 `swap` 是如何处理的吧。首先找到 global 作用域或 `T` （class 或 template）所在之命名空间内的任何 `T` 专属的 `swap`。如果没有 `T` 专属之 `swap` 存在，编译器就使用 `std`内的 `swap`（这得感谢 `using` 声明式让 `std::swap` 在函数内曝光），即便如此编译器还是比较喜欢 `std::swap` 的 `T` 专属特化版，而非缺省的那个template。

&emsp;&emsp;调用 `swap`，请确定包含一个 `using` 声明式，同时不加任何 `namespace` 修饰符，赤裸裸地调用 `swap`。  
&emsp;&emsp;STL 容器也都提供有 `public` `swap` 成员函数和 `std::swap` 特化版本（用以调用前者）。为 class 进行 std templates 全特化是好的，千万不要尝试在 std 内加入某些对 `std` 而言全新的东西（例如，以在 `std` 中重载 class templates 的 `std::swap` 作为不能偏特化 `std::swap` 的替代方案）。

## 五. Implementations

&emsp;&emsp;声明谈完了，接下来是实现。  

### 26 Postpone variable definitions as long as possible

&emsp;&emsp;尽可能延后变量定义式的出现时间。  
&emsp;&emsp;第一个原因是，当程序控制流（control flow）到达变量定义式时，承受构造成本，当这个变量离开作用域时，承受析构成本。由于各种原因（例如，异常），定义的变量可能并未被使用，仍需耗费这些成本，应尽可能避免这种情况。第二个原因是，无初值定义变量时会调用 default 构造函数，大多数情况下变量会再被赋予有意义的值，尝试延后变量定义直到能够给它初值为止，这样避免了无意义的 default 构造行为与成本。同时，以“具明显意义之初值”将变量初始化，附带说明了变量的目的。  
&emsp;&emsp;（A)在进入循环前定义变量，循环里使用变量。或者（B）在每轮循环开始处定义变量，循环中使用变量。哪种方法更可取呢？条款给出的方案是除非赋值成本比“构造+析构”成本低（赋值操作往往包含析构+构造），或者你正在处理代码中效率高度敏感的部分，否则就应该使用 B —— 在循环内解决循环内的问题，A 造成的变量名称的作用域更大，对程序可理解性和易维护性造成冲突。

### 27 Minimize casting

&emsp;&emsp;尽量少做转型动作。  
&emsp;&emsp;C++ 提供四种新式转型（new-style 或 C++-style casts）：

```c++
    const_cast<T>( expression )
    // 将对象的常量性转除 —— 唯一有此能力的 C++-style 转型操作符
    dynamic_cast<T>( expression )
    // “安全向下转型” —— 可能耗费重大运行成本
    reinterpret_cast<T>( expression )
    // 执行低级转型 —— 取决于编译器，不可移植
    static_cast<T>( expression )
    // 强迫隐式转换 —— 将 non-const 转为 const，将 const 转为 non-const 只有 const_cast 才能办到
```

&emsp;&emsp;条款中说到，类似 `T(expression)` 这样的“对象生成”动作也是“转型”动作，这通常发生在将一个新对象传递给一个函数时。当 `T` 有一个接受 `expression` 的 `explicit` 构造函数时，隐式使用了函数风格的转型动作。  
&emsp;&emsp;转型并不只是告诉编译器把某种类型视为另一种类型。任何一个类型转换（无论是通过转型操作而进行的显式转换，或通过编译器完成的隐式转换）往往真的令编译器编译出运行期间执行的码。  
&emsp;&emsp;单一对象（例如一个 derived class 对象）可能拥有一个以上的地址（例如，“以 base class pointer 指向 derived class”时的地址和“以 derived class pointer 指向它”时的地址）。当将 derived class 的地址赋给 base class pointer 时，有个偏移量（offset）在运行期被施行于 derived class pointer 身上，用于取得正确的 base class pointer 指针值。这在多重继承和单一继承都会发生，意味着应该避免做出“对象在 C++ 中如何布局”的假设，更不该以此假设为基础执行任何转型动作。  
&emsp;&emsp;尽可能隔离转型动作，通常是把它隐藏在某个函数内，函数的接口会保护调用者不受函数内部任何肮脏龌龊的动作影响。条款还介绍了 `dynamic_cast` 以及它的替代方案，我对此不太了解，外加书上结合实例介绍，因此未做记录。

### 28 Avoid returning "handles" to object internals

&emsp;&emsp;避免返回 handles 指向对象内部成分。  
&emsp;&emsp;如果 const 成员函数传出一个 reference，该 reference 所指数据与对象自身有关联，而它又被存储于对象之外，这个函数的调用者可以修改那笔数据，这正是 bitwise constness （const 成员函数未改变任何一个 bit，却传出了 handles，给出了改变内部 bit 的权利）的一个附带结果，因此这样的设计可以通过编译。所以，**成员变量的封装性最多只等于“返回其 reference ”的函数的访问级别**。对于指针和迭代器同样如此，它们被称为 handles（号码牌，用来取得某个对象）。返回一个“代表对象内部数据”的 handles，随之而来的便是“降低对象封装性”的风险。不被公开的成员函数（`private` 和 `protected`）也是对象“内部”的一部分，因此也应该留心不要返回它们的 handles（函数指针和函数对象）。一个成员函数返回一个指向“访问级别较低”的成员函数的指针，后者的访问级别也就提高如同前者。另一个理由是，返回“代表对象内部”的 handles，可能导致 dangling handles （空悬的号码牌）：这种 handles 所指东西（的所属对象）不复存在。这种“不复存在的对象”最常见的来源就是函数返回值（在函数返回的临时对象身上调用其返回“代表对象内部”的handles 的函数）。因此，**函数返回一个 handle 代表对象内部成分总是危险的行为，一个 handle 被传出去了，也就暴露在 “handle 比其所指对象更长寿” 的风险下。你没法保证对象不复存在时 handle 不复存在或不会被使用。**让成员函数返回 handle（例如 `operator[]`）是例外，不是常态。

### 29 Strive for exception-safe code

&emsp;&emsp;为异常安全而努力是值得的。  
&emsp;&emsp;当异常被抛出时，带有异常安全性的函数会：

+ 不泄露任何资源。  —— 资源管理对象。
+ 不允许数据败坏。  —— 合理的语句次序。不要为了表示某件事情发生而改变对象状态，除非那件事情真的发生了。

&emsp;&emsp;异常安全函数（Exception-safe functions）提供以下三个保证之一：

+ 基本承诺。—— 如果异常被抛出，程序内的任何事物仍然保持在有效状态下。注意：程序有可能处于任何状态下，只要那是个合法状态。
+ 强烈保证。—— 如果异常被抛出，程序状态不改变。如果函数成功就是完全成功，如果函数失败，程序会回复到“调用函数之前”的状态。
+ 不抛掷（nothrow）保证。—— 承诺绝不抛出异常。**作用于内置类型身上的所有操作都提供 nothrow 保证。这是异常安全码中一个必不可少的关键基础材料。** 注意：`noexcept` 并不是说函数绝不会抛出异常，而是说如果函数抛出异常将是严重错误，会有*意想不到的函数*被调用。函数的声明式（包括其异常明细——如果有的话）并不能够告诉你是否它是正确的、可移植的或高效的，也不能告诉你它是否提供任何异常安全性保证。所有那些性质都由函数的实现决定，无关乎声明。

&emsp;&emsp;异常安全码必须提供上述三种保证之一。该为我们所写的每一个函数提供哪一种保证？我们很难在 C part of C++ 领域中完全没有调用任何一个可能抛出异常的函数。任何使用动态内存的东西（例如所有STL容器）如果无法找到足够内存以满足需求，通常便会抛出一个 `bad_alloc` 异常。有可能的话，请提供 nothrow 保证。但对大部分函数而言，抉择往往落在基本保证和强烈保证之间。  
&emsp;&emsp;有个一般化的设计策略很典型地会导致强烈保证，很值得熟悉它 —— copy and swap 。为你打算修改的对象（原件）做出一份副本，然后在那副本身上做一切必要修改。若有任何修改动作抛出异常，原对象仍保持未改变状态。待所有改变都成功后，再将修改过的那个副本和原对象在一个不抛出异常的操作中置换（swap）。实现上通常将所有“隶属对象的数据”从原对象放进另一个对象内，然后赋予原对象一个指针，指向那个所谓的实现对象（implementation object，即副本）。注意：copy-and-swap 必须为每一个即将改动的对象做出一个副本，这也就意味着“强烈保证”并非在任何时刻都显得实际。当“强烈保证”不切实际时，必须提供“基本保证”。当你曾经付出适当的心力试图提供强烈保证，万一实际不可行，使你退而求其次地只提供基本保证，任何人都不该因此责难你。对许多函数而言，“异常安全之基本保证”是一个绝对通情达理的选择。另一理由是，如果一个函数调用另一个函数，后者未提供任何异常安全保证，那前者自身也不可能提供任何保证。函数提供的“异常安全保证”通常最高只等于其所调用之各个函数的“异常安全保证”中的最弱者。这也就意味着，一个软件系统要不就具备异常安全性，要不就全然否定，没有所谓的“局部异常安全系统”。调用一个（惟有一个）不具备异常安全性的函数有可能导致资源泄露或数据结构败坏，那整个系统就不具备异常安全性。  
&emsp;&emsp;许多老旧的 C++ 代码并不具备异常安全性，但没有理由让这种情况永垂不朽。当撰写新码或修改旧码时，请仔细想想如何让它具备异常安全性。首先是“以对象管理资源”以阻止资源泄露。然后挑选三个“异常安全保证”中的某一个实施于你所写的每一个函数身上。应该挑选“现实可实施”条件下的最强烈等级，只有当你的函数调用了传统代码才别无选择地将它设为“无任何保证”。函数的“异常安全性保证”是其可见接口的一部分，应该慎重选择，就像选择函数接口的其他任何部分一样，为函数的用户和将来的维护者着想，将你的决定写进文档。

### 30 Understand the ins and outs of inlining

&emsp;&emsp;彻底了解 inlining 的里里外外。  
&emsp;&emsp;inline 函数，看起来像函数，动作像函数，比宏好得多，调用它们无需蒙受函数调用的所招致的额外开销。编译器最优化机制通常被设计用来浓缩那些 “不含函数调用” 的代码，这意味着当 inline 某个函数，编译器有能力对它（函数本体）执行语境相关最优化。但是，inline 将函数的每一个调用都以函数本体替换之，过度热衷 inlining 会增加你的目标码（object code）大小，同时造成程序体积太大（对可用空间而言），导致额外的*换页行为（paging）*，降低指令高速缓存装置的击中率（instruction cache hit rate），以及伴随这些而来的效率损失。当然，若 inline 函数的本体很小，编译器针对“函数本体”所产出的码可能比针对“函数调用”所产出的码更小，这将导致较小的目标码和较高的指令高速缓存装置击中率。  
&emsp;&emsp;inline 只是对编译器的一个申请，不是强制命令。将函数定义于 class 定义式内隐喻地向编译器提出 inline 函数的申请，`friend` 函数也可能被定义于 class 内（也是被隐喻声明为 inline）。明确声明 inline 函数的做法是在其定义式前加上关键字 `inline` （例如标准的 `max template`）。inline 函数通常一定被置于头文件内是因为大多数建置环境（build environments）在编译过程中进行 inlining，而为了将一个“函数调用”替换为“被调用函数的本体”，编译器必须知道那个函数长什么样子。Inlining 在大多数C++程序中是编译器行为，templates 通常也被置于头文件内基于同样的理由。同时，template 的具现化与 inlining 无关，没有理由必须将 function template 声明为 inline，除非它真的适合 inlining。  
&emsp;&emsp;一个表面看似 inline 的函数是否真是 inline，取决于你的建置环境，主要取决于编译器。大多数编译器如果无法将你要求的函数 inline 化，会给你一个警告信息。如果程序要取某个 inline 函数的地址，编译器通常必须为此生成一个 outlined 函数本体，同样地，编译器通常不对“通过函数指针而进行的函数调用”实施 inlining，这意味着inline 函数的调用是否被 inlined 取决于该调用的实施方式。注意，程序员并非唯一要求函数指针的人，编译器有时候会生成构造函数和析构函数的 outline 副本，它们获得指针指向那些函数，在 array 内部元素的构造和析构过程中使用。构造函数和析构函数往往是 inlining 的糟糕候选人。在构造和析构函数中隐含了许多我们未曾看见和编写但确实发生的行为（成员变量的构造和析构、基类构造函数的调用等等），“事情如何发生”是编译器实现者的权责，但它们肯定不可能凭空发生，在程序内肯定有某些代码让它们发生，而那些代码——由编译器于编译期间代为产生并安插到程序中——肯定存在于某个地方，有时候就放在构造和析构函数内。成员变量和 base class 两者的构造函数的调用（它们自身可能被 inlined）会影响编译器是否对此当前函数 inlining。显而易见，将构造和析构函数 inline 化不是个轻松的决定。  
&emsp;&emsp;inline 函数无法随着程序库的升级而升级，一旦 inline 函数被改变，所有用到它的客户端程序都必须重新编译。non-inline 函数有任何修改，客户端只需要重新连接就好（程序库还可采取动态连接）。还有一个事实：大部分调试器面对 inline 函数都束手无策（如何在一个并不存在的函数内设立断点（break point）？）。  
&emsp;&emsp;将大多数 inlining 限制在小型、被频繁调用的函数身上。这可使日后的调试过程和二进制升级（binary upgradability）更容易，也可使潜在的代码膨胀问题最小化，使程序的速度提升机会最大化。**牢记 80-20 经验法则：平均而言一个程序往往将 80% 的执行时间花费在 20% 的代码上。这是一个重要法则，因为它提醒你，作为一个软件开发者，找出这可能有效增进程序整体效率的 20% 代码，然后将 它 inline 或竭尽所能地将它“瘦身”**。

### 31 Minimize compilation dependencies between files

&emsp;&emsp;将文件间的编译依存关系降至最低。  
&emsp;&emsp;当编译器看到一个定义式时，同时也必须知道该定义式所涉及变量的类型的定义式，以便分配足够空间放置该变量。这也是为什么我们 `#include` 我们需要的标准库头文件。对于我们自己定义的头文件，如此一来便是在定义文件（`---.h`）和其含入文件（`#include "---.h`）之间形成了一种编译依存关系（compilation dependency）。当这些头文件中有任何一个被改变，或这些头文件所依赖的其他头文件有任何改变，那么每一个含入文件（的含入文件）就得重新编译。这样的连串编译依存关系会对许多项目造成难以形容的灾难。  
&emsp;&emsp;C++ 并没有把“接口和实现分离”这事做得很好，Class 的定义式叙述了 class 的接口，也包括十足的实现细节（例如，在 class 定义式中的 private 的 object 成员变量就是实现细节）。使用 pimpl idiom （pimpl 是 “pointer to implementation” 的缩写）设计，可以实现真正的“接口与实现分离”，class 的任何实现修改无需客户端重新编译，客户也无法看到 class 的实现细目。

&emsp;&emsp;让头文件尽可能自我满足，相依于声明式而不是定义式：

+ 使用 object references 或 object pointers ，而不是 objects 。仅靠声明式就能定义出指向该类型的 references 和 pointers 。
+ 尽量以 class 声明式替换 class 定义式。注意：声明函数用到 class 时，并不需要该 class 定义式（函数以 by value 方式传递该类型参数（或返回值）亦然）。但是，调用函数时，class 的定义式一定得先曝光。这就使得，将“提供 class 定义式”（通过 `#include` 完成）的义务从函数声明所在的头文件转移到内含函数调用的客户文件。
+ 为声明式和定义式提供不同的头文件。程序库客户应该总是 `#include` 一个声明文件而非前置声明若干函数，程序库作者应该提供这两个头文件。

&emsp;&emsp;制作 Handle class 的另一方法：abstract base class （抽象基类），也称为 Interface class —— 描述 derived classes 的接口，通常不带成员变量，也没有构造函数，只有一个virtual 析构函数以及一组 pure virtual 函数，用来叙述整个接口。 class 的客户以抽象基类的 pointers 和 references 撰写应用程序时，就像 handle class 客户一样，除非 Interface class 的接口被修改否则其客户不需重新编译。这一点具体如何实现见书上。  
&emsp;&emsp;程序库头文件应该以“完全仅有声明式”（full and declaration-only forms）的形式存在。这种做法无论是否设计 templates 都适用。

## 六. Inheritance and Object-Oriented Design

&emsp;&emsp;继承与面向对象设计。  

### 32 Make sure public inheritance models "is-a"

&emsp;&emsp;确定你的 public 继承塑模出 is-a 关系。  
&emsp;&emsp;以 c++ 进行面向对象编程，最重要的一个规则是：**public inheritance（公开继承）意味 “is-a”（是一种）的关系。适用于 base classes 身上的每一事情一定也适用于 derived classes 身上，因为每一个 derived class 对象也都是一个 base class 对象。**  
&emsp;&emsp;有时候我们设计出的继承体系符合我们对真实世界的直觉，却并非是严格意义上的“is-a”，例如，Penguin（企鹅）是一种 Bird（鸟），Bird 可以 fly，但 Penguin 却不会。这时候怎么办？如果我们的系统对 fly 一无所知，未来也不会对其“有所知”，那区分会飞的鸟和不会飞的鸟就显得多余了。但如果我们需要 fly，除了在继承体系中区分开会飞的鸟和不会飞的鸟外，还有其他的处理方法：1）令 Penguin 拥有 fly，在实施 fly 时告知 fly 是一个错误。2）直接忽略掉 Penguin 的 fly。我们应该采取编译器做出反应的设计（1），而不是运行期才能侦测的设计（2）。另一个例子是 Rectangle（矩形）和 Square（正方形），数学课说正方形是一种矩形，但是对矩形的很多操作不可施行于正方形身上。相反的例子是 Person 和 Student，两者的关系符合真实世界也符合我们的“is-a”。

### 33 Avoid hiding inherited names

&emsp;&emsp;避免遮掩继承而来的名称。  
&emsp;&emsp;public 继承意味着“is-a”，即 base classes 的名称也应属于其 derived classes，但事实却并非这样。derived classes 内的名称会遮掩 base classes 内的名称。这是因为，derived class 作用域被嵌套在 base class 作用域内。在 C++ 中，名称查找先于参数匹配，当编译器遇到一个名称时，先对其进行查找（从当前作用域到最外层作用域），当在某一层作用域找到它时，便开始对其进行参数匹配，若不匹配则报告错误，就算此时外层作用域有个参数匹配的相同名称，编译器也不会去找它了，看起来就相当于外层作用域名称被内层作用域名称遮掩了。  
&emsp;&emsp;使用 `using` 声明式让被遮掩的名称重见天日，它令继承而来的某给定名称之所有同名函数在 derived class 中都可见。若要只令某个特定的函数版本可见，使用转交函数（forwarding functions）在其内部通过作用域符（`Base::fname(xxx)`）调用那个特定的版本。

### 34 Differentiate between inheritance of interface and inheritance of implementation

&emsp;&emsp;区分接口继承和实现继承。  
&emsp;&emsp;之前看《 C++ primer 》介绍继承时，一个 derived class 从其 base classes 继承而来各种各样的的函数，pure virtual（纯虚函数），impure virtual（非纯的虚函数），non-virtual（非虚函数）。这一条款讲述了这些继承的区分：

+ pure virtual 函数必须被任何“继承了它们”的具象 class 重新声明，在抽象 class 内通常没有定义。声明一个 pure virtual 函数的目的是为了让 derived classes 只继承函数接口。若 pure virtual 函数在 抽象 class 内提供了定义，则其声明部分表现的是接口，其定义部分类似 impure virtual 表现出缺省行为，与其不同的是需要 derived classes 自己明确提出申请，即通过作用域符 `Base::fname(xxx)` 调用。
+ 声明 impure virtual 函数的目的，是让 derived classes 继承该函数的接口和缺省实现。derived classes 可能覆写（override）它。
+ 声明 non-virtual 函数的目的是为了令 derived classes 继承函数的接口即一份强制性实现。一个 non-virtual 成员函数所表现的不变形（invariant）凌驾其特异性（specialization），无论 derived class 变得多么特异化，它的行为都不可以改变，所以它绝不该在 derived class 中被重新定义。
  
&emsp;&emsp;在 public 继承下，derived class 总是继承 base class 的接口。不同类型的声明意味根本意义并不相同的事情，当声明成员函数时，必须谨慎选择 pure virtual函数（接口继承）、simple（impure）virtual 函数（接口继承及缺省实现继承）、non-virtual函数（接口继承以及强制性实现继承）。有些函数就是不该在 derived class 中被重新定义，当不变性（invariant）凌驾特异性（specialization）时，别害怕说出来。

### 35 Consider alternatives to virtual functions

&emsp;&emsp;考虑 virtual 函数以外的其他选择。  
&emsp;&emsp;当你为解决问题而寻找某个设计方法时，不妨考虑 virtual 函数的替代方案：

+ 使用 non-virtual interface （NVI）手法，Template Method 设计模式的一种特殊形式 —— 以 public non-virtual 成员函数（derived class 会接口继承以及强制性实现继承）包裹较低访问性（private 或 protected）的 virtual 函数（derived class 会继承接口和缺省实现版本）。在 public non-virtual 成员函数内我们还可以做一些除调用 virtual 函数之外的其他事情，例如设定好适当场景，做一些事前事后工作等。
+ 将 virtual 函数替换为 “函数指针成员变量” —— Strategy 设计模式的一种分解表现形式。同一类型的不同对象的函数指针成员变量可不相同，相当于可各自拥有自己的函数，同时一个对象的函数成员变量指针可以改变，相当于在运行期变更函数。不过，非成员函数无法访问 class 的 non-public 成员，这也可能导致 class 被迫对这些 class 外部函数降低其封装性或为其实现提供 public 访问函数。
+ 以 function（函数对象） 成员变量替换 virtual 函数 —— 允许使用任何可调用物（callable entity）搭配一个兼容于需求的签名式。
+ 将继承体系内的 virtual 函数替换为另一个继承体系内的 virtual 函数 —— Strategy 设计模式的传统实现手法。前者的对象都含一个指针（类型是指向后者中基类的指针），指向一个来自后者的对象（类型为 derived classes）。只需在后者继承体系中加入一个 derived class，就提供“将一个既有的健康算法纳入使用”的可能性。

&emsp;&emsp;这个世界还有许多其他道路，值得我们花时间加以研究。

### 36 Never redefine an inherited non-virtual function

&emsp;&emsp;绝不重新定义继承而来的 non-virtual 函数。  
&emsp;&emsp;条款给出了两个理由：1） non-virtual 函数都是静态绑定的，当使用 pointer-to-base-class 调用 non-virtual 函数，永远是 base-class 所定义的版本被调用（pointer 指向 derived-class 时同样如此）。使用指针（`B*`）调用继承体系的 non-virtual 函数，无论指针指向哪个 class（`D`），调用的函数永远会是指针声明时的 class（`B`） 所定义的版本，reference 同样如此。2）public 继承意味着 “is-a” 的关系，non-virtual 表明 D（derived-class） 必须继承 B（base-class） 的接口和实现，重新定义 non-virtual 函数表明 D 有必要实现出与 B 不同的码，很显然这是矛盾的。同时，non-virtual 函数意味着“不变性凌驾特异性”，重新定义 non-virtual 函数打破了这一准则，既然如此，virtual 函数才是更好的选择。

### 37 Never redefine a function's inherited default parameter value

&emsp;&emsp;绝不重新定义继承而来的缺省参数值。  
&emsp;&emsp;non-virtual 函数不该被重新定义，所以只讨论 virtual 函数，即继承一个带有缺省参数值的 virtual 函数时，绝不重新定义继承而来的缺省参数值。  
&emsp;&emsp;理由很简单：virtual 函数是动态绑定（dynamically bound），而缺省参数值却是静态绑定（statically bound）。当（使用指向 D 对象的 B* 或 B&）调用一个定义于 D 内的 virtual 函数时，却会使用 B 为它所指定的缺省参数值。  
&emsp;&emsp;如何处理继承而来的缺省参数值呢？忽略掉它？那不就是重新定义缺省参数值嘛。重要的是，当我们不愿意继承 virtual 函数给我们的缺省实现时，我们才要覆写它，覆写时总是需要处理这个缺省参数值。有一个办法：赋予其重复（与 B 一样的）的缺省参数值。但是，这样不仅造成了代码重复还带着相依性（with dependencied）:如果 base class 的缺省参数值改变了，所有“重复给定缺省参数值”的 derived class 也必须改变，否则就会导致 “重复定义继承而来的缺省参数值” 。**当你想令 virtual 函数表现出你所想要的行为但却遭遇麻烦，聪明的做法是考虑替代设计（条款35）。** 使用 NVI 手法，在 non-virtual 函数中指定缺省参数值，其内部的 private virtual 函数负责真正的工作，derived class 继承 non-virtual 函数的接口和实现，由于 non-virtual 函数应该绝对不被 derived class 覆写，这个设计很清楚地使得 non-virtual 的函数参数的缺省参数值总是为 base class 所指定的那个。

### 38 Model "has-a" or "is-implemented-in-terms-of" through composition

&emsp;&emsp;通过复合塑模出 has-a 或 “根据某物实现出”。  
&emsp;&emsp;复合（composition）是类型之间的一种关系 —— 某种类型的对象内含它种类型的对象。在应用域（application domain），复合意味 has-a（有一个），例如，`Person` has-a `name`(`String`) and `address`（`Address`）。在实现域（implementation domain），复合意味 is-implemented-in-terms-of（根据某物实现出），例如，根据线性表实现队列和栈，队列和栈与线性表并不是 “is-a” 关系，虽然前者可以通过后者继承实现，但 public 继承不适合用来塑模它们，它们更像是 “has-a” 关系：在队列和栈的实现内定义一个线性表（成员）表述数据。复合的意义和 public 继承完全不同。书上举的例子是：根据 list 实现 set，两者关系并非 is-a ，而是 is-implemented-in-terms-of。  
&emsp;&emsp;在程序员之间，复合（composition）这个术语有许多同义词：layering（分层）、containment（内含）、aggregation（聚合）和 embedding（内嵌）。

### 39 Use private inheritance judiciously

&emsp;&emsp;明智而审慎地使用 private 继承。  
&emsp;&emsp;当以 `public` 形式继承时，编译器在必要时刻（为了让函数调用成功）将 derived class 暗自转换为 base class。`private` 继承关系则不会使编译器这样做。除此之外，由 private base class 继承而来的所有成员，在 derived class 中都会变成 `private` 属性，纵使它们在 base class 中原本是 `protected` 或 `public` 属性。  
&emsp;&emsp;private 继承并不意味 is-a 关系，它意味 implemented-in-terms-of（根据某物实现出）。让 class D 以 private 形式继承 class B，用意是为了采用 class B 内已经备妥的某些特性，不是因为 B 对象和 D 对象存在有任何观念上的关系。private 继承纯粹只是一种实现技术（B 的每样东西在 D 内部都是 private：因为它们都只是实现枝节而已）。用条款 34 提出的术语，private 继承意味只有实现部分被继承，接口部分应略去。Private 继承在软件“设计”层面上没有意义，其意义只及于软件实现层面。  
&emsp;&emsp;条款 38 指出复合（composition）的意义也是这样，如何在两者之间取舍呢？答案很简单：尽可能使用复合，必要时才使用 private 继承。必要时刻：当 protected 成员和/或 virtual 函数牵扯进来时。这种情况其实也可以以复合取而代之，在 D 内声明一个嵌套式 private class DB，令 DB 以 public 形式继承 B（原本 D 需要 private 继承它），并重新定义 virtual 函数，然后放一个 DB 类型的对象于 D 内。这个设计同时涉及 public 继承和复合，并导入一个新 class DB。这告诉我们，解决一个设计问题的方法不止一种，而训练自己思考多种做法是值得的（看看条款35）。  
&emsp;&emsp;private 继承主要用于上面提到的 “当一个 class（要成为 derived class 者）想访问一个 class （要成为base class 者）的 protected 成分，或为了重新定义 virtual 函数”，这时候两个 classes 之间的概念关系其实是 is-implemented-in-terms-of（根据某物实现出）而非 is-a。所谓的 empty classes —— 不使用任何空间，没有 non-static 成员变量，没有 virtual 函数（这种函数的存在会为每个对象带来一个 vptr,见条款7），也没有 virtual base class（招致体积上的额外开销，见条款40），没有任何隶属对象的数据需要存储。C++ 裁定凡是独立（非附属）对象都必须有非零大小，通常 C++ 官方勒令默默安装一个 char 到空对象内，然后齐位需求（alignment，见条款50）可能造成编译器为 empty classes 加上一些衬垫（padding）。因此，当复合一个 empty class 时，它是独立（非附属）对象，将会有非零大小。注意，上面说的是“独立（非附属）”对象的大小一定不为零，也就是说，这个约束不适用与 derived class 对象内的 base class 成分，因为它们并非独立（非附属）。继承一个 empty class，而不是内含一个 empty class 对象 —— 所谓的 EBO（empty base optimization：空白基类最优化）。如果你是一个程序库开发人员，而你的客户非常在意空间，那么值得注意 EBO，另外 EBO 一般只在单一继承（而非多重继承）下才可行。  
&emsp;&emsp;总结一下，复合和 private 都意味 is-implemented-in-terms-of，但复合比较容易理解。当面对“并不存在 is-a 关系”的两个 classes，其中一个需要访问另一个的 protected 成员，或需要重新定义其一或多个 virtual 函数，private 继承极有可能成为正统设计。即便如此，一个混合了 public 继承和复合的设计也能表达出想要的行为。明智而审慎地使用 private 继承。

### 40 Use multiple inheritance judiciously

&emsp;&emsp;明智而审慎地使用多重继承。  
&emsp;&emsp;在多重继承中，程序有可能从一个以上的 base classes 继承相同的名称（如函数、typedef等等），这导致较多的歧义机会。注意，哪怕某个名称在不同的 base class 中的属性（public、private）不同，derived class 继承之后仍可能产生歧义。这是因为 C++ 解析（resolving）重载函数的规则：先在重载函数中寻找最佳匹配版本，之后才检验其可取用性。当在寻找最佳匹配的过程中，发现没有最佳的匹配（没有谁优于谁）时，就会产生二义性，无论重载函数中的某些函数是否可取用（例如，继承而来的函数在 base class 中是 private）。  
&emsp;&emsp;在多重继承中，从派生类到某个直接基类，再到某个间接基类，直到某个根基类，错综复杂。在继承树中，从派生类到某个间接基类之间不止一条路径是十分可能的，如何处理从多条路径继承而来的同一个基类的同名数据呢？C++ 的缺省做法是执行复制 —— 派生类保留多条路径继承而来的基类的重复数据，采用 “virtual 继承” 能够避免继承得来的成员变量重复，但 “virtual 继承” 是有代价的：体积更大，速度更慢，并且 virtual base 的初始化责任是由继承体系中最低层（most derived）class 负责，它必须承担其 virtual base （不论直接或间接）的初始化责任。两个忠告：非必要请使用 non-virtual 继承；尽可能避免在 virtual base class 中放置数据，这样就无需担心它们身上的初始化和赋值带来的诡异事情了。以上的 virtual base 指的是以 virtual 继承的 base class，而不是所谓的 abstract base class。  
&emsp;&emsp;多重继承一个通情达理的应用 —— public 继承某个 Interface class（public 继承接口）和 private 继承某个协助实现的 class （private 继承实现） 相结合。  
&emsp;&emsp;尽可能使用单一继承，多重继承有时候确实是完成任务最简洁、最易维护、最合理的做法，果真如此就别害怕使用它。  

## 七. Templates and Generic Programming

&emsp;&emsp;模板与泛型编程。  
&emsp;&emsp;C++ template 机制自身是一部完整的图灵机（Turing-complete）：它可以被用来计算任何可计算的值。于是导出了模板元编程（template metaprogramming），创造出 “在 C++ 编译器内执行并于编译完成时停止执行” 的程序。  

### 41 Understand implicit interfaces and compile-time polymorphism

&emsp;&emsp;了解隐式接口和编译器多态。  
&emsp;&emsp;classes 和 template 都支持接口（interfaces）和多态（polymorphism）。对 classes 而言接口是显式的（explicit），以函数签名为中心，在源码中明确可见，多态则是通过 virtual 函数发生于运行期（**哪一个 virtual 函数该被绑定**）。  
&emsp;&emsp;对 template 而言（在 template 中，显式接口和运行期多态仍然存在，但重要性较低），接口是隐式的（implicit），它不基于函数签名式，而是由有效表达式（valid expression）组成，表达式自身可能看起来很复杂，但它们要求的约束条件一般而言相当直接明确。例如，前面条款提到的 `std::swap`，其表达式通过 `=` 交换两个 `T` 类型的对象，还使用了 `T` 的 copy 构造函数。template 参数身上的隐式接口和 class 对象身上的显式接口都是在编译期完成检查，无法在 template 中使用 “不支持 template 所要求之隐式接口” 的对象（代码将通不过编译）。  
&emsp;&emsp;多态则是通过 template 具现化和函数重载解析（function overloading resolution）发生于编译期 —— 以不同的 template 参数具现化 function template 会导致调用不同的函数（**哪一个重载函数该被调用**）。  

### 42 Understand the two meanings of typename

&emsp;&emsp;了解 `typename` 的双重意义。  
&emsp;&emsp;声明 template 参数时，前缀关键字 `class` 和 `template` 的意义完全相同，可互换。  
&emsp;&emsp;从属名称（dependent names） —— template 内出现的名称相依于某个 template 参数。  
&emsp;&emsp;嵌套从属名称（nested dependent name） —— 从属名称在 clss 内呈嵌套状。  
&emsp;&emsp;嵌套从属类型名称（nested dependent type name） —— 嵌套从属名称指涉某类型（例如：`std::iterator_traits<IterT>::value_type temp`，`IterT` 是 template 参数，`temp` 是个嵌套从属类型名称）。  
&emsp;&emsp;非从属名称（non-dependent names） —— 不依赖任何 template 参数的名称。  
&emsp;&emsp;C++ 解析器在 template 中遭遇一个嵌套从属名称，它便假设这名称不是个类型，除非你告诉它 —— 紧邻名称之前放置关键字 `typename` ，例如：`typename std::iterator_traits<IterT>::value_type temp`。不过，这一规则有个例外：`typename` 不可以出现在 base classes list（基类列）和 member initialization list（成员初始值列）的嵌套从属类型名称之前。  
&emsp;&emsp;注意，在 template 内建立 嵌套从属类型名称的 `typedef` 时，需要并列 “`typedef typename`”，例如：`typedef typename std::iterator_traits<IterT>::value_type value_type;`。

### 43 Know how to access names in templatized base classes

&emsp;&emsp;学习处理模板化基类内的名称。  
&emsp;&emsp;C++ 知道 base class templates 有可能被特化，而那个特化版本可能不提供和一般性 template 相同的接口。因此，在 derived class template 中，C++ 往往拒绝寻找在 templatized base classes（模板化基类）继承而来的名称。就某种意义而言，当我们从 Object Oriented C++ 跨进了 Template C++，继承就不像以前那般畅行无阻了，毕竟 template 具现化以后才会成为 class。有三个办法可令 C++ 进入 templatized base classes 寻找名称：

+ 在 base class 函数调用动作之前加上 `this->` ： `this->sendClear(info)；` 。。通过阅读下一条款发现这儿还需补充一下：这里 `this->` 声明告知编译器一层一层往上查找，直至找到或找完（告知编译器进入 base class 作用域查找），但如果在 derived class 中也有个名称 sendClear，将会调用 derived class 中的 sendClear，执行其参数匹配（哪怕并不匹配），不会到 base class 中继续寻找。
+ 使用 using 声明式，见条款 33 。这里的情况不是 base class 名词被 derived class 遮掩，而是编译器不进入 base class 作用域内查找，于是我们通过 using 告诉它：`using MsgSender<Company>::sendClear;` （这里 Company 是 template 参数）。这更类似于继承该名称。通过阅读下一条款发现这儿还需补充一下：如果在 derived class 中也有名称 sendClear，则类似函数重载与 override 。
+ 明白指出被调用的函数位于 base class 内：`MsgSender<Company>::sendClear(info)；` 。如果被调用的是 virtual 函数，这会关闭 virtual 绑定行为。

&emsp;&emsp;上诉做法都对编译器承诺 base class template 的任何特化版本都支持其一般化（泛化）版本所提供的接口（这儿是 `sendClear(info)`）。如果这个承诺最终未被实践出来，之后解析时编译器还是会还给事实一个公道。  
&emsp;&emsp;编译器解析 derived class template 的定义式时对 base classes 的内容毫无所悉，除非你通过上面三个方法告诉了它。当 templates 被特定 template 实参具现化时，若你先前告诉编译器的承诺并未被保证（你告诉它有，但后来它发现没有，可能仅是某个特化版本没有，刚好又调用到这个特化版本），编译器依然会诊断出问题，而不会被你骗过去。  

### 44 Factor parameter-independent code out of templates

&emsp;&emsp;将与参数无关的代码抽离 templates 。  
&emsp;&emsp;Template 是节省时间和避免代码重复的一个奇方妙法，class template 的成员函数只有在被使用时才被暗中具现化。但是，如果你不小心，使用 template 可能会导致代码膨胀（code bloat）：其二进制码带着重复（或几乎重复）的代码、数据，或两者。其源码看起来合身而整齐，但目标码却不是那么回事。  
&emsp;&emsp;共性与变性分析（commonality and variablibity analysis）。编写 classes 和 functions 时，我们会分析不同 class 之间和不同function 之间共同的部分（共性）以及变化的部分（变性），可能是实现，也可能是数据。对于 function ，使用第三方函数处理共性，对于 class ,使用继承或复合来处理。切换到 template 中，重复变成隐晦的了，毕竟只存在一份 template 源码。所以你必须训练自己去感受 template 被具现化多次时可能发生的重复。  
&emsp;&emsp;template 生成多个 classes 和多个 functions，所以任何 template 代码不该与某个造成膨胀的 template 参数产生相依福安息。对于非类型模板参数（non-type template parameters），以函数参数或 class 成员变量替换 template 参数。对于类型参数（type parameters），让带有完全相同二进制表述（binary representations）的具现类型（instantiation types）共享实现码（例如，`T*` 共享 `void*` ）。  
&emsp;&emsp;如果看到这想不起来该条款具体讲了什么，回去看书吧。  

### 45 Use member function templates to accept "all compatible types"

&emsp;&emsp;运用成员函数模板接受所有兼容类型。  
&emsp;&emsp;真实指针支持隐式转换（implicit conversions）。Derived class 指针可以隐式转换为 base class 指针，“指向 non-const 对象” 的指针可以转换为 “指向 const 对象”（即，指向 const 对象的指针可以指向 non-const 对象）...... 等等。智能指针这样的 template 是如何做到这些的呢？成员函数模板可以做到：

```c++
template<class T>
class shared_ptr {
public:
    // copy 构造函数
    shared_ptr(shared_ptr const& r);

    // 构造，来自任何兼容的
    // 内置指针
    template<class Y>
        explicit shared_ptr(Y* p);

    // 或 shared_ptr （泛化 copy 构造函数）  
    template<class Y>
        shared_ptr(shared_ptr<Y> const& r)  // 以 r 的 heldPtr
        : heldPtr(r.get()) { ... }         // 初始化 this 的 heldPtr

    // 或 weak_ptr
    template<class Y>
        explicit shared_ptr(weak_ptr<Y> const& r);

    // 或 auto_ptr
    template<class Y>
        explicit shared_ptr(auto_ptr<Y>& r);

    // copy assignment
    shared_ptr& operator=(shared_ptr const& r);

    // 赋值，来自任何兼容的        
    // shared_ptr   （泛化 copy assignment）
    template<class Y>            
        shared_ptr& operator=(shared_ptr<Y> const& r);
    
    // 或 auto_ptr
    template<class Y>
        shared_ptr& operator=(auto_ptr<Y>& r);

    T* get() const { return heldPtr; }

    ...

private:
    T* heldPtr;
};
```

&emsp;&emsp;上面的实现部分仅作为说明用途添加。在成员初始值列中，以类型为 `Y*` 的指针（由 `shared_ptr<Y>` 持有）作为初值，初始化 `shared_ptr<T>` 内类型为 `T*` 的 `heldPtr`，这个行为只有当 “存在某个隐式转换可将一个 `Y*` 转为一个 `T*` 指针” 时才能通过编译，而这正是我们想要的。这个泛化的构造函数只在其所获得的实参隶属适当（兼容）类型时才通过编译（原始指针支持隐式转换）。再者，泛化 copy 构造函数不是 `explicit`，意味着从某个 `shared_ptr` 类型隐式转换至另一个 `shared_ptr` 类型是被允许的（像原始指针一样）。  
&emsp;&emsp;注意上面，仍声明了正常的 copy 构造函数和 copy assignment 操作符。member template 用于“泛化 copy 构造”或“泛化 assignment 操作”，并不会阻止编译器生成它们自己的版本，如果你想控制方方面面的行为，仍需声明正常的 copy 构造函数和 copy assignment 操作符。

### 46 Define non-member functions inside templates when type conversions are desired

&emsp;&emsp;需要类型转换时请为模板定义非成员函数。准确的说，是 “**定义**于class template 内部的 friend 函数”。  
&emsp;&emsp;条款 24 讨论了为什么惟有 non-member 函数才有能力 “在所有实参身上实施隐式类型转换”，并以 Rational class 的 `operator*` 为例。在条款 24 内，编译器明确知道需要调用的哪个函数（接受两个 Rationals 参数的那个 `operator*`），同时它也能够在函数调用过程中将隐式类型转换（有些通过构造函数发生）考虑在内。这也是这种函数有能力在实参身上实施隐式类型转换的原因。对于 template 来说，情况不同了，首先编译器不知道调用哪个函数，而它试图具现化一个函数用作调用，在此之前必须先进行 template 实参推导。在 template 实参推导过程中从不将隐式类型转换纳入考虑，即必须明能确推导出 template 参数类型，不能通过隐式类型转换推导，template 参数推导和函数调用是两码事，前者发生时连函数都还没有。那如何为 template class 的（模板）函数实现 “在所有实参身上实施隐式类型转换” 的能力？下面有一份实例：

```c++
template<typename T> class Rational;    // 声明 Rational template
template<typename T>                    // 声明 helper template
const Rational<T> doMultiply(const Rational<T>& lhs, 
                             const Rational<T>& rhs);
template<typename T>
class Rational {
public:

    ...

friend
const Rational<T> operator*(const Rational<T>& lhs, 
                            const Rational<T>& rhs)
    {
        return doMultiply(lhs, rhs);    // 令 friend 调用 helper
    }

    ...
};

// 若有必要，在头文件内定义 helper template
template<typename T>
const Rational<T> doMultiply(const Rational<T>& lhs, 
                             const Rational<T>& rhs)
{
    return Rational<T>(lhs.numerator() * rhs.numerator(),
                       lhs.denominator() * rhs.denominator());
}
```

&emsp;&emsp;template class 内的 `friend` 声明式可以指涉某个特定函数。class `Rational<T>` 声明 `oprator*` 是它的一个 `friend` 函数。class template 并不倚赖 template 实参推导（后者只施行于 function templates 身上），所以编译器总是能够在 class `Rational<T>` 具现化得知 `T`。`friend` 函数 `operator*` 作为过程的一部分，当具现化一个 `Rational<T>`时，总是被自动声明出来，此时它是一个函数而非函数模板，因此编译器可在调用它时使用隐式转换函数（例如 `Rational` 的 non-explicit 构造函数）。注意，此时的 `operator*` 的定义式也同声明式一起提供，这是因为如果只在内部提供声明式而在外部提供定义式，那不就是内部有一个 `operator*` 函数声明，外部有一个 `operator*` 函数模板吗，内部的 `operator*` 函数声明实际并没有提供定义式，连接器将找不到它的定义式。因此，`operator*` 函数本体合并到了其声明式内。`friend` 的传统用法是“访问 class 的 non-public 部分”，这儿是为了让类型转换可能发生于所有实参身上，我们需要声明一个 non-member 函数，为了令这个函数被自动具现化，我们需要将它声明在 class 内部，而在 class 内部声明 non-member 函数的唯一办法就是：令它成为一个 `friend`。注意，定义于 class 内的函数都暗自成为 `inline` 函数，包括像 `operator*` 这样的 `friend` 函数，为使 `inline` 声明所带来的冲击最小化，令 `operator*` 不做任何事情，只调用一个定义于 class 外部的辅助函数（虽然这儿 `operator*` 是个单行函数，但这种做法值得研究）。作为一个 template，`doMultiply` 当然不支持混合式乘法，但它其实也不需要，它只被 `operator*` 调用，而 `operator*` 支持了类型转换所需的任何东西，确保两个对象能够相乘，然后 `doMultiply` 根据这两个对象（可能的隐式转换后的确定类型对象）具现化适当的 `doMultiply`，具现体再完成两个对象实际的乘法操作。  
&emsp;&emsp;总结下来就是，编写 template class 时，将 “与此 template 相关且支持所有参数都能隐式转换” 的函数（例如，operator* 这一类运算符）**定义**为 “class template 内部的 friend 函数”。

### 47 Use traits classes for information about types

&emsp;&emsp;请使用 traits classes 表现类型信息。  
&emsp;&emsp;看完条款后，我的感觉是，traits classes 像是一种封装，它将类型信息（往往是 template 类型参数 `T` 相关的）包装为 traits classes 供 template 使用，template 无需在意 template 具现化时给定的实际类型 `T` 是什么，它们（`T`）都有 traits classes 这样的接口提供类型信息。当然，也可以在 template 的参数类型（`T`）中加入一个类型标识成员达到同样的效果（类型内的嵌套信息），但是对于内置类型，无法在其中添加类似的标识，而 traits classes 这样的 template 能够为内置类型提供特化版本。  
&emsp;&emsp;Traits 并不是 C++ 关键字或一个预先定义好的构件：它们是一种技术，也是一个 C++ 程序员共同遵守的协议，它对内置（built-in）类型和用户自定义（user-defined）类型的表现必须一样好。这种技术，让我们能在编译期间取得某些类型信息，它们以 templates 和 “templates 特化” 完成实现，下面是书上举例的 `iterator_traits`：

```c++
// 对于 5 种类型的迭代器，c++ 标准库分别提供专属的卷标结构（tag struct）
struct input_iterator_tag {};
struct output_iterator_tag {};
struct forward_iterator_tag: public input_iterator_tag {};
struct bidirectional_iterator_tag: public forward_iterator_tag {};
struct random_access_itetator_tag: public bidirectional_iterator_tag {};

template <...>     // 略而未写 template 参数
class deque{
public:
    class iterator{
    public:
        typedef random_access_iterator_tag iterator_category;
        ...
    };
    ...
};

template <...>
class list {
public:
    class iterator{
    public:
        typedef bidirectional_iterator_tag iterator_category;
        ...
    };
    ...
};

// 类型 IterT 的 iterator_category 其实就是用来表现“IterT 说它自己是什么”
// iterator_traits 只是鹦鹉学舌般地响应 iterator class 的嵌套式 typedef
template<typename IterT>
struct iterator_traits{
    typedef typename IterT::iterator_category iterator_category;
    ...
}

// 为支持指针迭代器，针对指针类型提供一个偏特化版本
template<typename IterT>
struct iterator_traits<IterT*>
{
    typedef random_access_itetator_tag iterator_category;
    ...
}

//-----上面是设计和实现--------
//-----下面是使用-------------

// 这份实现用于 random access 迭代器
template<typename IterT, typename DistT>
void doAdvance(IterT& iter, DistT d, 
               std::random_access_iterator_tag)
{
    iter += d;
}

// 这份实现用于 bidirectional 迭代器
template<typename IterT, typename DistT>
void doAdvance(IterT& iter, DistT d, 
               std::bidirectional_iterator_tag)
{
    if(d >= 0) { while(d--) ++iter; }
    else { while(d++) --iter; }
}

// 这份实现用于 input 迭代器（同时包括 forward 迭代器，后者继承前者）
template<typename IterT, typename DistT>
void doAdvance(IterT& iter, DistT d, 
               std::input_iterator_tag)
{
    // 对于 forward 或 input 迭代器，移动负距离会导致不明确（未定义）行为。
    // 这儿以抛出异常取而代之。
    if(d < 0) {
        throw std::out_of_range("Negative distance");
    }
    while(d--) ++iter;
}

template<typename IterT, typename DistT>
void advance(Iter& iter, DistT d)
{
    doAdvance(iter,d,
        typename std::iterator_traits<IterT>::iterator_category
        );
}
```

&emsp;&emsp;`advance` 用来将某个迭代器移动某个给定距离。它根据不同的迭代器类型提供不同的实现，原始指针是一种 random access 迭代器。通过 traits 我们可以在编译期间获知迭代器的类型（针对原始指针的特化版本处理了迭代器是原始指针的情况），`IterT` 类型在编译期间获知，所以 `iterator_traits<IterT>::iterator_categoty` 也可在编译期间确定。  
&emsp;&emsp;在 `advance` 内使用 `if` 条件判断 `iterator_category` 也能实现相同机能，但 `if` 语句在运行期才会核定。重载（overloading）是一种针对类型而发生的“编译期条件句”，在编译期对类型执行 if...else 测试，根据实参调用最匹配的函数，相当于 `if( f1最匹配p ) { f1(p); }`。为了让 `advance` 的行为在编译期间确定下来，使用函数 `doAdvance` 的重载，内含 `advance` 的本质内容，但各自接受不同类型的 `iterator_category` 对象。  
&emsp;&emsp;总结一下，traits class 的设计和实现：

+ 确认若干你希望将来可取得的类型相关信息。例如对迭代器而言，我们希望将来可取得其分类（category）。
+ 为该信息选择一个名称（例如 `iterator_category`）
+ 提供一个 template 和一组特化版本（例如稍早说的 `iterator_traits`），内含你希望支持的类型相关信息。

&emsp;&emsp;traits class的使用：

+ 建立一组重载函数（身份像劳工）或函数模板（例如 `doAdvance`），彼此间的差异只在于各自的 traits 参数。令每个函数实现码与其接受之 traits 信息相应和。
+ 建立一个控制函数（身份像工头）或函数模板（例如 `advance`），他调用上诉那些“劳工函数”并传递 traits class 所提供的信息。

### 48 Be aware of template metaprogramming

&emsp;&emsp;认识 template 元编程。  
&emsp;&emsp;Template metaprogramming （模板元编程）是以 c++ 写成，执行于 c++ 编译器内的程序，一旦 TMP 程序结束执行，其输出，也就是 template 具现出来的若干 c++ 源码，便会一如往常地被编译。TMP 将工作由运行期移往编译期，因而得以实现早期错误侦测和更高的执行效率。  
&emsp;&emsp;如果条款 47 的 `advance` 函数改用 `if` 在运行期实现条件判断：

```c++
template<typename IterT, typename DistT>
void advance(Iter& iter, DistT d)
{
    if (typeid(typename std::iterator_traits<IterT>::iterator_category)
        == typeid(std::random_access_iterator_tag)) {
        iter += d;      // 针对 random access 迭代器，使用迭代器算术运算。
    }
    else {
        if (d >= 0) { while(d--) ++iter; }  // 针对其他迭代器分类
        else { while(d++) --iter; }         // 反复调用 ++ 或 --
    }
}

int main(){
    std::list<int>::iterator iter;
    ...
    advance(iter, 10);      // 上述实现无法通过编译。
}
```

&emsp;&emsp;注意，类型测试发生于运行期而非编译期，“运行期类型测试” 代码会出现在（或说被连接于）可执行文件中（由 TMP 具现化后编译而成）。`advance(iter, 10)` 的调用将 template 参数 `Iter` 和 `DistT` 分别替换为 `std::list<int>::iterator` 和 `int`，编译器具现化对应的 `advance` 函数源码并编译它，然而对于一个 `std::list<int>::iterator` 类型的 `iter`，在其身上执行 `+=` 总是会失败，虽然我们知道绝不会执行 `+=` 那一行，因为在 `if` 类型测试中总是会失败，但是编译器必须确保所有源码都有效，纵使是不会执行起来的源码！`iter` 参数必须满足 `+=` 的要求，既然如此，`if` 类型测试就成了虚设。与此对比的是 traits_based TMP 解法，其针对不同类型而进行的代码，被拆分为不同的函数，每个函数所使用的操作（操作符）都可施行于该函数所对付的类型。  
&emsp;&emsp;TMP 的阶乘运算示范如何通过 “递归模板具现化” 实现循环，以及如何在 TMP 中创建和使用变量：

```c++
template<unsigned n>
struct Factorial {
    enum { value = n * Factorial<n-1>::value };
};

template<>
struct Factorial<0> {       // 特殊情况：
    enum { value = 1 };     // Factorial<0> 的值是 1
}
```

&emsp;&emsp;只要指涉 `Factorial<n>::value` 就可以得到 n 阶乘值。条款 2 提到 “enum hack” 是 TMP 的基础技术。

## 八. Customizing new and delete

&emsp;&emsp;本章只讨论 `operator new` 和 `operator delete` （用来分配单一对象，Arrays 所用的内存由 `operator new[]` 分配出来，并由 `operator delete[]` 归还，带`[]` 的版本同不带`[]`的叙述）以及 new-handler（当 `operator new` 无法满足客户的内存需求时所调用的函数）。注意，由于 heap 是一个可被改动的全局性资源，因此多线程系统充斥着发狂访问这一类资源的 *race conditions（竞速状态）* 出现机会。STL 容器所使用的 heap 内存由容器所拥有的分配器对象（allocator objects）管理，不是被 `new` 和 `delete` 直接管理，本章不讨论 STL 分配器。  
&emsp;&emsp;这一章的条款互相穿插，显得比较杂乱。主要内容就是上一段提到的概念。  

### 49 Understand the behavior of the new-handler

&emsp;&emsp;了解 new-handler 的行为。  
&emsp;&emsp;当 operator new 无法满足某一内存分配需求时，它会调用一个客户指定的错误处理函数 —— new-handler。客户通过调用 `set_new-handler`（声明于 `<new>` 的一个标准程序库函数）指定这个 new-handler。  

```c++
namespace std {
    // new_handler 是个函数指针的 typedef，该函数无参数也不返回任何东西
    typedef void (*new_handler)();
    // set_new_handler 的参数和返回值 都是 new_handler 类型，
    // 绑定新的 new_handler，
    // 返回函数调用前正在执行（但马上就要被替换）的那个 new_handler。
    new_handler set_new_handler (new_handler p) throw();
}
```

&emsp;&emsp;因此，一个设计良好的 new_handler 必须做以下事情：

+ 让更多内存可被使用。
+ 安装另一个 new_handler。在下次尝试内存分配，且不被满足时，会调用这个 new_handler。
+ 卸除 new_handler。—— 将 `null` 指针传给 `set_new_handler`，一旦没有安装任何 new_handler，operator_new 会在内存分配不成功时抛出异常。
+ 抛出 `bad_alloc`（或派生自 `bad_alloc`）的异常。
+ 不返回。通常调用 `abort` 或 `exit` 。

&emsp;&emsp;上面提到的是 global new-handler。对于 class 来说，如何设计 class 专属之 new_handlers？书中先是给出单一 class 的解决方案，再引出 template 通用化设计，由于解决方案相同，这儿仅记录一下 template 通用版：

```c++
// 为确保原本的 new_handler 总是能够被重新安装回去，
// 将 global new-handler 视为资源并遵守条款13的忠告，
// 运用资源管理对象管理资源防止资源泄露。
class NewHandlerHolder {
public:
    // 取得目前的 new_handler
    explicit NewHandlerHolder(std::new_handler nh)
    : handler(nh) {}
    // 释放它
    ~NewHandlerHolder()
    { std::set_new_handler(handler); }
private:
    std::new_handler handler;                 // 记录下来
    // 阻止 copying（见条款14）
    NewHandlerHolder(const NewHandlerHolder);
    NewHandlerHolder& operator=(const NewHandlerHolder&);
};

template<typename T>
class NewHandlerSupport {
public:
    static std::new_handler set_new_handler(std::new_handler p) throw();
    static void* operator new(std::size_t size) throw(std::bad_alloc);
    ...
private:
    static std::new_handler currentHandler;
};

template<typename T>
std::NewHandlerSupport<T>::set_new_handler(std::new_handler p) throw()
{
    std::new_handler oldHandler = currentHandler;
    currentHandler = p;
    return oldHandler;
}

template<typename T
void* NewHandlerSupport<T>::operator new(std::size_t size) throw(std::bad_alloc)
{
    // 安装 T 的 new-handler
    NewHandlerHolder h(std::set_new_handler(currentHandler));
    return ::operator new(size);    // 分配内存或抛出异常
}                                   // 恢复 global new-handler

// 以下将每一个 currentHandler 初始化为 null
template<typename T
std::new_handler NewHandlerSuppotr<T>::currentHandler = 0;

//................ 上面是设计和实现 ...................
//.................. 下面是使用 ......................

class Widget: public NewHandlerSuppotr<Widget>{
    ... // 不必声明 set_new_handler 或 operator new
};

void outOfMem();

int main()
{
    Widget::set_new_handler(outOfMem);
    Widget* pw1 = new Widget;
    std::string* ps = new std::string;
    Widget::set_new_handler(0);
    Widget* pw2 = new Widget;
}
```

&emsp;&emsp;这个设计的 base class 部分让 derived classes 继承它们所需的 `set_new_handler` 和 `operator new`，而 template 则确保每一个 derived class 获得一个**实体互异**的 `currentHandler` 成员变量。它可被任何有所需要的 class 的使用，例如上面的 `Widget`。注意，类型参数 `T` 只是用来区分不同的 derived class，Template 机制会自动为每一个 `T`（`newHandlerSupport` 赖以具现化的根据）生成一份 `currentHandler`。至于 `Widget` 那个怪异的继承，被叫做“怪异的循环模板模式”（curiously recurring template pattern; CRTP）。  
&emsp;&emsp;还有另一形式的 operator new —— 传统的 “分配失败便返回 `null`” 行为，这个形式被称为 “nothrow” 形式（定义于头文件 `<new>`），调用像这样：`Widget* pw2 = new (std::nothrow) Widget;`如果分配失败，返回 `null`。注意，内存分配成功，即 operator new 调用成功，class 构造函数会被调用，而 class 构造函数可能会抛出异常。因此，`pw2` 的那样的表达式不保证绝不导致异常。  

### 50 Understand when it makes sense to replace new and delete

&emsp;&emsp;了解 `new` 和 `delete` 的合理替换时机。  
&emsp;&emsp;有许多理由需要写个自定的 `new` 和 `delete`，包括改善效能、对 heap 运行错误进行调试、收集 heap 使用信息。但这并非易事，条款除了讲述大量替换的理由，还陈诉出容易被漠视的细节（它们用来区分“几乎行得通”和“真正行得通”的制品），齐位（alignment）是其中之一：许多计算机体系结构（computer architectures）要求特性的类型必须放在特定的内存地址上，有些体系结构宣称齐位条件满足，便提供较佳效率。C++ 要求所有 operator news 返回的指针都有适当的对齐（取决于数据类型），令 operator new 返回一个得自 `malloc`（满足齐位） 的指针是安全的。但返回一个得自 `malloc` 但被处理过（例如，偏移）的指针，没人能够保证它的安全。  
&emsp;&emsp;需要写这玩意之前，最好先了解编译器的内存管理器、商业产品或开放源码领域中的内存管理器，看看它们能否带给你问题的答案。  

### 51 Adhere to convention when wirting new and delete

&emsp;&emsp;编写 `new` 和 `delete` 时需固守常规。  
&emsp;&emsp;条款 50 解释什么时候你会想写个自己的 operator new/delete，本条款则解释编写时遵守的规则。  
&emsp;&emsp;一致性 operator new 的要求：

+ 内含一个无穷循环，并在其中尝试分配内存。
+ 必须返回正确的值。有能力供应客户申请的内存时，返回一个指针指向分配的内存。
+ 内存不足时必须调用 new-handing 函数。operator new 不止一次尝试分配内存，每次分配内存失败后都调用 new handing 。
+ 必须有对付零内存需求的准备。C++ 规定，即使客户要求 0 bytes，operator new 也得返回一个合法指针。
+ 避免不慎掩盖正常形式的 new。更偏近对 class 的接口要求而非实现要求，详见 条款52。
+ class 专属版本还应该处理 “比正确大小更大的（错误）申请”。写出定制内存管理器的一个最常见理由是为针对某特定 class 的对象分配行为提供最优化，却不是为了该 class 的任何 derived classes。一旦 class 被继承下去，有可能 base class 的operator new 被调用以分配 derived class 对象（大小更大）。最佳做法：改用标准的 operator new —— 调用 global 中的那个 operator new —— `::operator new(size)`。

&emsp;&emsp;operator new 伪码（pseudocode）：

```c++
// non-member 版本
void* operator new(std::size_t size) throw(std::bad_alloc)
{// 你的operator new 可能接受额外参数
    using namespace std;
    if (using == 0) {                   // 处理 0-byte 申请。
        size = 1;                       // 将它视为 1-byte 申请。
    }
    while (true) {// 这个无穷循环满足 条款 49 的 new_handler 要求
        尝试分配 size bytes;
        if ( 分配成功 )
            return (一个指针：指向分配得来的内存)；
        // 分配失败：找出目前的 new-hading 函数（见下）
        // 没有之间办法可以1取得 new-handing 函数指针，
        // 所以必须调用 set_new_handler 找它出来 —— 注意多线程环境的问题
        new_handler globalHandler = set_new_handler(0);
        set_new_handler(globalHandler);

        if (globalHandler) (*globalHandler)();
        else throw std::bad_alloc();
    }
}
// member 版本
void* Base::operator new(std::size_t size) throw(std::bad_alloc)
{
    // if 存在 0-byte 检验，
    // sizeof(Base) 无论如何不为零 —— “非附属（独立式）对象必须有非零大小”，
    // ::operator new 有责任合理处理 0-byte 申请。
    if (size != sizeof(Base))           // 如果大小错误
        retuen ::operator new(size);    // 令标准 operator new 起而处理
    ...                                 // 否则在这里处理
}   
```

&emsp;&emsp;operator delete 规矩：

+ C++ 保证“删除 `null` 指针永远安全”。
+ 与 operator new 一样，class 专属版本应该处理 “比正确大小更大的（错误）申请”。

```c++
void Base::operator delete(void* rawMemory, std::size_t size) throw()
{
    if (rawMemory == 0) return;     // 检查 null 指针，non-member 版本同
    if (size != sizeof(Base)) {     // 如果大小错误，令标准版处理申请
        :: operator delete(rawMemory);
        return;
    }
    ...                             // 否则，在这里归还 rawMemory 所指的内存
    return;
}
```

&emsp;&emsp;让 base classes 拥有 virtual 析构函数的另一理由：被删除对象派生自某个 base class 而后者欠缺 virtual 析构函数，那么 C++ 传给 operator delete 的 `size_t` 数值可能不正确。  
&emsp;&emsp;class 专属值 “array内存分配行为” 由 `operator new[]` 控制，实现它唯一需要做的就是：分配一块未加工内存（raw memory）。因为无法对 array 之内迄今为止尚未存在的元素对象做任何事情，不知道它将含有多少个元素对象等等等等。  

### 52 Write placement delete if you write placement new

&emsp;&emsp;写了 placement new 也要写 placement delete。  
&emsp;&emsp;声明它们时，也确定不要无意识（非故意）地掩盖了它们的正常版本。  
&emsp;&emsp;`Widget* pw = new Widget;` 在一个这样的 new 表达式中，有两个函数被调用：一个是用以分配内存的 operator new，一个是 `Widget` 的 default 构造函数。如果内存分配成功，而 `Widget` 构造函数抛出异常，C++ 运行期系统有责任取消 operator new 的分配并恢复旧观，但是运行期系统无法知道真正被调用的那个 operator new 如何运作，它寻找 “参数个数和类型都与 operator new 相同” 的某个 operator delete，并调用该 operator delete 恢复旧观。如果未找到，则运行期系统什么也不会做 —— 导致内存泄露。当只使用正常形式的 `new` 和 `delete`，运行期系统毫无疑问可以找出 `new` 对应的 `delete`，当声明了非正常形式的 `operator new`，也就是带有附加参数的 `operator new` —— placement new 时，有必要声明一个 placement delete 对应于这个 placement new。placement new 的调用可能像这样：`Widget* pw = new (std::cerr) Widget`。至于，正常形式 和 placement形式 的 delete/new 的模样，后面会给出。  
&emsp;&emsp;注意，placement delete 只有在 “伴随 placement new 调用而触发的构造函数” 出现异常时才会被调用，对一个由 placement new 分配的对象施行正常形式的 `delete` （`delete pw`）绝不会导致调用 placement delete。这意味着，对于 placement new 相关的内存泄漏，提供正常的 operator delete 和 对应的 palcement delete（额外参数和 placement new 一样），就不会再失眠了（至少不因本条款说的这些内存泄露失眠）。  
&emsp;&emsp;缺省情况下，C++ 在 global 作用域内提供以下形式的 operator new：

```c++
    // normal new.
    void* operator new(std::size_t) throw(std::bad_alloc);
    // placement new （placement new 术语的另一意义）
    void* operator new(std::size_t, void *) throw();
    // nothrow new , 见条款 49 末。
    void* operator new(std::size_t, const std::nothrow_t&) throw();
```

&emsp;&emsp;由于成员函数的名称会掩盖其外围作用域中的相同名称，在 class 内声明任何 operator news，它会遮掩上述这些标准形式以及任何继承而得的版本。除非你就是要阻止 class 的客户使用这些形式，否则请确保它们在你所生成的任何定制型 operator new 之外还可用，也请确定提供对应的 operator delete。如果你希望这些函数有着平常的行为，令你的 class 专属版本调用 global 版本即可。  
&emsp;&emsp;一个简单做法，完成以上所言：

```c++
class StandardNewDeleteForms {
public:
    // normal new/delete
    static void* operator new(std::size_t size) throw(std::bad_alloc)
    { return ::operator new(size); }
    static void operator delete(void* pMemory) throw()
    { :: operator delete(pMemory); }
    // placement new/delete
    static void* operator new(std::size_t size, void* ptr) throw()
    { return ::operator new(size, ptr); }
    static void operator delete(void* pMemory, void* ptr) throw()
    { return ::operator delete(pMemory, ptr); }
    // nothrow new/delete
    static void* operator(std::size_t size, const std::nothrow_t& nt) throw()
    { return ::operator new(size, nt); }
    static void operator delete(void *pMemory, const std::nothrow_t&) throw()
    { ::operator delete(pMemory); }
};

class Widget: public StandardNewDeleteForms {       // 继承标准形式
    public:
    using StandardNewDeleteForms::operator new;     // 让这些形式可见
    using StandardNewDeleteForms::operator delete;

    // 添加一个自定的 placement new
    static void* operator new(std::size_t size, std::ostream& logStream) throw(std::bad_alloc);
    // 添加一个对应的 placement delete
    static void operator delete(void* pMemory, std::ostream& logStream) throw();
    ...
};
```

## 九. Miscellany

&emsp;&emsp;杂项讨论。

### 53 pay attention to compiler warnings

&emsp;&emsp;不要轻忽编译器的警告。  
&emsp;&emsp;严肃对待编译器发出的警告信息。在打发某个警告信息之前，请确定你真正了解它意图说出的精确意义。在最高警告级别下也无任何警告信息的程序最是理想，有了上诉经验和对警告信息的深刻理解，倒是可以选择忽略某些警告信息。  
&emsp;&emsp;警告信息天生和编译器相依，不同的编译器有不同的警告标准。因此，不要过度依赖编译器的报警能力。草率编程然后依赖编译器指出错误，并不可取。不同编译器对待事物的态度并不相同。

### 54 Familiarize yourself with the standard library, including TR1

&emsp;&emsp;让自己熟悉包括 TR1 在内的标准程序库。  
&emsp;&emsp;TR1 是一份规范，代表 “Technical Report 1”，这是 C++ 程序工作小组对钙粉文档的称呼。TR1 描述加入 C++ 标准程序库的诸多新机能，它所叙述的机能单位都放在 `std` 的嵌套命名空间 `tr1` 内。  
&emsp;&emsp;TR1 自身只是一份文档，为了取得它所规范的那些机能，还需要取得实现代码，这些代码最终会随编译器出货。在此之前，对于 TR1-like（许多 TR1 机能奠基于 Boost 程序库，有些 Boost 机能并不完全吻合 TR1 规范） 机能而言，Boost 是个绝佳资源。

```c++
// 一个命名空间上的小伎俩
// 在编译器 TR1 实现品到来前，以 Boost 的 TR1-like 程序库做为一时权宜
// 令编译器对待 references to std::tr1 就像对待 reference to boost 一样
namespace std {
    namespace tr1 = ::boost;    // namespace std::tr1 是
}                               // namespace boost 的一个别名
// 当编译器提供 TR1 实现品后，消除上面的 namespace 别名，而后指涉 std::tr1 的代码继续生效。
```

### 55 Familiarize yourself with Boost

&emsp;&emsp;让自己熟悉 Boost 。  
&emsp;&emsp;<https://www.boost.org>  

---

## References

《 Effective C++ 》（第三版）  
《 C++ Primer 5 》
