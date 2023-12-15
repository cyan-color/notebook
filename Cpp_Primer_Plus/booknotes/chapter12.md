# 第十二章 类和动态内存分配

本章内容包括：

- 对类成员使用动态内存分配；
- 隐式和显式复制构造函数；
- 隐式和显式重载赋值运算符；
- 在构造函数中使用 `new` 所必须完成的工作；
- 使用静态类成员；
- 将定位 `new` 运算符用于对象；
- 使用指向对象的指针；
- 实现队列抽象数据类型（ADT）。

对类使用 `new` 和 `delete` 将影响构造函数和析构函数的设计以及运算符的重载。

## 12.1 动态内存和类

### 12.1.1 复习示例和静态类成员

```Cpp
// strngbad.h -- flawed string class definition
#include <iostream>
#ifndef STRNGBAD_H_
#define STRNGBAD_H_
class StringBad
{
private:
    char * str;                // pointer to string
    int len;                   // length of string
    static int num_strings;    // number of objects
public:
    StringBad(const char * s); // constructor
    StringBad();               // default constructor
    ~StringBad();              // destructor
// friend function
    friend std::ostream & operator<<(std::ostream & os,
                       const StringBad & st);
};
#endif

```

对这个声明，需要注意的有两点。首先，它使用char指针（而不是 char数组）来表示姓名。这意味着类声明没有为字符串本身分配存储空间，而是在构造函数中使用new来为字符串分配空间。这避免了在类声明中预先定义字符串的长度。

其次，将num_strings成员声明为静态存储类。静态类成员有一个特点：无论创建了多少对象，程序都只创建一个静态类变量副本。也就是说，`StringBad` 类的所有对象共享同一个 `num_strings`。假设创建了10个 `StringBad` 对象，将有10个 `str` 成员和10个 `len` 成员，但只有一个共享的 `num_strings` 成员。


```Cpp
// strngbad.cpp -- StringBad class methods
#include <cstring>                    // string.h for some
#include "strngbad.h"
using std::cout;

// initializing static class member
int StringBad::num_strings = 0;

// class methods

// construct StringBad from C string
StringBad::StringBad(const char * s)
{
    len = std::strlen(s);             // set size
    str = new char[len + 1];          // allot storage
    std::strcpy(str, s);              // initialize pointer
    num_strings++;                    // set object count
    cout << num_strings << ": \"" << str
         << "\" object created\n";    // For Your Information
}

StringBad::StringBad()                // default constructor
{
    len = 4;
    str = new char[4];
    std::strcpy(str, "C++");          // default string
    num_strings++;
    cout << num_strings << ": \"" << str
         << "\" default object created\n";  // FYI
}

StringBad::~StringBad()               // necessary destructor
{
    cout << "\"" << str << "\" object deleted, ";    // FYI
    --num_strings;                    // required
    cout << num_strings << " left\n"; // FYI
    delete [] str;                    // required
}

std::ostream & operator<<(std::ostream & os, const StringBad & st)
{
    os << st.str;
    return os;
}
```

`int StringBad::num_strings = 0;` 这条语句将静态成员 `num_strings` 的值初始化为零。注意：**不能在类声明中初始化静态成员变量，这是因为类声明中只描述如何分配内存，但并不分配内存**。

可以在类声明之外使用单独的语句来进行初始化，这是因为静态类成员是单独存储的，而不是对象的组成部分。

另外，初始化是在方法文件中，而不是在类声明文件中进行的，这是因为类声明位于头文件中，程序可能将头文件包括在其他几个文件中。如果在头文件中进行初始化，将出现多个初始化语句副本，从而引发错误。

上述代码中，`strlen()`返回字符串长度，但不包括末尾的空字符，因此构造函数将`len`加1，使分配的内存能够存储包含空字符的字符串。

删除对象可以释放对象本身占用的内存，但并不能自动释放属于对象成员的指针指向的内存。因此，必须使用析构函数。在析构函数中使用delete语句可确保对象过期时，由构造函数使用new分配的内存被释放。

