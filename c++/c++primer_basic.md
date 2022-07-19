# C++ 学习笔记

本文所有内容参考自[C++Primer第5版](https://zhjwpku.com/assets/pdf/books/C++.Primer.5th.Edition_2013.pdf)

- [C++ 学习笔记](#c-学习笔记)
  - [Getting Started](#getting-started)
  - [Setting out to C++](#setting-out-to-c)
  - [Dealing with data](#dealing-with-data)
    - [integer](#integer)
    - [bool](#bool)
    - [float](#float)
    - [Type cast](#type-cast)
  - [Compound types](#compound-types)
    - [Arrays](#arrays)
    - [String](#string)

## Getting Started
一个可简单的C语言Helloworld
```c
#include<stdio.h>
int main()
{
  printf("Hello world - AshyEarl!\n");
  return 0;
}
```
C++ 是基于C语言的，也就是C++ 中完全可以只使用C的特性来完成功能，C++ 是C语言的增强版。C语言用来解决与底层硬件打交道，而C++ 更适合用来解决更高层的问题。
在Linux上，C++源文件以大写C为文件后缀，或者cc以及cxx，但是小写的c是用来作为c语言。
| C++ Implementation     | Source Code Extension(s) |
| :--------------------- | :----------------------- |
| Unix                   | C, cc, cxx, c            |
| GNU                    | C++ C, cc, cxx, cpp, c++ |
| Digital Mars           | cpp, cxx                 |
| Borland                | C++ cpp                  |
| Watcom                 | cpp                      |
| Microsoft Visual       | C++ cpp, cxx, cc         |
| Metrowerks CodeWarrior | cpp, cp, cc, cxx, c++    |

Linux上通常使用免费的g++ 来编译链接c++ 源码，通常使用命令cc或者g++来完成
```shell
cc hello.cc -o hello.o
// 可以使用 strip hello.o来去掉符号，减少二进制文件的大小
strip hello.o
```

## Setting out to C++
一个简单的C++ Helloworld
```cpp
// myfirst.cc--displays a message

#include <iostream> // a PREPROCESSOR directive
int main()
{
  using namespace std; // make definitions visible
  cout << "hello we are in c++ world!";
  cout << endl; // start a new line
  cout << "I want say more";
  cout << endl;
  return 0; // terminate main()
}
```
> 当然这里可用使用C语言的方式来输出字符串，因为C++是C语言的进阶版，C语言的东西当然是可以用的拉。

在这里使用cc编译的话没有链接std库，使用`g++`就没有问题
一个简单的调用C++库的例子：
```cpp
// cxxMath.cc -- Use math library
#include <iostream>
#include <cmath> // math library
using namespace std;
int main()
{
  cout << "This is a test!" << endl;
  cout << "Please input a number:" << endl;
  int test = 0;
  cin >> test;
  cout << "sqrt(test) is " << sqrt(test) << endl;
}
```
> cout 和 cin 就体现了C++的特性，第一cout重载了操作符‘<<’,而cin重载了操作符‘>>’；第二，在使用时操作符的右边可以是整型，可以是字符串，cout会自动判断类型，体现了多态的特性。

C++中可以先定义方法原型，然后再写方法实现。
```cpp
// function.cc -- C++ function test
#include <iostream>
using namespace std;
void helloPrint(void); // 方法原型
int main()
{
  cout << "C++ function test!" << endl;
  helloPrint(); // invoke function
  return 0;
}
void helloPrint(void)
{
  cout << "This message is print by custom function!" << endl;
}
```

## Dealing with data
### integer
`C++`中的整型按照长度可以分为：char, short, int, long，而`C++`对于这4中类型又可以分signed和unsigned两类，其中signed是指包含负数范围的，而unsigned是指不包含负数，仅有正数的。`C++`中int不一定是4个字节的，它仅保证最少是和short一样长的，也就int有可能是2字节的。在C++中可以使用sizeof操作符来查看一个类型或者变量所占用的内存空间，这里的sizeof是一个操作符，类似于‘+’加号。
```cpp
// sizeof.cc -- use sizeof to find out type or var size
#include <iostream>
using namespace std;

int main()
{
  cout << "int sizeof: " << sizeof(int) << endl;
  int test = 9;
  cout << "int var test sizeof: " << sizeof test << endl;
  return 0;
}
```
C++中climit.h头文件中描述了整型的范围，例如最大值，最小值等，flimit.h头文件描述了浮点型的范围。
```cpp
// limits.cc -- some limit of integer
#include <iostream>
#include <climits>
using namespace std;

int main()
{
  cout << "int max: " << INT_MAX << endl;
  cout << "int min: " << INT_MIN << endl;
  cout << "bits per byte: " << CHAR_BIT << endl;
  return 0;
}
```
climits.h中的一些定义：
| Symbolic Constant | Represents                   |
| :---------------- | :--------------------------- |
| CHAR_BIT          | Number of bits in a char     |
| CHAR_MAX          | Maximum char value           |
| CHAR_MIN          | Minimum char value           |
| SCHAR_MAX         | Maximum signed char value    |
| SCHAR_MIN         | Minimum signed char value    |
| UCHAR_MAX         | Maximum unsigned char value  |
| SHRT_MAX          | Maximum short value          |
| SHRT_MIN          | Minimum short value          |
| USHRT_MAX         | Maximum unsigned short value |
| INT_MAX           | Maximum int value            |
| INT_MIN           | Minimum int value            |
| UINT_MAX          | Maximum unsigned int value   |
| LONG_MAX          | Maximum long value           |
| LONG_MIN          | Minimum long value           |
| ULONG_MAX         | Maximum unsigned long value  |

`C++`中的宏定义（#define INT_MAX 32767）最终会在预编译阶段把指定的字符替换为后面的字符，然后再编译源码。`C++`可以使用： test(16)来初始化变量，这是C语言中没有的。
C++可以使用10进制，8进制和16进制来初始化变量：
```cpp
#include <iostream>
using namespace std;

int main()
{
  int a = 42;   // 10
  int b = 042;  // 8
  int c = 0x42; // 16
  cout << "a=" << a << ", b=" << b << ", c=" << c << endl;
  cout << hex;  // change print as hex
  cout << "a hex=" << a << endl;
  cout << oct;  // change print as oct
  cout << "a oct=" << a << endl;
  cout << dec;  // back to decimal
  cout << "a dec=" << a << endl;
  return 0;
}
```

> **对于数字来说，10进制的数字C++中是按照 int, long, unsigned
long来识别的，也就是说在一个int是2个字节的系统上，20000被识别int，40000被识别为long，3000000000被识别为unsigned long,而对于16进制和8进制则是按照 int, unsigned int, long, unsigned long的顺序来识别的。当然可以显式声明数字的类型，在数字后面加上L代表它是long型，带上UL代表它是unsigned long型的。**

C++支持无符号类型，即存储的值是大于0的，例如：short范围为 –32,768 到 +32,767， 而unsigned shor范围是0 到 65,535.
```cpp
// unsigned.cc -- test unsigned types.

#include <iostream>
using namespace std;

int main(){
  cout << "This is a unsigned type test, unsigned int:" << 8u << endl;
  unsigned int num = 9u;
  u_int num2 = 10u;
  cout << "num is:" << num << ", num2 is:" << num2 << endl;
  unsigned num3 = 11u;  // also unsigned int
  cout << "num3 is:" << num3 << endl;
  return 0;
}
```

C++中的char是单字节的，只能容纳0-255这个范围的字符，对于中文，日语就无能为力了，因此就有了wchar_t这个int型的字符。
```cpp
// wchar.cc -- print Chinese in c++

#include <iostream>
using namespace std;

int main()
{
  wchar_t chinese = L'中'; // define Chinese char
  setlocale(LC_ALL, "");   // set locale, this is need for ubuntu may be.
  wcout << L"这是一个中文测试， chinese中的字符是：" << chinese << endl;  //  
  wcout << L"chinese的长度为：" << sizeof chinese << endl;
  return 0;
}
```
### bool
C++中新增了bool类型，bool可以和int做自动隐式转换
```cpp
// bool.cc -- test and print bool.

#include<iostream>
using namespace std;

int main(){
  bool isReady = true;
  int a = true;  // a assigned 1
  int b = false; // b assigned 0
  bool c = -9;   // c assigned true
  bool d = 0;    // d assigned false
  cout << boolalpha; // default cout will not display bool(display 1 for true, 0 for false)
  cout << "isReady:" << isReady << ", a:" << a << ", b:" << b << ", c:" << c << ", d:" << d << endl;
  return 0;
}

```

### float
`C++`中浮点型分为float, double和long double, 和integer一样，`C++`并不保证double肯定是64位的，仅保证double肯定不会比float少。但是一般情况下float是32位的，double是64位的，long double是80或96或128位的。`C++`中浮点型是以类似科学计数法的方式存储的，例如1400是以类似1.4E3的方式存储，0.00023是以类似2.3E-4的方式存储的，因此对于浮点型有效数字（例如1.4, 2.3）决定了浮点型所能描述的精度范围，下面是Borland C++中float的限制：
```cpp
// the following are the minimum number of significant digits
#define DBL_DIG 15 // double
#define FLT_DIG 6 // float
#define LDBL_DIG 18 // long double
// the following are the number of bits used to represent the mantissa
#define DBL_MANT_DIG 53
#define FLT_MANT_DIG 24
#define LDBL_MANT_DIG 64
// the following are the maximum and minimum exponent values
#define DBL_MAX_10_EXP +308
#define FLT_MAX_10_EXP +38
#define LDBL_MAX_10_EXP +4932
#define DBL_MIN_10_EXP -307
#define FLT_MIN_10_EXP -37
#define LDBL_MIN_10_EXP -4931
```
其中对于float来说它只能在右边小数位描述6位有效数字，也就是说对于12345678.0，科学计数法是：1.2345678E7,而float只能表示为1.234567E7,而如果我们把它打印出来就可以看到错误的结果了：
```cpp
// float.cc -- test use of float.

#include <iostream>
using namespace std;

int main(){
  // 1.23f, 1.23F -> float; 1.23e3, 1.23E3 -> double; 1.23L -> long double
  float test3 = 6E-9; 
  cout << "test3 is:" << test3 << endl;

  float test = 123456989.0;
  double test2 = 1234567890123456.2;
  cout.setf(ios_base::fixed, ios_base::floatfield);
  cout << "test is:" << test << endl;   // test is:123456992.000000
  cout << "test2 is:" << test2 << endl; // test2 is:1234567890123456.250000
  
  float a = 1.23E22;
  float b = a + 1235;
  cout << "a = " << a << endl; // a = 12300000492793395937280.000000
  cout << "b = " << b << endl; // a = 12300000492793395937280.000000
  cout << "b - a = " << b - a << endl; // b - a = 0.000000
  return 0;
}
```
> 上面使用`cout.setf(ios_base::fixed, ios_base::floatfield);`是为了让cout不以科学计数法的方式输出数字用的。

> 上面b和a输出一样的原因是：a以十进制方式写出来有23位，而float在科学计数法表示时只有6-7位数字是有效的，因此给a+1是不会对结果有任何影响的

### Type cast
C++中可以使用下面方式来进行强制类型转换
```cpp
// type_cast.cc -- test use of type cast.

#include <iostream>
using namespace std;

int main(){
  int a = (int) 4.5f; // C
  int b = int(3.4f);  // pure C++ way
  int c = static_cast<int>(9.6);
  cout << "a=" << a << ", b=" << b << ", c=" << c << ", 'A' is store as code:" << int('A') << endl;
  return 0;
}
```

## Compound types
### Arrays
C++中数组的声明是以下面的方式进行的：
```cpp
int months[12];  // create array of 12 int
```
这样声明的数组是没有初始化的，但是这个数组是已经分配了内存的，它里面的值是未定义的，这和内存里原来的值有关。C++数组使用{}的方式来初始化, 当{}中的长度不够数组长度时，其他元素将会自动被初始化为0：
```cpp
// array.cc -- use of array

#include <iostream>
using namespace std;

int main()
{
  int array[4] = {5, 6}; 
  int array2[] = {4, 3}; 
  cout << "array is:" << array << endl;
  cout << "array2 size is:" << sizeof array2 / sizeof(int) << endl;
  return 0;
}
```
### String
C++中字符串有两种方式存储（当然自己定义另说），一个是char数组，一个是string类。使用char数组时，字符串结尾必须是‘\0’，否则就不认为是字符串，例如下面的str2.
```cpp
// char_string.cc -- string using old char style

#include <iostream>
using namespace std;

int main()
{
  char str[] = {'T', 'h', 'i', 's', '\0'};    // \0 is use to mark end of string
  char str2[] = {'T', 'h', 'i', 's'};         // This is not a string
  char str3[] = "This";                       // str3 size will be 5, end with \0
  cout << "str is: " << str << ", str2 is:" << str2 << endl;
  cout << "str3 size:" << sizeof str3 / sizeof(char) << endl;
  cout << "String can "                       // split string by space/table/newline
  "break in new line, but print in one line." << endl;
  return 0;
}
```
C++在使用char来存储字符串，使用cin来输入字符串时，需要使用cin.getline来获取整行字符串，而不会被其他的空白符号（空格，tab）截断：
```cpp
// string_input.cc -- use cin to input string

#include <iostream>
#include <cstring> // for the strlen() function
using namespace std;

int main()
{
  char name[10];
  int age;
  cout << "Input your name:\n";
  cin.getline(name, 10);   // This will mark the enter key as input end, and ignore the '\n'
  cout << "Input your age:\n";
  cin >> age;
  cout << "Your name is: " << name << ", it's length is:" << strlen(name) << endl;
  cout << "Your age is:" << age << endl;
  return 0;
}
```