> 在构造函数中使用 `new` 来分配内存时，必须在相应的析构函数中使用 `delete` 来释放内存。如果 使用 `new[]`（包括中括号）来分配内存，则应使用`delete[]`（包括中括号）来释放内存。

`StringBad`的第一个版本有许多故意留下的缺陷，是一个很糟糕的类（找到该类的错误之处，甚至可以作为一道困难的编程题），这些缺陷使得输出是不确定的。例如，有些编译器无法编译它。虽然输出的具体内容有所差别，但基本问题和解决方法（稍后将介绍） 是相同的。

程序输出结果：

![image-20210812111740343](https://static.fungenomics.com/images/2021/08/image-20210812111740343.png)

`callme2()` 按值（而不是按引用）传递 `headline2`，结果表明这是一个严重的问题。

首先，将 `headline2` 作为函数参数来传递从而**导致析构函数被调用**。这是因为函数的参数  `sb` 是一个临时变量，当函数调用结束后会释放这个临时变量，从而导致析构函数被调用，糟糕的源头在于析构函数中恰巧就释放了字符串。 其次，虽然按值传递可以防止原始参数被修改，但实际上函数已使原始字符串无法识别，导致显示一些非标准字符（显示的文本取决于内存中包含的内容）。

因为自动存储对象被删除的顺序与创建顺序相反，所以最先删除的 3个对象是`knots`、`sailor`和`sport`。删除`knots`和`sailor`时是正常的，但在删除`sport`时，`Dollars`变成了`Doll8`(或其他)。对于`sport`，程序只使用它来初始化 `sailor`，但这种操作修改了 `sports`（这又可以做一道题）。

具体而言，程序中 `Stringbad sailor = sports;` 这个语句既不是调用默认构造函数也不是调用参数为 `const char*` 的构造函数，而是等价于 `StringBad sailor=StringBad(sports);` ，又因为`sports`的类型为`StringBad`，因此与之相应的构造函数原型应该是 `StringBad(const String &);`，**但这个构造函数在`StringBad`类中没有显式声明更没有定义。这时当我们使用一个对象来初始化另一个对象时，编译器将自动生成上述构造函数（称为复制构造函数，因为它创建对象的一个副本）**。但自动生成的复制构造函数不知道需要更新静态变量`num_string`，因此会将计数方案搞乱（这就是复制对象带来的问题）。实际上，这个例子说明的所有问题都是由编译器自动生成的成员函数引起的。

最后被删除的两个对象（`headline2`和 `headline1`）已经无法识别。


### 12.1.2 特殊成员函数

`StringBad`类的问题是由特殊成员函数引起的。这些成员函数是自动定义的，就`StringBad`而言，这些函数的行为与类设计不符。具体地说， C++自动提供了下面这些成员函数：
- 默认构造函数；
- 默认析构函数；
- 复制构造函数；
- 复制预运算符；
- 地址运算符。

更准确地说，编译器将生成上述最后三个函数的定义——如果程序使用对象的方式要求这样做。例如，如果您将一个对象赋给另一个对象，编译器将提供赋值运算符的定义。

结果表明，StringBad类中的问题是由隐式复制构造函数和隐式赋值运算符引起的。

1．默认构造函数

默认情况下，编译器将提供一个不接受任何参数，也不执行任何操作的构造函数（默认的默认构造函数），这是因为创建对象时总是会调用构造函数。

如果定义了构造函数，C++将不会定义默认构造函数。

2．复制构造函数

复制构造函数用于将一个对象复制到新创建的对象中。也就是说，**它用于初始化过程中（包括按值传递参数），而不是常规的赋值过程**（那赋值的时候怎么办？赋值靠赋值运算符=，见12.1.4），原型是：`Class_name(const Class_name &)`。它接受一个指向类对象的常量引用作为参数。

对于复制构造函数，需要知道两点：何时调用和有何功能。

3．何时调用复制构造函数

新建一个对象并将其初始化为同类现有对象时，复制构造函数都将被调用。 最常见的情况是将新对象显式地初始化为现有的对象。

假设motto式一个StringBad对象，则下面4种声明都将调用复制构造函数
```cpp
StringBad ditto(motto);                          // calls StringBad(const StringBad &)
StringBad metoo = motto;                         // calls StringBad(const StringBad &)
StringBad also = StringBad(motto);               // calls StringBad(const StringBad &)
StringBad * pStringBad = new StringBad(motto);   // calls StringBad(const StringBad &)
```

其中中间的2种声明可能会使用复制构造函数直接创建metoo和 also，也可能使用复制构造函数生成一个临时对象，然后将临时对象的内容赋给metoo和also，这取决于具体的实现。最后一种声明使用motto 初始化一个匿名对象，并将新对象的地址赋给pStringBad指针。

**每当程序生成了对象副本时，编译器都将使用复制构造函数**。具体地说，当函数按值传递对象（如程序清单12.3中的 `callme2()`）或函数返回对象时，都将使用复制构造函数。记住，**按值传递意味着创建原始变量的一个副本**。编译器生成临时对象时，也将使用复制构造函数。

何时生成临时对象随编译器而异，但无论是哪种编译器，**当按值传递和返回对象时，都将调用复制构造函数**。

> 由于按值传递对象将调用复制构造函数，因此应该按引用传递对象。这样可以节省调用构造函数的时间以及存储新对象的空间。


4．默认的复制构造函数的功能

默认的复制构造函数逐个复制非静态成员（成员复制也称为浅复制），复制的是成员的值。

![image-20210812120946029](https://static.fungenomics.com/images/2021/08/image-20210812120946029.png)



### 12.1.3  回到 Stringbad：复制构造函数的哪里出了问题 

第一个异常，num_string是负值。当`callme2()`被调用时，复制构造函数被用来初始化 `callme2()` 的形参，还被用来将对象 `sailor` 初始化为对象 `sports`。默认的复制构造函数不说明其行为，因此它不指出创建过程，也不增加计数器 num_strings的值。但析构函数更新了计数，并且在任何对象过期时都将被调用，而不管对象是如何被创建的。程序的输出表明，析构函数的调用次数比构造函数的调用次数多2。

第二个异常之处更微妙，也更危险，其症状之一是字符串内容出现乱码。原因在于隐式复制构造函数是按值进行复制的。例如，对于程序清 单12.3，隐式复制构造函数的功能相当于：`sailor.str = sport.str;`。这里复制的并不是字符串，而是一个指向字符串的指针。也就是说，将sailor初始化为sports后，得到的是两个指向同一个字符串的指针。当operator <<()函数使用指针来显示字符串时，这并不会出现问题。但当析构函数被调用时，这将引发问题。析构函数StringBad释放str 指针指向的内存，因此释放sailor的效果如下：

`sports.str`指向的内存已经被 `sailor` 的析构函数释放，这将导致不确定的、可能有害的后果。

1．定义一个显式复制构造函数以解决问题

解决类设计中这种问题的方法是进行深度复制（deep copy）。也就是说，**复制构造函数应当复制字符串并将副本的地址赋给`str`成员，而不仅仅是复制字符串地址**。这样每个对象都有自己的字符串，而不是引用另一个对象的字符串。调用析构函数时都将释放不同的字符串，而不会试图去释放已经被释放的字符串。可以这样编写`String`的复制构造函数：

```Cpp
String::String(const char * s)     // construct String from C string
{
    len = std::strlen(s);          // set size
    str = new char[len + 1];       // allot storage
    std::strcpy(str, s);           // initialize pointer
    num_strings++;                 // set object count
}
```

![image-20210812121927440](https://static.fungenomics.com/images/2021/08/image-20210812121927440.png)

### 12.1.4 Stringbad的其他问题：赋值运算符

并不是程序清单12.3的所有问题都可以归咎于默认的复制构造函 数，还需要看一看默认的赋值运算符。C++允许类对象赋值，这是通过自动为类重载赋值运算符实现的。这种运算符的原型如下：

```
Class_name & Class_name::operator=(const Class_name &);
```

它接受并返回一个指向类对象的引用。

1．赋值运算符的功能以及何时使用它

将已有的对象赋给另一个对象时，将使用重载的赋值运算符。初始化对象时，并不一定会使用赋值运算符，而更可能是调用复制构造函数。

```Cpp
StringBad metoo = knot; 
```
这里，`metoo` 是一个新创建的对象，被初始化为 `knot` 的值，因此使用复制构造函数。然而，正如前面指出的，实现时**也可能分两步来处理这条语句：使用复制构造函数创建一个临时对象，然后通过赋值将临时对象的值复制到新对象中**。这就是说，初始化总是会调用复制构造函数，而使用 `=` 运算符时也可能调用赋值运算符。

与复制构造函数相似，赋值运算符的隐式实现也对成员进行逐个复制，静态成员不受影响。

2．赋值的问题出在哪里

程序清单12.3将headline1赋给knot：`knot=headline1;`。为knot调用析构函数时，正常。为Headline1调用析构函数时，异常。**出现的问题与隐式复制构造函数相同：数据受损。这也是成员复制的问题，即导致headline1.str和knot.str指向相同的地址**。

3. 解决赋值的问题

解决办法是提供赋值运算符（进行深度复制）定义，这与复制构造函数相似，但也有一些差别:

- 由于目标对象可能引用了以前分配的数据，所以函数应使用`delete []` 来释放这些数据；
- **函数应当避免将对象赋给自身；否则，给对象重新赋值前，释放内 存操作可能删除对象的内容**；
- 函数返回一个指向调用对象的**引用**（注意是引用，不是值也不是指针）。

```Cpp
// assign a String to a String
String & String::operator=(const String & st)
{
    if (this == &st)
        return *this;
        
    delete [] str;             // 先释放原指针指向的字符串
    len = st.len;
    str = new char[len + 1];
    std::strcpy(str, st.str);
    return *this;
}
```

赋值操作并不创建新的对象，因此不需要调整静态数据成员 `num_strings` 的值。

## 12.2 改进后的新String类

首先，添加前面介绍过的复制构造函数和赋值运算符，使类能够正确管理类对象使用的内存。其次，由于您已经知道对象何时被创建和释放，因此可以让类构造函数和析构函数保持沉默，不再在每次被调用时都显示消息。另外，也不用再监视构造函数的工作情况，因此可以简化默认构造函数，使之创建一个空字符串，而不是“C++”。

新默认构造函数中：

```Cpp
String::String()                   // default constructor
{
    len = 4;
    str = new char[1];
    str[0] = '\0';                 // default string
    num_strings++;
}
```

为什么代码为 `str=new char[1];` 而不是 `str = new char;`，这两个方式相同，但区别在于前者和类析构函数兼容，而后者不兼容。这是析构函数：

```Cpp
String::~String()                     // necessary destructor
{
    --num_strings;                    // required
    delete [] str;                    // required
}
```

对 `operator>` 的重载很妙啊，直接利用了 `operator<` 的重载结果：

### 12.2.3 使用中括号表示法访问字符

在C++中，两个中括号组成一个运算符——中括号运算符，可以使用方法operator 来重载该运算符。通常，二元C++运算符（带两个操作数）位于两个操作数之间，例如2+5。但对于中括号运算符，**一个操作数位于第一个中括号的前面，另一个操作数位于两个中括号之间**。例如：`city[0]` 中，`city`是第一个操作数，`[]`是运算符，`0`是第二个操作数。

**在重载时，C++将区分常量和非常量函数的特征标**。

```Cpp
// read-write char access for non-const String
char & String::operator[](int i)
{
    return str[i];
}

// read-only char access for const String
const char & String::operator[](int i) const
{
    return str[i];
}
```

有了上述定义后，就可以读/写常规String对象了；而对于const String对象，则只能读取其数据。

> 重载要注意同时考虑对const 和非 const 变量进行。
```cpp
String text("Once upon a time");
const String answer("futile");
cout << text[1];    // ok, uses non-const version of operator[]
cout << answer[1];  // ok, uses const version of operator[]
cin >> text[1];    // ok, uses non-const version of operator[]
cin >> answer[1];  // compile-time error
```

也可以修改内容：

```Cpp
String means("might");
means[0] = 'r';   // 这一句相当于 means.str[0] = 'r', 但 str 是私有成员，实际是没法在成员函数之外这样使用的。
```

### 12.2.4 静态类成员函数

首先，不能通过对象调用静态成员函数；实际上，静态成员函数甚至不能使用this指针。

其次，由于静态成员函数不与特定的对象相关联，因此只能使用静态数据成员。

```cpp
int count = String::HowMany();  // invoking a static member function
```

### 12.2.5 进一步重载赋值运算符

![image-20210812170559987](https://static.fungenomics.com/images/2021/08/image-20210812170559987.png)

重载>>运算符提供了一种将键盘输入行读入到String对象中的简单方法。它假定输入的字符数不多于String::CINLIM的字符数，并丢弃多余的字符。在if条件下，如果由于某种原因（如到达文件尾或`get(char *, int)` 读取的是一个空行）导致输入失败，istream对象的值将置为 false。


## 12.3 在构造函数中使用new时应注意的事项

使用new初始化对象的指针成员时必须特别小心：
- 如果在构造函数中使用new来初始化指针成员，则应在析构函数中使用delete；
- new和delete必须相互兼容。`new`对应于`delete`，`new[]`对应于`delete[]`；
- 如果有多个构造函数，则必须以相同的方式使用new，要么都带中括号，要么都不带。因为只有一个析构函数，所有的构造函数都必须与它兼容。然而，可以在一个构造函数中使用new初始化指针，而在另一个构造函数中将指针初始化为空（0或C++11中的 nullptr），这是因为delete（无论是带中括号还是不带中括号）可以用于空指针；
- 应定义一个复制构造函数，通过深度复制将一个对象初始化为另一 个对象；

![image-20210812183412719](https://static.fungenomics.com/images/2021/08/image-20210812183412719.png)

- 应当定义一个赋值运算符，通过深度复制将一个对象复制给另一个对象；

![image-20210812183448925](https://static.fungenomics.com/images/2021/08/image-20210812183448925.png)

具体地说，该方法应完成这些操作：**检查自我赋值的情况，释放成员指针以前指向的内存，复制数据而不仅仅是数据的地址，并返回一个指向调用对象的引用**。

### 12.3.1 应该和不应该

### 12.3.2 包含类成员的类的逐成员复制

如果您将一个 Magazine对象复制或赋值给另一个Magazine对象，逐成员复制将使用成员类型定义的复制构造函数和赋值运算符。也就是说，复制成员title 时，将使用String的复制构造函数，而将成员title赋给另一个Magazine对 象时，将使用String的赋值运算符，依此类推。

## 12.4 有关返回对象的说明
当成员函数或独立的函数返回对象时，有几种返回方式可供选择。 可以**返回指向对象的引用、指向对象的const引用或const对象**。

### 12.4.1 返回指向const对象的引用

![image-20210812185620612](https://static.fungenomics.com/images/2021/08/image-20210812185620612.png)

这里有三点需要说明。首先，返回对象将调用复制构造函数，而返回引用不会，所以版本2效率更高。其次，引用指向的对象应该在调用函数执行时存在。第三，**v1和v2都被声明为const引用，因此返回类型必须为const，这样才匹配**。

### 12.4.2 返回指向非const对象的引用

两种常见的返回非 `const` 对象情形是，重载赋值运算符以及重载与 `cout` 一起使用的 `<<` 运算符。前者这样做旨在提高效率，而后者必须这样做。

Operator<<()的返回值用于串接输出：

```Cpp
String s1("Good stuff");
cout << s1 << " is comming!";
```

`operator<<（cout, s1）`的返回值成为一个用于显示字符串“is coming!”的对象。返回类型必须是 `ostream &`，而不能仅仅是 `ostream`。**如果使用返回类型`ostream`，将要求调用`ostream`类的复制构造函数，而`ostream`没有公有的复制构造函数**。

### 12.4.3 返回对象

如果被返回的对象是**被调用函数中的局部变量，则不应按引用方式返回它，因为在被调用函数执行完毕时，局部对象将调用其析构函数**。因此，当控制权回到调用函数时，引用指向的对象将不再存在。**在这种情况下，应返回对象而不是引用**。

### 12.4.4 返回 `const` 对象

前面的`Vector::operator+()`定义有一个奇异的属性，它旨在让您能够以下面这样的方式使用它：`net = force1 + force2;`。

然而，这种定义也允许您这样使用它：

```cpp
force1 + force2 = net;
cout << (force1 + force2 = net).magval() << endl;
```

这提出了三个问题。为何编写这样的语句？这些语句为何可行？这些语句有何功能？**首先，没有要编写这种语句的合理理由，但并非所有代码都是合理的**。

这种代码之所以可行，是因为复制构造函数将创建一个临时对象来表示返回值。因此，在前面的代码中，表达式force1 + force2的结果为一个临时对象。在语句1中，该临时对象被赋给net；在语句2和3 中，net被赋给该临时对象。使用完临时对象后，将把它丢弃。

总之，如果方法或函数要返回局部对象，则应返回对象，而不是指向对象的引用。在这种情况下，将使用复制构造函数来生成返回的对象。如果方法或函数要返回一个没有公有复制构造函数的类（如ostream类）的对象，它必须返回一个指向这种对象的引用。最后，有些方法和函数（如重载的赋值运算符）可以返回对象，也可以返回指向对象的引用，在这种情况下，应首选引用，因为其效率更高。

## 12.5 使用指向对象的指针

![image-20210812215628088](https://static.fungenomics.com/images/2021/08/image-20210812215628088.png)

### 12.5.1 再谈`new`和`delete`

![image-20210812220903934](https://static.fungenomics.com/images/2021/08/image-20210812220903934.png)

### 12.5.2 指针和对象小结

- 使用常规表示法来声明指向对象的指针；
- 可以将指针初始化为指向已有的对象；
- 可以使用new来初始化指针，这将创建一个新的对象；
- 对类使用new将调用相应的类构造函数来初始化新创建的对象。

![image-20210812221115586](https://static.fungenomics.com/images/2021/08/image-20210812221115586.png)

![image-20210812221146826](https://static.fungenomics.com/images/2021/08/image-20210812221146826.png)

- 可以使用`->`运算符通过指针访问类方法；
- 可以对对象指针应用解除引用运算符`*`来获得对象。

### 12.5.3 再谈定位new运算符
## 12.6 复习各种技术
### 12.6.1 重载<<运算符

要重新定义 << 运算符，以便将它和cout一起用来显示对象的内容，请定义下面的友元运算符函数：
```cpp
ostream & operator<<(ostream &os, const c_name &obj){
    os << ...;     // display object contents
    return os;
} 
```

### 12.6.2 转换函数
要将单个值转换为类类型，需要创建原型如下所示的类构造函数：

```Cpp
class_name(type_name value);
```

要将类转换为其他类型，需要创建原型如下所示的类成员函数：

```
operator type_name();
```

**虽然该函数没有声明返回类型，但应返回所需类型的值**。

使用转换函数时要小心。可以在声明构造函数时使用关键字 explicit，以防止它被用于隐式转换。

### 12.6.3 其构造函数使用new的类

## 12.7 队列模拟
这里不记录了。

对于`const`数据成员，必须在执行到构造函数体之前，即创建对象时进行初始化。C++提供了一种特殊的语法来完成上述工作，它叫做成员初始化列表（member initializer list）。成员初始化列表由逗号分隔的初始化列表组成（前面带冒号）。它位于参数列表的右括号之后、函数体左括号之前。如果数据成员的名称为 `mdata`，并需要将它初始化为`val`，则初始化器为`mdata(val)`。


**只有构造函数可以使用这种初始化列表语法**。如上所示，对于const类成员，必须使用这种语法。另外，**对于被声明为引用的类成员，也必须使用这种语法**：

**这是因为引用与const数据类似，只能在被创建时进行初始化**。对于简单数据成员（例如front和items），使用成员初始化列表和在函数体中使用赋值没有什么区别。

> 【注意】：
> 这种格式只能用于构造函数；
> 必须用这种格式来初始化**非静态const数据成员**；
> 必须用这种格式来初始化**引用数据成员**。

**不能将成员初始化列表语法用于构造函数之外的其他类方法**。

如果我们不希望复制构造函数被调用，也不允许赋值运算，可以这样做：
```cpp
class Queue{
private:
    Queue(const Queue & q) : qsize(0) {}
    Queue & operator=(const Queue & q) {return *this;}
};
```
这么做有两个作用：第一，它避免了本来将自动生成的默认方法定义；第二，因为这些方法式私有的，所以不能被广泛使用。

这是一种禁用方法的技巧，同时可以作为一种暂时不编写这两个函数的预防措施：与其将来面对无法预料的运行故障，不如得到一个易于跟踪的编译错误，指出这些方法是不可访问的。另外，在定义其对象不允许被复制的类时，这种方法也很有用。

还有没有其他影响需要注意呢？当然有。当对象被按值传递（或返回）时，复制构造函数将被调用。然而，如果遵循优先采用按引用传递对象的惯例，将不会有任何问题。另外，复制构造函数还被用于创建其他的临时对象，但Queue定义中并没有导致创建临时对象的操作，例如重载加法运算符。

## 12.8 总结

本章介绍了定义和使用类的许多重要方面。其中的一些方面是非常微妙甚至很难理解的概念。

在类构造函数中使用new，也可能在对象过期时引发问题。如果对象包含成员指针，同时它指向的内存是由new分配 的，则释放用于保存对象的内存并不会自动释放对象成员指针指向的内存。因此在类构造函数中使用new类来分配内存时，应在类析构函数中 使用delete来释放分配的内存。这样，当对象过期时，将自动释放其指针成员指向的内存。

如果对象包含指向new分配的内存的指针成员，则将一个对象初始化为另一个对象，或将一个对象赋给另一个对象时，也会出现问题。

在默认情况下，C++逐个对成员进行初始化和赋值，这意味着被初始化或被赋值的对象的成员将与原始对象完全相同。如果原始对象的成员指向一个数据块，则副本成员将指向同一个数据块。当程序最终删除这两个对象时，类的析构函数将试图删除同一个内存数据块两次，这将出错。解决方法是：**定义一个特殊的复制构造函数来重新定义初始化，并重载赋值运算符**。

这样，旧对象和新对象都将引用独立的、相同的数据，而不会重叠。由于同样的原因，必须定义赋值运算符。对于每一种情况，最终目的都是执行深度复制，也就是说，复制实际的数据，而不仅仅是复制指向数据的指针。

**C++允许在类中包含结构、类和枚举定义。这些嵌套类型的作用域为整个类**，这意味着它们被局限于类中，不会与其他地方定义的同名结构、类和枚举发生冲突。

C++为**类构造函数**提供了一种可用来**初始化数据成员的特殊语法**。 这种语法包括冒号和由逗号分隔的初始化列表，被放在构造函数参数的右括号后，函数体的左括号之前。每一个初始化器都由被初始化的成员的名称和包含初始值的括号组成。从概念上来说，这些初始化操作是在对象创建时进行的，此时函数体中的语句还没有执行。语法如下：
```cpp
queue(int qs): qsize(qs), items(0), front(NULL), rear(NULL) {}
```
如果数据成员是**非静态const成员或引用，则必须采用这种格式**，但可将C++11新增的类内初始化用于非静态const成员。

