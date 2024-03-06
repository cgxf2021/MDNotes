# CPP 笔记

[toc]

## CPP 基础知识

### Hello World

```cpp
// helloworld.cpp
#include <iostream>

int main() {
  std::cout << "Hello, World!" << std::endl;

  return 0;
}
```

### 名称空间

用来处理不同的代码段之间的名称冲突问题

```cpp
/* Example */

#include <iostream>

namespace mycode {
void foo() { std::cout << "foo() called in the mycode namespace" << std::endl; }
}  // namespace mycode

int main() {
  mycode::foo();

  return 0;
}

/*
foo() called in the mycode namespace
*/
```

### 数值极限

使用定义在`<limits>`中的类模板`std::numeric_limits`. 

```cpp
/* Example */

#include <iostream>
#include <limits>

using std::cout;
using std::endl;

int main() {
  cout << "Max int value: " << std::numeric_limits<int>::max() << endl;
  cout << "Min int value: " << std::numeric_limits<int>::min() << endl;
  cout << "Lowest int value: " << std::numeric_limits<int>::lowest() << endl;
  cout << "Max double value: " << std::numeric_limits<double>::max() << endl;
  cout << "Min double value: " << std::numeric_limits<double>::min() << endl;
  cout << "Lowest double value: " << std::numeric_limits<double>::lowest()
       << endl;

  return 0;
}

/*
Max int value: 2147483647
Min int value: -2147483648
Lowest int value: -2147483648
Max double value: 1.79769e+308
Min double value: 2.22507e-308
Lowest double value: -1.79769e+308
*/
```

### 位运算符

| 运算符 | 说明 | 用法 |
| ----- | ----- | ----- |
| `& / &=` | 按位与运算 | `i = j & k;` |
| `\| / \|=` | 按位或运算 | `i = j \| k;` |
| `<< / <<=` | 左移 | `i <<= 1;` |
| `>> / >>=` | 右移 | `i >>= 4;` |
| `^ / ^=` | 按位异或 | `i ^= j;` |

### 枚举类型

总是使用强类型`enum class`而不是类型不安全的旧式风格`enum`枚举. 

```cpp
/* Example */

enum class PieceType { King, Queen, Rook, Pawn };

PieceType piece1{PieceType::King};
```

### 结构体

```cpp
/* Example */

struct Employee {
  char firstInitial;
  char lastInitial;
  int employeeNumber;
  int salary;
};
```

### 条件语句

#### `if/else`

```cpp
/* Example */

if (int i = 0; i < 0) {
// <if body>
} else if (i == 0) {
// <else if body>
} else {
// <else body>
}
```

#### `switch`

```cpp
/* Example */

enum class Menu { Open, Save, Download, Load };
Menu menuItem = Menu::Open;

switch (menuItem) {
case Menu::Open:
  // open
  break;
case Menu::Save:
  // save
  break;
default:
  // other
  break;
}
```

### 三向比较运算(C++20)

三向比较运算符(`<=>`)可以用于确定两个值的大小顺序. 得到一个值是否等于、
小于、大于另一个值. 

### 函数

> **函数签名**: 指函数名与形参列表组合在一起, 但不包括返回类型. 

```cpp
/* Example */

#include <iostream>

int addNumbers(int num1, int num2) {
  std::cout << "Name of the Current Function: " << __func__ << std::endl;
  return num1 + num2;
}

int main() {
  int sum{addNumbers(5, 3)};

  std::cout << "sum = " << sum << std::endl;

  return 0;
}

/*
Name of the Current Function: addNumbers
sum = 8
*/
```

> **函数重载**: 函数名称相同, 但参数列表不同. (仅指定不同的返回类型是不够的, 
参数的数量/类型必须不同)

### 结构化绑定

```cpp
/* Example */

#include <array>
#include <iostream>

int main() {
  std::array values{11, 22, 33};
  // 必须为结构化绑定使用auto关键词
  auto [x, y, z]{values};

  std::cout << "x = " << x << " y = " << y << " z = " << z << std::endl;

  return 0;
}

/* x = 11 y = 22 z = 33 */
```

### 循环

C++提供了4种循环结构`for`循环, `while`循环, `do/while`循环, 基于范围的`for`循环. 

```cpp
/* Example */

#include <array>
#include <iostream>

using std::array;

int main() {
  array<int, 5> values{1, 2, 3, 4, 5};
  int i{0};

  std::cout << "for1: ";
  for (i = 0; i < values.size(); i++) {
    std::cout << values[i] << ' ';
  }
  std::cout << "\nwhile: ";
  i = 0;
  while (i < values.size()) {
    std::cout << values[i++] << ' ';
  }
  std::cout << "\ndo/while: ";
  i = 0;
  do {
    std::cout << values[i++] << ' ';
  } while (i < values.size());
  std::cout << "\nfor2: ";
  for (const auto &value : values) {
    std::cout << value << ' ';
  }
  std::cout << '\n';

  return 0;
}

/*
for1: 1 2 3 4 5 
while: 1 2 3 4 5 
do/while: 1 2 3 4 5 
for2: 1 2 3 4 5
*/
```

### 初始化列表

初始化列表在`<initializer_list>`头文件中定义, 
利用初始化列表可以编写能够接收可变参数的函数. 

```cpp
/* Example */

#include <initializer_list>
#include <iostream>

int makeSum(std::initializer_list<int> values) {
  int total{0};
  for (int value : values) {
    total += value;
  }
  return total;
}

int main() {
  int a{makeSum({1, 2, 3, 4, 5})};
  int b{makeSum({11, 22, 33, 44, 55})};

  std::cout << "a = " << a << std::endl;
  std::cout << "b = " << b << std::endl;

  return 0;
}

/*
a = 15
b = 165
*/
```

## CPP 面向对象

### 实现一个基本的类

#### 定义类

首先声明一个类名, 在大括号内声明类的数据成员(属性)和方法(行为). 
每个属性和行为都有特定的访问级别: `public`、`protected`、`private`. 

* `public`: 可以在类的外部访问. 
* `protected`: 可以在派生类中可以访问. 
* `private`: 只能在本类中访问. 

```cpp
/* airline_ticket.h */

class AirlineTicket {
 public:
  AirlineTicket();
  ~AirlineTicket();

  double calculatePriceInDollars();

  // const限定, 表示该行为不会修改对象中的成员数据
  const std::string &getPassengerName() const;
  void setPassengerName(const std::string &name);

  int getNumberOfMiles() const;
  void setNumberOfMiles(int miles);

  bool hasEliteSuperRewardsStatus() const;
  void setHasEliteSuperRewardsStatus(bool status);

 private:
  std::string m_passengerName;
  int m_numberOfMiles;
  bool m_hasEliteSuperRewardsStatus;
};
```

> 约定类数据成员都以`m_`开头. 

与类同名但没有返回类型的方法是构造函数, 当创建类的对象时会自动调用构造函数. 
`~`紧跟类名的方法是析构函数, 当销毁对象时会自动调用. 

#### 实现类

```cpp
/* airline_ticket.cpp */

#include "airline_ticket.h"

AirlineTicket::AirlineTicket()
    : m_passengerName{"Unknown Passenger"},
      m_numberOfMiles{0},
      m_hasEliteSuperRewardsStatus{false} {}

AirlineTicket::~AirlineTicket() {}

double AirlineTicket::calculatePriceInDollars() {
  if (hasEliteSuperRewardsStatus()) {
    return 0;
  }
  return getNumberOfMiles() * 0.1;
}

const std::string &AirlineTicket::getPassengerName() const {
  return m_passengerName;
}

void AirlineTicket::setPassengerName(const std::string &name) {
  m_passengerName = name;
}

int AirlineTicket::getNumberOfMiles() const { return m_numberOfMiles; }

void AirlineTicket::setNumberOfMiles(int miles) { m_numberOfMiles = miles; }

bool AirlineTicket::hasEliteSuperRewardsStatus() const {
  return m_hasEliteSuperRewardsStatus;
}

void AirlineTicket::setHasEliteSuperRewardsStatus(bool status) {
  m_hasEliteSuperRewardsStatus = status;
}
```

#### 使用类

```cpp
/* Example */

#include <iostream>

#include "airline_ticket.h"

using std::cout;
using std::endl;

int main() {
  AirlineTicket airlineTicket;
  airlineTicket.setPassengerName("smxrfx");
  airlineTicket.setNumberOfMiles(700);
  double cost{airlineTicket.calculatePriceInDollars()};
  cout << "This ticket will cost $" << cost << endl;

  return 0;
}

/*
This ticket will cost $70
*/
```

## CPP I/O

### std::cout

*[ostream](https://zh.cppreference.com/w/cpp/io/basic_ostream): 字符流上的高层输出操作*

| 方法 | 定义 |
| ----- | ----- |
| `operator<<` | 插入带格式数据(有格式输出) |
| `put` | 插入字符(无格式输出) |
| `write` | 插入字符块(无格式输出) |

```cpp
/* Example */

#include <cstring>
#include <iostream>

int main(void) {
  const double PI = 3.1415926;
  const bool b = true;
  std::cout << "PI: " << PI 
            << std::boolalpha << "\tb: " << b 
            << std::endl;

  char ch = 'c';
  std::cout.put(ch);
  std::cout.put('\n');

  char str[] = "Hello World";
  std::cout.write(str, strlen(str));
  std::cout.write("!\n", 2);

  return 0;
}

/*
PI: 3.14159	b: true
c
Hello World!
*/
```

**格式化输出**

*注: 主要是浮点数的显示*

```cpp
/* Example */

#include <iostream>

int main(void) {
  double f1 = 1.200;
  std::cout << f1 << std::endl;
  std::cout << (f1 + 1.0 / 9.0) << std::endl;

  double f2 = 1.67e2;
  std::cout << f2 << std::endl;
  f2 += 1.0 / 9.0;
  std::cout << f2 << std::endl;
  std::cout << (f2 + 1.0e4) << std::endl;

  double f3 = 2.3e-4;
  std::cout << f3 << std::endl;
  std::cout << f3 / 10 << std::endl;

  return 0;
}

/*
1.2
1.31111
167
167.111
10167.1
0.00023
2.3e-05
*/
```

* 浮点数类型最多显示6位数字
* 当指数大于等于6或小于等于-5时用科学计数法显示

| 方法 | 定义 |
| ----- | ----- |
| `flags` | 管理格式标志 |
| `setf` | 设置特定格式标志 |
| `unsetf` | 清除特定格式标志 |
| `precision` | 管理浮点操作的精度 |
| `width` | 管理域的宽度 |
| `fill` | 管理填充字符 |

**进制转换**

```cpp
/* Example */

#include <iostream>

int main(void) {
  const int num = 20;
  const double PI = 3.1415926;
  // 返回当前格式化设置
  std::ios_base::fmtflags flag = std::cout.flags();
  std::cout << num << std::endl;

  // 清除十进制且十六进制输出
  std::cout.setf(std::ios_base::hex, std::ios_base::basefield);
  // 设置前缀
  std::cout.setf(std::ios_base::showbase);
  std::cout << num << std::endl;
  // 清除特定的格式化设置
  std::cout.unsetf(std::ios_base::hex);
  std::cout << num << std::endl;

  // 设置浮点数精度
  int prec = std::cout.precision();
  std::cout << "Default Precision: " << prec << std::endl;
  // 12位
  std::cout.precision(12);
  std::cout << "PI: " << PI << std::endl;
  // 2位
  std::cout.precision(2);
  std::cout << "PI: " << PI << std::endl;
  // 恢复
  std::cout.flags(flag);
  // 恢复精度
  std::cout.precision(prec);
  std::cout << "PI: " << PI << std::endl;

  return 0;
}

/*
20
0x14
20
Default Precision: 6
PI: 3.1415926
PI: 3.1
PI: 3.14159
*/
```

**打印表格**

```cpp
/* Example */

#include <array>
#include <iostream>
#include <string>

typedef struct student {
  std::string name;
  int id;
  int score;
} Student;

void printLine(const int totalWidth);
void printItem(const Student &student, const std::array<int, 3> widths);
void printTitle(const std::array<const std::string, 3> title,
                const std::array<int, 3> widths);

int main(void) {
  std::array<const Student, 3> students{Student{"Joker", 1001, 298},
                                        Student{"Alex", 1002, 278},
                                        Student{"Gregoire", 1003, 291}};
  const std::array<const std::string, 3> title{"name", "id", "score"};
  const std::array<int, 3> widths{15, 10, 10};
  const int totalWidth = widths.at(0) + widths.at(1) + widths.at(2);
  std::cout.setf(std::ios::left, std::ios::adjustfield);
  std::cout.precision(2);
  printLine(totalWidth);
  printTitle(title, widths);
  for (int i{0}; i < 3; i++) {
    printLine(totalWidth);
    printItem(students.at(i), widths);
  }
  printLine(totalWidth);
  return 0;
}

void printLine(const int totalWidth) {
  std::cout.width(totalWidth);
  std::cout.fill('-');
  std::cout << '-' << std::endl;
  std::cout.fill(' ');

  return;
}

void printItem(const Student &student, const std::array<int, 3> widths) {
  std::cout.width(widths.at(0));
  std::cout << ("| " + student.name) << '|';
  std::cout.width(widths.at(1) - 1);
  std::cout << (' ' + std::to_string(student.id)) << '|';
  std::cout.width(widths.at(2) - 2);
  std::cout << (' ' + std::to_string(student.score)) << '|';
  std::cout << std::endl;

  return;
}

void printTitle(const std::array<const std::string, 3> title,
                const std::array<int, 3> widths) {
  std::cout.width(widths.at(0));
  std::cout << ("| " + title.at(0)) << '|';
  std::cout.width(widths.at(1) - 1);
  std::cout << (' ' + title.at(1)) << '|';
  std::cout.width(widths.at(2) - 2);
  std::cout << (' ' + title.at(2)) << '|';
  std::cout << std::endl;

  return;
}

/*
-----------------------------------
| name         | id      | score  |
-----------------------------------
| Joker        | 1001    | 298    |
-----------------------------------
| Alex         | 1002    | 278    |
-----------------------------------
| Gregoire     | 1003    | 291    |
-----------------------------------
*/
```

**格式化浮点数**

```cpp
/* Example */

#include <iostream>

typedef std::ios_base::fmtflags format;
typedef std::streamsize precis;
format setFormat(void);
void restore(format f, precis p);

int main(void) {
  double num = 12.567890;
  std::cout << "num: " << num << std::endl;
  format flag = setFormat();
  precis prec = std::cout.precision(3);
  std::cout << "num: " << num << std::endl;
  restore(flag, prec);
  std::cout << "num: " << num << std::endl;

  return 0;
}

// 启用小数点形式, 返回原来的格式标志
format setFormat(void) {
  return std::cout.setf(std::ios_base::fixed, std::ios_base::floatfield);
}

// 恢复原来的格式标志
void restore(format f, precis p) {
  std::cout.flags(f);
  std::cout.precision(p);

  return;
}

/*
num: 12.5679
num: 12.568
num: 12.5679
*/
```

### std::cin

*[istream](https://zh.cppreference.com/w/cpp/io/basic_istream): 字符流上的高层输入操作*

| 方法 | 定义 |
| ----- | ----- |
| `operator>>` | 提取带格式数据 |
| `get` | 提取字符 |
| `peek` | 读取下一个字符, 但不会提取它 |
| `unget` | 撤销上一个字符提取 |
| `putback` | 往输入流中放置一个字符 |
| `getline` | 持续提取字符, 直到找到给定字符 |
| `ignore` | 持续提取并丢弃字符, 直到找到给定字符 |
| `read` | 提取字符块 |
| `readsome` | 提取可用的若干块字符 |
| `gcount` | 返回上次无格式输入操作提取的字符数量 |

```cpp
/* Example */

#include <iostream>
#include <limits>

int main(void) {
  // >> 输入
  int num1{0};
  std::cout << "Enter a number: ";
  std::cin >> num1;
  std::cout << "num1 = " << num1 << std::endl;
  std::cin.get();
  // get
  char str1[20]{};
  std::cout << "Enter a string: ";
  /* 提取12个字符(get/read区别) */
  std::cin.get(str1, 13);
  std::cout << "str1 = " << str1 << ", gcount = " << std::cin.gcount() << std::endl;
  std::cin.get();
  // peek
  char c1{'\0'};
  char c2{'\0'};
  std::cout << "Enter a char: ";
  c1 = std::cin.peek();
  std::cout << "c1 = " << c1 << std::endl;
  c2 = std::cin.get();
  std::cout << "c2 = " << c2 << std::endl;
  // unget
  if (std::cin.unget()) {
    char c3 = std::cin.get();
    std::cout << "c3 = " << c3 << std::endl;
  }
  // putback
  std::cin.get();
  std::cin.putback('N');
  char c4 = std::cin.get();
  std::cout << "c4 = " << c4 << std::endl;
  // getline
  char str2[20]{};
  std::cout << "Enter a string(end @): ";
  std::cin.getline(str2, 20, '@');
  std::cout << "str2 = " << str2 << std::endl;
  // ignore
  constexpr auto max_size = std::numeric_limits<std::streamsize>::max();
  std::cout << "stdin: " << (char) std::cin.peek() << std::endl;
  /* 清除缓冲区中的输入直到换行符 */
  std::cin.ignore(max_size, '\n');
  // read
  char str3[20]{};
  std::cout << "Enter a string: ";
  /* 提取9个字符(get/read区别) */
  std::cin.read(str3, 9);
  std::cout << "str3 = " << str3 << std::endl;

  return 0;
}

/*
Enter a number: 12
num1 = 12
Enter a string: Hello World!
str1 = Hello World!, gcount = 12
Enter a char: p
c1 = p
c2 = p
c3 = p
c4 = N
Enter a string(end @): smxrfx@qq.com
str2 = smxrfx
stdin: q
Enter a string: best dota
str3 = best dota
*/
```

### 文件输入/输出

* *[ofstream](https://zh.cppreference.com/w/cpp/io/basic_ofstream): 实现文件上基于流的高层输出操作*
* *[ifstream](https://zh.cppreference.com/w/cpp/io/basic_ifstream): 实现文件流上的高层输入操作*

**文件操作**

| 方法 | 定义 |
| ----- | ----- |
| `open` | 打开文件, 并与流关联 |
| `is_open` | 检查流是否有关联文件 |
| `close` | 关闭关联文件 |
| `construction` | 通过构造方法关联文件 |

**写入文件操作**

| 方法 | 定义 |
| ----- | ----- |
| `operator<<` | 插入带格式数据 |
| `put` | 插入字符 |
| `write` | 插入字符块 |

```cpp
/* Example */

#include <fstream>

int main(int argc, char *argv[]) {
  const char *filename = "test.txt";
  // 通过构造方法关联文件
  // 追加写入文件test.txt
  std::ofstream file1(filename, std::ios::out);
  if (file1.is_open()) {
    file1 << "hello world";
    file1.put('\n');
    file1.close();
  }
  // 通过open方法关联文件
  std::ofstream file2;
  file2.open(filename, std::ios::out | std::ios::app);
  if (file2.is_open()) {
    file2.write("HELLO WORLD", 11);
    file2.put('\n');
    file2.close();
  }

  return 0;
}

/*
test.txt

hello world
HELLO WORLD
*/
```

**状态函数**

| 方法 | 定义 |
| ----- | ----- |
| `good` | 检查是否没有发生错误, 例如是否可执行输入/输出操作 |
| `eof` | 检查是否到达了文件末尾 |
| `fail` | 检查是否发生了可恢复的错误 |
| `bad` | 检查是否已发生了不可恢复的错误 |
| `operator!` | 检查是否有错误发生(同`fail`) |
| `operator bool` | 检查是否有错误发生(同`fail`) |
| `rdstate` | 返回状态标志 |
| `setstate` | 设置状态标志 |
| `clear` | 修改状态标志 |

*注: fail/good/!都可用于判断文件是否成功打开, 但使用is_open会更好*

**读出文件操作**

同**istream**中的读入操作

```cpp
/* Example */

#include <cstring>
#include <fstream>
#include <iostream>

int main(int argc, char *argv[]) {
  const char *filename = argv[1];
  const unsigned int length = 128;

  // 通过构造方法关联文件
  std::ifstream file(filename, std::ios::in);
  char *str = new char[length + 1]{};
  if (file.is_open()) {
    // getline 读取一行
    file.getline(str, length);
    std::cout << "getline: " << std::endl;
    std::cout << str << std::endl;
    int len = std::strlen(str);
    // >> 读取一个字符串(直到空白字符)
    file >> &str[len];
    std::cout << ">>: " << std::endl;
    std::cout << str << std::endl;
    len = std::strlen(str);
    // read 读取length长度的字符串
    file.read(&str[len], length);
    std::cout << "read: " << std::endl;
    std::cout << str << std::endl;
    file.close();
  }

  // 读取一整个文件
  std::cout << ">> 使用eof()读取一整个文件" << std::endl;
  std::memset(str, '\0', length + 1);
  const unsigned int buffer = 10;
  char *pstr = str;
  file.open(filename, std::ios::in);
  if (file.is_open()) {
    while (!file.eof()) {
      file.read(pstr, buffer);
      pstr += buffer;
    }
    std::cout << str << std::endl;
    file.close();
  }

  return 0;
}

/*
test.txt
hello world
HELLO WORLD
Hello CMake
Chinese Dota Great Again!

getline: 
hello world
>>: 
hello worldHELLO
read: 
hello worldHELLO WORLD
Hello CMake
Chinese Dota Great Again!

>> 使用eof()读取一整个文件
hello world
HELLO WORLD
Hello CMake
Chinese Dota Great Again!
*/
```

## CPP 设计模式

### 单例模式

保证在系统中只存在一个实例, 从而保证逻辑正确性以及良好的效率.

单例实现的两种方式:

* 懒汉: 第一次用到类实例的时候才会去实例化.

*单线程版本*

```cpp
/* Example */

/* singleton.hpp */
#ifndef SINGLETON_HPP_
#define SINGLETON_HPP_

class Singleton {
 private:
  Singleton();
  static Singleton *m_singleton;

 public:
  static Singleton *getInstance();
};

#endif  // !SINGLETON_HPP_

/* singleton.cpp */
#include "singleton.hpp"

#include <iostream>

Singleton::Singleton() { std::cout << "create singleton" << std::endl; }

Singleton *Singleton::m_singleton = nullptr;
Singleton *Singleton::getInstance() {
  if (m_singleton == nullptr) {
    m_singleton = new Singleton();
  }

  return m_singleton;
}
```

> 问题在于不能用在多线程的情况.例如 Thread A 进入if但没有执行new, 
此时 Thread B 进入if, 从而会创建多个对象.

*枷锁版本*

```cpp
/* Example */

/* singleton.hpp */
#ifndef SINGLETON_HPP_
#define SINGLETON_HPP_

#include <mutex>

class Singleton {
 private:
  Singleton();
  static Singleton *m_singleton;
  static std::mutex m_lock;

 public:
  static Singleton *getInstance();
};

#endif  // !SINGLETON_HPP_

/* singleton.cpp */
#include "singleton.hpp"

#include <iostream>

Singleton::Singleton() { std::cout << "create singleton" << std::endl; }

Singleton *Singleton::m_singleton = nullptr;

std::mutex Singleton::m_lock;

Singleton *Singleton::getInstance() {
  // 加锁
  std::lock_guard<std::mutex> guard(m_lock);
  if (m_singleton == nullptr) {
    m_singleton = new Singleton();
  }

  return m_singleton;
}
```

> 加锁可以保证一个线程在调用`getInstance`方法时, 其他线程调用`getInstance`方法
时会被阻塞在锁处, 从而保证只实例化了一次, 但是锁的开销是很大的, 并且当对象实例化
完成后, 进行的都是'读'操作, 而读操作不需要加锁, 故存在性能问题.

*双重检查锁版本*

```cpp
/* Example */

Singleton *Singleton::getInstance() {
  if (m_singleton == nullptr) {
    // 加锁
    std::lock_guard<std::mutex> guard(m_lock);
    if (m_singleton == nullptr) {
      m_singleton = new Singleton();
    }
  }

  return m_singleton;
}
```

**漏洞: 内存读写乱序执行(`reorder`)**

> 例如,在执行`m_singleton = new Singleton();`
时, 编译器有三个过程: 1. 分配内存、2. 调用构造函数、3. 让指针指向该地址; 但是, 
实际编译器并不一定按照1->2->3进行下去, 而可能进行换序操作, 如按照1->3->2进行. 
于是, 当Thread A执行到`m_singleton = new Singleton();`时, 可能`m_singleton`
已经不为空, 但并未调用构造函数进行初始化, 而此时Thread B到达第一个if, 得知非空, 
拿到了`m_singleton`.

*解决方案(基于operator new+placement new)*

```cpp
/* Example */

Singleton *Singleton::getInstance() {
  if (m_singleton == nullptr) {
    // 加锁
    std::lock_guard<std::mutex> guard(m_lock);
    if (m_singleton == nullptr) {
      // 1. 分配内存
      Singleton *tmp =
          static_cast<Singleton *>(operator new(sizeof(Singleton)));
      // 2. 调用构造方法
      new (tmp) Singleton();
      // 3. 指向该内存
      m_singleton = tmp;
    }
  }

  return m_singleton;
}
```

*C++11 的懒汉式单例模式*

```cpp
/* Example */

#ifndef SINGLETON_HPP_
#define SINGLETON_HPP_

class Singleton {
 private:
  Singleton() = default;
  ~Singleton() = default;
  Singleton(const Singleton &) = delete;
  Singleton & operator=(const Singleton &) = delete;

 public:
  static Singleton *getInstance() {
    static Singleton instance;
    return &instance;
  }
};

#endif  // !SINGLETON_HPP_
```

> 新的C++标准规定了当一个线程正在初始化一个变量的时候, 
其他线程必须得等到该初始化完成以后才能访问它, 因此是线程安全的.

* 饿汉: 单例类定义的时候就进行了实例化.

```cpp
/* Example */

/* singleton.hpp */
#ifndef SINGLETON_HPP_
#define SINGLETON_HPP_

class Singleton {
 private:
  Singleton();
  static Singleton *m_singleton;

 public:
  static Singleton *getInstance();
};

#endif  // !SINGLETON_HPP_

/* singleton.cpp */
#include "singleton.hpp"

#include <iostream>

Singleton::Singleton() { std::cout << "create singleton" << std::endl; }

Singleton *Singleton::m_singleton = new Singleton();

Singleton *Singleton::getInstance() { return m_singleton; }
```

## CPP 异常

## CPP 模板

## CPP STL

| 容器 | 定义 |
| ----- | ----- |
| [`array`](#array) | 静态的连续数组 |
| [`vector`](#vector) | 动态的连续数组 |
| [`forward_list`](#forward_list) | 单链表 |
| [`list`](#list) | 双链表 |
| [`deque`](#deque) | 双端队列 |
| [`set`](#set) | 唯一健的集合, 按照键排序 |
| [`map`](#map) | 键值对的集合, 按照键排序, 键是唯一的 |
| `multiset` | 键的集合, 按照键排序 |
| `multimap` | 键值对的集合, 按照键排序 |

| 适配器 | 定义 |
| ----- | ----- |
| [`stack`](#stack) | 适配一个容器以提供栈 |
| [`queue`](#queue) | 适配一个容器以提供队列 |
| [`priority_queue`](#priority_queue) | 适配一个容器以提供优先级队列 |

**容器的元素访问**

| 方法 | 定义 |
| ----- | ----- |
| `at` | 访问指定的元素, 同时进行越界检查 |
| `operator[]` | 访问指定的元素 |
| `front` | 访问第一个元素 |
| `back` | 访问最后一个元素 |


**容器的迭代器**

| 方法 | 定义 |
| ----- | ----- |
| `begin` | 返回指向起始的迭代器 |
| `end` | 返回指向末尾的迭代器 |
| `rbegin` | 返回指向起始的逆向迭代器 |
| `rend` | 返回指向末尾的逆向迭代器 |

**容器的容量**

| 方法 | 定义 |
| ----- | ----- |
| `empty` | 检查容器是否为空 |
| `size` | 返回容纳的元素数 |
| `max_size` | 返回可容纳的最大元素数 |

**容器的修改器**

| 方法 | 定义 |
| ----- | ----- |
| `clear` | 清除内容 |
| `insert` | 插入元素 |
| `erase` | 擦除元素 |
| `push_back` | 将元素添加到容器末尾 |
| `pop_back` | 移除末元素 |
| `resize` | 改变容器中可存储元素的个数 |

*注: 列举了大多数容器都有的常用操作, 并非所有容器都可以调用上述方法*

*[STL容器](https://zh.cppreference.com/w/cpp/container): CPP Container*

### array(C++11)

```cpp
/* Example */

#include <array>
#include <iostream>

int main(void) {
  // 声明array
  std::array<int, 5> nums;
  nums.fill(12);
  // 遍历数组
  for (int i = 0; i < 5; i++) {
    std::cout << nums.at(i) << ' ';
  }
  std::cout.put('\n');
  // 使用迭代器
  for (std::array<int, 5>::iterator it = nums.begin(); it != nums.end(); it++) {
    std::cout << *it << ' ';
  }
  std::cout.put('\n');
  // foreach遍历
  for (auto &n : nums) {
    std::cout << n << ' ';
  }
  std::cout.put('\n');

  return 0;
}

/*
12 12 12 12 12 
12 12 12 12 12 
12 12 12 12 12
*/
```

### vector

```cpp
/* Example */

#include <iostream>
#include <vector>

void printVector(const std::vector<int> &v);

int main(void) {
  // 无参构造
  std::vector<int> v1;
  printVector(v1);
  // 指定大小和值
  std::vector<int> v2(3, 100);
  printVector(v2);
  // 指定大小和0填充
  std::vector<int> v3(10);
  printVector(v3);
  // reserve分配空间
  std::vector<int> v4;
  v4.reserve(10);
  printVector(v4);
}

void printVector(const std::vector<int> &v) {
  std::cout << "size: " << v.size() << std::endl;
  std::cout << "capacity: " << v.capacity() << std::endl;
  for (const int &i : v) {
    std::cout << i << ' ';
  }
  std::cout << std::endl;
}

/*
size: 0
capacity: 0

size: 3
capacity: 3
100 100 100 
size: 10
capacity: 10
0 0 0 0 0 0 0 0 0 0 
size: 0
capacity: 10

*/
```

**emplace_back()和emplace()**

*在使用push_back()和insert()增加对象时, 以push_back()为例:*

1. 找到一块足够放vector的新内存空间.
2. 调用类对应的构造方法生成一个临时对象.
3. 调用移动构造函数将临时对象的值移动给vector中的对象.
4. 调用拷贝构造函数将原来vector中的对象拷贝至新的内存空间中.

*而emplace_back()和emplace()表示原位插入, 也就是不需要创建临时变量, 
直接在新内存中创建对象, 再进行上述[4]操作, 即少了一次移动构造函数的调用.* 

```cpp
/* Example */

/* user.hpp */
#ifndef USER_HPP_
#define USER_HPP_

#include <algorithm>
#include <iostream>
#include <string>

class User {
 private:
  int m_id;
  std::string m_name;

 public:
  User() { std::cout << "无参构造" << std::endl; }
  User(int id, const std::string &name) : m_id(id), m_name(name) {
    std::cout << "id = " << m_id << " 带参构造" << std::endl;
  }
  User(const User &other) : m_id(other.m_id), m_name(other.m_name) {
    std::cout << "id = " << m_id << " 拷贝构造" << std::endl;
  }
  User(const User &&other)
      : m_id(std::move(other.m_id)), m_name(std::move(other.m_name)) {
    std::cout << "id = " << m_id << " 移动构造" << std::endl;
  }
  ~User() {}
  User &operator=(const User &other) {
    m_id = other.m_id;
    m_name = other.m_name;
    std::cout << "id = " << m_id << " 对象被赋值" << std::endl;

    return *this;
  }

  User &operator=(const User &&other) {
    m_id = std::move(other.m_id);
    m_name = std::move(other.m_name);
    std::cout << "id = " << m_id << " 对象被移动赋值" << std::endl;

    return *this;
  }
};

#endif  // !USER_HPP_

/* main.cpp */
#include <iostream>
#include <vector>

#include "user.hpp"

int main(void) {
  std::vector<User> vec;
  vec.push_back(User{1, "shadow"});
  std::cout << std::endl;
  vec.push_back(User{2, "Maybe"});
  std::cout << std::endl;
  vec.emplace_back(3, "Lou");
  std::cout << std::endl;
  vec.emplace(vec.begin() + 1, 4, "fy");

  return 0;
}

/*
id = 1 带参构造
id = 1 移动构造

id = 2 带参构造
id = 2 移动构造
id = 1 拷贝构造

id = 3 带参构造
id = 1 拷贝构造
id = 2 拷贝构造

id = 4 带参构造
id = 3 移动构造
id = 2 对象被移动赋值
id = 4 对象被移动赋值
*/
```

**erase(): 从容器擦除指定的元素**

1. 参数: **pos - 指向要移除的元素的迭代器** | **first, last - 要移除的元素范围**;
2. 返回值: 移除元素之后的迭代器, 如果是最后一个元素, 则为end;

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <vector>

using std::cout;
using std::endl;

int main() {
  std::vector<int> nums{0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
  auto output = [](int n) -> void { cout << n << ' '; };

  std::for_each(nums.begin(), nums.end(), output);
  cout << endl;
  // remove odd
  for (std::vector<int>::iterator it = nums.begin(); it != nums.end();) {
    if (*it % 2 == 1) {
      it = nums.erase(it);
    } else {
      it++;
    }
  }
  std::for_each(nums.begin(), nums.end(), output);
  cout << endl;

  return 0;
}

/*
0 1 2 3 4 5 6 7 8 9 
0 2 4 6 8 
*/
```

### forward_list

*单向链表的迭代器只能进行自增操作*

```cpp
/* Example */

#include <forward_list>
#include <functional>
#include <iostream>

void printList(const std::forward_list<int> &fs);

int main(void) {
  std::forward_list<int> fs1{1, 2, 3, 4, 5};
  std::forward_list<int> fs2{6, 7, 8, 9, 10};
  std::forward_list<int> fs3{10, -8, 2, 15, 13, 0};

  // 返回指向第一个元素之前迭代器
  std::forward_list<int>::iterator it = fs1.before_begin();
  std::cout << "first element: " << *(++it) << std::endl;
  // 删除节点
  fs1.erase_after(it);
  std::cout << "fs1: " << ' ';
  printList(fs1);
  std::cout << "fs2: " << ' ';
  printList(fs2);
  // 将fs2加入fs1
  fs1.splice_after(it, fs2);
  std::cout << "fs1: " << ' ';
  printList(fs1);
  std::cout << "fs2: " << ' ';
  printList(fs2);
  // 移除指定的数
  fs1.remove(8);
  std::cout << "fs1: " << ' ';
  printList(fs1);
  // 移除 < 4 的数
  fs1.remove_if([](const int &n) -> bool { return n < 4; });
  std::cout << "fs1: " << ' ';
  printList(fs1);
  std::cout << "fs3: " << ' ';
  printList(fs3);
  // 排序(递增)
  fs3.sort([](const int &a, const int &b) -> bool { return a < b; });
  std::cout << "fs3: " << ' ';
  printList(fs3);
  // 排序(递减)..仿函数
  fs3.sort(std::greater<int>());
  std::cout << "fs3: " << ' ';
  printList(fs3);

  return 0;
}

void printList(const std::forward_list<int> &fs) {
  for (const auto &f : fs) {
    std::cout << f << ' ';
  }
  std::cout.put('\n');
}

/*
first element: 1
fs1:  1 3 4 5 
fs2:  6 7 8 9 10 
fs1:  1 6 7 8 9 10 3 4 5 
fs2:  
fs1:  1 6 7 9 10 3 4 5 
fs1:  6 7 9 10 4 5 
fs3:  10 -8 2 15 13 0 
fs3:  -8 0 2 10 13 15 
fs3:  15 13 10 2 0 -8 
*/
```

### list

*双向链表的迭代器可以进行自增/自减操作, 使用方法与`forword_list`大致相同*

### stack

*栈是一种后进先出的数据结构*

```cpp
/* Example */

#include <iostream>
#include <stack>

int main(void) {
  std::stack<int> iStack;
  // 入栈
  iStack.push(1);
  iStack.push(4);
  iStack.push(8);
  iStack.push(9);

  int num{0};
  // 将栈中所有元素出栈
  while (!iStack.empty()) {
    // 取顶部元素
    num = iStack.top();
    // 删除顶部元素
    iStack.pop();
    std::cout << num << " has been popped. Stack Size: " << iStack.size()
              << std::endl;
  }

  return 0;
}

/*
9 has been popped. Stack Size: 3
8 has been popped. Stack Size: 2
4 has been popped. Stack Size: 1
1 has been popped. Stack Size: 0
*/
```

### queue

*队列是一种先进先出的数据结构*

```cpp
/* Example */

#include <iostream>
#include <queue>

int main(void) {
  std::queue<const char *> strQueue;

  // 入队
  strQueue.push("smxr");
  strQueue.push("zjy");
  strQueue.push("zly");
  strQueue.push("xs");
  strQueue.push("dfyq");

  // 队头
  std::cout << "Front: " << strQueue.front() << std::endl;
  // 队尾
  std::cout << "Back: " << strQueue.back() << std::endl;
  // 出队
  while (!strQueue.empty()) {
    const char *s = strQueue.front();
    strQueue.pop();
    std::cout << s << " has been popped. Queue Size: " << strQueue.size()
              << std::endl;
  }

  return 0;
}

/*
Front: smxr
Back: dfyq
smxr has been popped. Queue Size: 4
zjy has been popped. Queue Size: 3
zly has been popped. Queue Size: 2
xs has been popped. Queue Size: 1
dfyq has been popped. Queue Size: 0
*/
```

### deque

*双端队列, 可以在两端进行读取和插入, 用法与`vector`基本一样*

### priority_queue

*优先队列, 默认比较大小, 大在前小在后*

```cpp
/* Example */

#include <iostream>
#include <queue>
#include <vector>

int main(void) {
  // 递增优先队列
  std::priority_queue<int, std::vector<int>, std::greater<int>> pQueue;
  pQueue.push(8);
  pQueue.push(37);
  pQueue.push(19);
  pQueue.push(75);

  // 出队
  while (!pQueue.empty()) {
    auto top = pQueue.top();
    pQueue.pop();
    std::cout << top << ' ';
  }
  std::cout << std::endl;

  return 0;
}

/*
8 19 37 75 
*/
```

*自定义类的优先队列*

```cpp
/* Example */

/* person.hpp */
#ifndef PERSON_HPP_
#define PERSON_HPP_

#include <iostream>
#include <string>

class Person {
 private:
  std::string m_name;
  int m_age;

 public:
  Person(const std::string &name = "null", int age = 0)
      : m_name(name), m_age(age) {}
  ~Person() {}
  int getAge() const { return m_age; }
  bool operator<(const Person &other) const { return m_age < other.m_age; }
  bool operator>(const Person &other) const { return m_age > other.m_age; }
  friend std::ostream &operator<<(std::ostream &out, const Person &object) {
    out << "Name: " << object.m_name << " Age: " << object.m_age;
    return out;
  }
};

#endif  // !PERSON_HPP_

/* main.cpp */
#include <iostream>
#include <queue>

#include "person.hpp"

int main(void) {
  // 递增优先队列
  std::priority_queue<Person, std::vector<Person>, std::greater<Person>> pQueue;
  pQueue.emplace("Jerry", 18);
  pQueue.emplace("Tom", 28);
  pQueue.emplace("Alex", 15);
  pQueue.emplace("Lee", 33);

  // 出队
  while (!pQueue.empty()) {
    auto top = pQueue.top();
    pQueue.pop();
    std::cout << top << std::endl;
  }

  return 0;
}

/*
Name: Alex Age: 15
Name: Jerry Age: 18
Name: Tom Age: 28
Name: Lee Age: 33
*/
```

### set

*根据键进行排序*

```cpp
/* Example */

#include <iostream>
#include <set>
#include <vector>

int main(void) {
  std::vector<int> v{5, 5, 2, 2, 1, 1, 3, 3, 4, 4};
  std::set<int> s1{1, 3, 4, 3, 7, 9};
  std::set<int> s2(v.begin(), v.end());

  // merge(C++17)
  s1.merge(s2);
  // print
  for (std::set<int>::iterator it = s1.begin(); it != s1.end(); it++) {
    std::cout << *it << ' ';
  }
  std::cout.put('\n');
  for (std::set<int>::iterator it = s2.begin(); it != s2.end(); it++) {
    std::cout << *it << ' ';
  }
  std::cout.put('\n');

  return 0;
}

/*
1 2 3 4 5 7 9 
1 3 4 
*/
```

### map

*根据键进行排序*

```cpp
/* Example */

#include <iostream>
#include <map>
#include <string>

int main(void) {
  // pair的用法
  std::pair<std::string, int> dir;
  dir.first = "AR";
  dir.second = 4;
  std::cout << "Key: " << dir.first << " Value: " << dir.second << std::endl;
  std::cout << std::endl;
  // map的用法
  std::map<std::string, int> mdir{{"TS", 1}, {"GG", 2}, {"LGD", 3}};
  // 插入
  mdir.insert(dir);
  for (auto &m : mdir) {
    std::cout << "Key: " << m.first << " Value: " << m.second << std::endl;
  }

  return 0;
}

/*
Key: AR Value: 4

Key: AR Value: 4
Key: GG Value: 2
Key: LGD Value: 3
Key: TS Value: 1
*/
```

### string

```cpp
/* Example */

#include <iostream>
#include <string>

int main(void) {
  std::string str1 = "Hello String";
  std::string str2(10, 's');

  std::cout << "str1: " << str1 << std::endl;
  std::cout << "str2: " << str2 << std::endl;
  // 字符串拼接
  std::string str3 = str1 + str2;
  std::cout << "str3: " << str3 << std::endl;
  // 元素访问
  std::cout << "str3[5]: " << str3[5] << std::endl;
  std::cout << "str3.at(5): " << str3.at(5) << std::endl;
  std::cout << "str3.front(): " << str3.front() << std::endl;
  std::cout << "str3.back(): " << str3.back() << std::endl;
  // 判空
  std::cout << "str3 is empty: " << std::boolalpha << str3.empty() << std::endl;
  // 返回字节数 != 字符数
  std::cout << "str3 size: " << str3.size() << std::endl;
  // 迭代器
  std::string::iterator it = str3.begin();
  it++;
  std::cout << "iterator[1]: " << *it << std::endl;

  return 0;
}

/*
str1: Hello String
str2: ssssssssss
str3: Hello Stringssssssssss
str3[5]:  
str3.at(5):  
str3.front(): H
str3.back(): s
str3 is empty: false
str3 size: 22
iterator[1]: e
*/
```

*字符串比较*

```cpp
/* Example */

#include <iostream>
#include <string>

int main(void) {
  std::string str1 = "Hello String";
  std::string str2 = "hello string";

  // ==
  std::cout << std::boolalpha << "str1 == str2: " << (str1 == str2)
            << std::endl;
  // compare
  std::cout << std::boolalpha << "str1 compare str2: " << (str1.compare(str2))
            << std::endl;
  // 追加字符
  str1.push_back('s');
  std::cout << "str1 push_back: " << str1 << std::endl;
  // 追加内容
  str2.append(" and STL");
  std::cout << "str2 append: " << str2 << std::endl;
  // 插入
  str2.insert(5, " std");
  std::cout << "str2 insert: " << str2 << std::endl;
  // 清除所有
  str1.clear();
  std::cout << "str1 clear: " << str1 << std::endl;
  // 清除指定字符
  str2.erase(str2.begin());
  std::cout << "str2 erase begin: " << str2 << std::endl;

  return 0;
}

/*
str1 == str2: false
str1 compare str2: -32
str1 push_back: Hello Strings
str2 append: hello string and STL
str2 insert: hello std string and STL
str1 clear: 
str2 erase begin: ello std string and STL
*/
```

*字符串替换、子串、查找*

```cpp
/* Example */

#include <iostream>
#include <string>

int main(void) {
  std::string str1 = "Hello String";
  std::string str2 = "good night";

  // replace
  str1.replace(2, 6, str2);
  std::cout << "str1.replace(2, 6, str2): " << str1 << std::endl;
  // 提取子串
  std::cout << "substr(5, 3): " << str1.substr(5, 3) << std::endl;
  // 查找
  std::cout << "str1.find(\"good\"): " << str1.find("good") << std::endl;
  int index = str2.find("get");
  if (index == std::string::npos) {
    std::cout << "can not find \"get\" in str2." << std::endl;
  } else {
    std::cout << "index: " << index << std::endl;
  }
  // to string
  int num = 114;
  std::cout << "to string: " << (str2 + std::to_string(num)) << std::endl;
  // string to number
  std::string str3 = "514";
  std::cout << "string to number: " << std::stoi(str3) << std::endl;
  std::string str4 = "114.514";
  std::cout << "string to number: " << std::stod(str4) << std::endl;

  return 0;
}

/*
str1.replace(2, 6, str2): Hegood nightring
substr(5, 3): d n
str1.find("good"): 2
can not find "get" in str2.
to string: good night114
string to number: 514
string to number: 114.514
*/
```

### string_view

`string_view`的用法和`string`差不多. 
`string_view`在复制字符串时与原字符串共享同一块内存. 

```cpp
/* Example */

#include <iostream>
#include <string>
#include <string_view>

int main(void) {
  const char *str1 = "hello string view";
  std::string str2(str1);
  std::string_view sv = str1;

  std::cout << "address str1: " << (uintptr_t)str1 << std::endl;
  std::cout << "address str2: " << (uintptr_t)str2.data() << std::endl;
  std::cout << "address str3: " << (uintptr_t)sv.data() << std::endl;

  const char *str4 = "犯大吴疆土者";
  std::string_view sv2 = str4;
  // remove_prefix
  sv2.remove_prefix(2);
  std::cout << "sv1: " << sv2 << std::endl;
  // remove_suffix
  sv2.remove_suffix(4);
  std::cout << "sv1: " << sv2 << std::endl;

  return 0;
}

/*
address str1: 94352231383048
address str2: 94352261852848
address str3: 94352231383048
sv1: �大吴疆土者
sv1: �大吴疆�
*/
```

### span(C++20)

*`span`是一个轻量级的非拥有型容器, 用于表示连续的内存区间.
提供了对数组、容器和其他连续内存块的统一访问接口, 而无需复制数据.*

```cpp
/* Example */

/* 暂不支持c++20 */
```

### bitset(比特集)

*bitset 表示一个 N 位的固定大小序列*

```cpp
/* Example */

#include <bitset>
#include <iostream>

int main(void) {
  std::bitset<8> b1("10101011");
  std::bitset<8> b2(121u);
  // 打印
  std::cout << "b1: " << b1 << std::endl;
  std::cout << "b2: " << b2 << std::endl;
  // any()
  std::cout << "b1.any(): " << std::boolalpha << b1.any() << std::endl;
  // all()
  std::cout << "b1.all(): " << std::boolalpha << b1.all() << std::endl;
  // count() 返回1的个数
  std::cout << "b2.count(): " << b2.count() << std::endl;
  // size
  std::cout << "b2.size(): " << b2.size() << std::endl;
  // 0/1 翻转
  std::cout << "b2     : " << b2 << std::endl;
  b2.flip();
  std::cout << "b2 flip: " << b2 << std::endl;
  // to string
  std::cout << "to string: " << b2.to_string() << std::endl;
  // to ulong
  std::cout << "to ulong: " << b2.to_ulong() << std::endl;
  // 左移
  std::cout << "b1: " << b1 << " b1 << 1: " << (b1 << 1) << std::endl;
  // 右移
  std::cout << "b1: " << b1 << " b1 << 1: " << (b1 >> 1) << std::endl;
  // 按位与
  std::cout << "b1: " << b1 << " b2: " << b2 << " b1 & b2: " << (b1 & b2)
            << std::endl;
  // 按位或
  std::cout << "b1: " << b1 << " b2: " << b2 << " b1 | b2: " << (b1 | b2)
            << std::endl;
  // 按位异或
  std::cout << "b1: " << b1 << " b2: " << b2 << " b1 ^ b2: " << (b1 ^ b2)
            << std::endl;
  // 取反
  std::cout << "b1: " << b1 << " ~b1: " << (~b1) << std::endl;

  return 0;
}

/*
b1: 10101011
b2: 01111001
b1.any(): true
b1.all(): false
b2.count(): 5
b2.size(): 8
b2     : 01111001
b2 flip: 10000110
to string: 10000110
to ulong: 134
b1: 10101011 b1 << 1: 01010110
b1: 10101011 b1 << 1: 01010101
b1: 10101011 b2: 10000110 b1 & b2: 10000010
b1: 10101011 b2: 10000110 b1 | b2: 10101111
b1: 10101011 b2: 10000110 b1 ^ b2: 00101101
b1: 10101011 ~b1: 01010100
*/
```

### Algorithm

*[standard library algorithm](https://hackingcpp.com/cpp/std/algorithms.html)*

#### 遍历

`for_each(@begin, @end, f(o))`: 接收迭代器的开始和结束, 将`f`作用这些元素

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{1, 1, 4, 5, 1, 4};
  // 打印元素
  std::for_each(nums.begin(), nums.end(),
                [](int n) -> void { std::cout << n << ' '; });
  std::cout << std::endl;

  return 0;
}

/*
1 1 4 5 1 4
*/
```

`next(@position, [steps])`: 返回当前迭代器后`steps`位置的迭代器

`prev(@position, [steps])`: 返回当前迭代器前`steps`位置的迭代器

```cpp
// begin + 1位置
next(nums.begin());
// begin
prev(nums.begin() + 1);
```

#### 统计

`any_of(@begin, @end, check(o) -> bool)`: 任意一个满足`check`, 返回`true`

`all_of(@begin, @end, check(o) -> bool)`: 所有都满足`check`, 返回`true`

`none_of(@begin, @end, check(o) -> bool)`: 没有一个满足`check`, 返回`true`

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{10, 51, 74, 59, 11, 48, 97};
  // any_of
  std::cout << std::boolalpha
            << std::any_of(nums.begin(), nums.end(),
                           [](int n) -> bool { return n > 50; })
            << std::endl;
  // all_of
  std::cout << std::boolalpha
            << std::all_of(nums.begin(), nums.end(),
                           [](int n) -> bool { return n > 50; })
            << std::endl;
  // none_of
  std::cout << std::boolalpha
            << std::none_of(nums.begin(), nums.end(),
                            [](int n) -> bool { return n < 10; })
            << std::endl;

  return 0;
}

/*
true
false
true
*/
```

`count(@begin, @end, value)`: 统计有多少个元素等于`value`

`count_if(@begin, @end, f(o) -> bool)`: 统计有多少个元素满足`f`

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{11, 11, 74, 59, 11, 48, 97};
  auto fun = [](int n) -> void { std::cout << n << ' '; };
  // count
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << std::count(nums.begin(), nums.end(), 11) << std::endl;
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << std::count(nums.begin(), nums.end(), 55) << std::endl;
  // count_if
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << std::count_if(nums.begin(), nums.end(), [](int n) -> bool {
    return n % 2 == 1;
  }) << std::endl;

  return 0;
}

/*
11 11 74 59 11 48 97 | 3
11 11 74 59 11 48 97 | 0
11 11 74 59 11 48 97 | 5
*/
```

#### 查找

`find(@begin, @end, value)`: 找到第一个等于`value`迭代器, 否则返回`end`

`find_if(@begin, @end, f(o) -> bool)`: 找到第一个满足`f`的迭代器, 否则返回`end`

`find_first_of(@Sbegin, @Send, @Wbegin, @Wend)`: 找到S迭代器中满足在W迭代器中的第一个迭代器

`search_n(@begin, @end, n, value)`: 找到第一个连续出现`n`次的`value`

`search(@Sbegin, @Send, @Wbegin, @Wend)`: 查找S中是否包含W

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{74, 11, 11, 59, 11, 48, 97};
  std::vector<int> nums2{33, 32, 90, 59, 11};
  auto fun = [](int n) -> void { std::cout << n << ' '; };
  auto check = [](int n) -> bool { return n < 20; };

  // find
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << *std::find(nums.begin(), nums.end(), 59) << std::endl;
  // find_if
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << *std::find_if(nums.begin(), nums.end(), check) << std::endl;
  // find_first_of
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::for_each(nums2.begin(), nums2.end(), fun);
  std::cout << "| ";
  std::cout << *std::find_first_of(nums.begin(), nums.end(), nums2.begin(),
                                   nums2.end())
            << std::endl;
  // search_n
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << *std::search_n(nums.begin(), nums.end(), 2, 11) << std::endl;
  // search
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::for_each(nums2.begin(), nums2.end(), fun);
  std::cout << "| ";
  std::cout << *std::search(nums.begin(), nums.end(), nums2.begin() + 3,
                            nums2.end())
            << std::endl;

  return 0;
}

/*
74 11 11 59 11 48 97 | 59
74 11 11 59 11 48 97 | 11
74 11 11 59 11 48 97 | 33 32 90 59 11 | 11
74 11 11 59 11 48 97 | 11
74 11 11 59 11 48 97 | 33 32 90 59 11 | 59
*/
```

#### 比较

`equal(@begin1, @end1, @begin2, [@end2])`: 比较第一组迭代器是否等于第二组迭代器

`mismatch(@begin1, @end1, @begin2, [@end2])`: 返回第一个不相等的迭代器位置pair

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums1{11, 11, 74, 59, 11, 48, 97};
  std::vector<int> nums2{11, 11, 74, 59, 11, 48, 97};
  std::vector<int> nums3{11, 11, 70, 59, 11, 48, 97};
  auto fun = [](int n) -> void { std::cout << n << ' '; };

  // equal
  std::for_each(nums1.begin(), nums1.end(), fun);
  std::cout << "| ";
  std::for_each(nums2.begin(), nums2.end(), fun);
  std::cout << "| ";
  std::cout << std::boolalpha
            << std::equal(nums1.begin(), nums1.end(), nums2.begin())
            << std::endl;
  // mismatch
  std::for_each(nums1.begin(), nums1.end(), fun);
  std::cout << "| ";
  std::for_each(nums3.begin(), nums3.end(), fun);
  std::cout << "| ";
  auto misPair = std::mismatch(nums1.begin(), nums1.end(), nums3.begin());
  std::cout << *misPair.first << ' ' << *misPair.second << std::endl;

  return 0;
}

/*
11 11 74 59 11 48 97 | 11 11 74 59 11 48 97 | true
11 11 74 59 11 48 97 | 11 11 70 59 11 48 97 | 74 70
*/
```

#### 排序

`sort(@begin, @end, [compare(O) -> bool])`: 排序

```cpp
/* Example */

#include <algorithm>
#include <functional>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{11, 11, 74, 59, 11, 48, 97};
  auto fun = [](int n) -> void { std::cout << n << ' '; };

  // sort
  std::sort(nums.begin(), nums.end());
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << std::endl;
  std::sort(nums.begin(), nums.end(), std::greater<int>());
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << std::endl;

  return 0;
}

/*
11 11 11 48 59 74 97 
97 74 59 48 11 11 11
*/
```

#### 二分查找

`binary_search(@begin, @end, value, [compare(O) -> bool])`: 二分查找

`includes(@begin1, @end1, @begin2, @end2)`: 有序迭代器2是否是有序迭代器1的子集(不要求连续)

```cpp
/* Example */

#include <algorithm>
#include <functional>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{11, 11, 74, 59, 11, 48, 97};
  std::vector<int> nums2{11, 48, 74};
  auto fun = [](int n) -> void { std::cout << n << ' '; };

  // sort
  std::sort(nums.begin(), nums.end());
  // binary search
  bool result = std::binary_search(nums.begin(), nums.end(), 59);
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| " << std::boolalpha << result << std::endl;
  result = std::binary_search(nums.begin(), nums.end(), 87);
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| " << std::boolalpha << result << std::endl;
  // includes
  result = std::includes(nums.begin(), nums.end(), nums2.begin(), nums2.end());
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::for_each(nums2.begin(), nums2.end(), fun);
  std::cout << "| " << std::boolalpha << result << std::endl;

  return 0;
}

/*
11 11 11 48 59 74 97 | true
11 11 11 48 59 74 97 | false
11 11 11 48 59 74 97 | 11 48 74 | true
*/
```

#### 最大最小值

`min_element(@begin, @end, [compare(O, O) -> bool])`: 最小值

`max_element(@begin, @end, [compare(O, O) -> bool])`: 最大值

`minmax_element(@begin, @end, [compare(O, O) -> bool])`: 最大最小值

```cpp
/* Example */

#include <algorithm>
#include <functional>
#include <iostream>
#include <vector>

int main(void) {
  std::vector<int> nums{11, 11, 74, 59, 11, 48, 97};
  auto fun = [](int n) -> void { std::cout << n << ' '; };

  // min element
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << *std::min_element(nums.begin(), nums.end()) << std::endl;
  // max element
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::cout << *std::max_element(nums.begin(), nums.end()) << std::endl;
  // min max element
  auto minMaxPair = std::minmax_element(nums.begin(), nums.end());
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| " << *minMaxPair.first;
  std::cout << " | " << *minMaxPair.second << std::endl;

  return 0;
}

/*
11 11 74 59 11 48 97 | 11
11 11 74 59 11 48 97 | 97
11 11 74 59 11 48 97 | 11 | 97
*/
```

#### 拷贝/移动/交换

`copy(@begin, @end, @target_begin) -> @target_end`: 将迭代器中的内容复制到目标迭代器中

`move(@begin, @end, @target_begin) -> @target_end`: 将迭代器中的内容移动到目标迭代器中

`iter_swap(@first, @second)`: 交换两个迭代器指定的内容

`swap_ranges(@begin, @end, @target_begin) -> @target_end`: 交换迭代器中的内容

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <string>
#include <vector>

int main(void) {
  std::vector<int> nums{11, 11, 74, 59, 11, 48, 97};
  std::vector<int> nums2(10);
  std::vector<std::string> str{"A", "B", "C", "D", "E", "F"};
  std::vector<std::string> str2(10);
  auto fun = [](int n) -> void { std::cout << n << ' '; };
  auto fun2 = [](std::string s) -> void { std::cout << s << ' '; };

  // copy
  auto copyEnd = std::copy(nums.begin(), nums.begin() + 5, nums2.begin());
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << "| ";
  std::for_each(nums2.begin(), copyEnd, fun);
  std::cout << std::endl;
  // move
  auto moveEnd = std::move(str.begin(), str.begin() + 3, str2.begin());
  std::for_each(str.begin(), str.end(), fun2);
  std::cout << "| ";
  std::for_each(str2.begin(), moveEnd, fun2);
  std::cout << std::endl;
  // iter_swap
  std::for_each(nums.begin(), nums.end(), fun);
  std::iter_swap(nums.begin(), nums.end() - 1);
  std::cout << "| ";
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << std::endl;
  // swap ranges
  std::for_each(nums.begin(), nums.end(), fun);
  std::swap_ranges(nums.begin(), nums.begin() + 2, nums.end() - 2);
  std::cout << "| ";
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << std::endl;

  return 0;
}

/*
11 11 74 59 11 48 97 | 11 11 74 59 11 
   D E F | A B C 
11 11 74 59 11 48 97 | 97 11 74 59 11 48 11 
97 11 74 59 11 48 11 | 48 11 74 59 11 97 11 
*/
```

#### 修改

`fill(@begin, @end, value)`: 用`value`填充迭代器

`generate(@begin, @end, generator())`: 通过`generator()`生成值

`transform(@begin, @end, @out, f(o))`: 将迭代器中元素作用`f`后存入指定迭代器

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <string>
#include <vector>

int main(void) {
  std::vector<int> nums1(10);
  std::vector<int> nums2(10);
  std::vector<int> nums3(10);
  auto fun = [](int n) -> void { std::cout << n << ' '; };
  auto gen = []() -> int {
    static int i = 0;
    i++;
    return i * i;
  };
  auto trans = [](int n) -> int { return n * 3; };

  // fill
  std::fill(nums1.begin(), nums1.end(), 8);
  std::for_each(nums1.begin(), nums1.end(), fun);
  std::cout << std::endl;
  // generate
  std::generate(nums2.begin(), nums2.end(), gen);
  std::for_each(nums2.begin(), nums2.end(), fun);
  std::cout << std::endl;
  // transform
  std::transform(nums2.begin(), nums2.end(), nums3.begin(), trans);
  std::for_each(nums3.begin(), nums3.end(), fun);
  std::cout << std::endl;

  return 0;
}

/*
8 8 8 8 8 8 8 8 8 8 
1 4 9 16 25 36 49 64 81 100 
3 12 27 48 75 108 147 192 243 300 
*/
```

`replace(@begin, @end, old_value, new_value)`: 将迭代器中旧值替换为新值

`replace_if(@begin, @end, condition(o) -> bool, new_value)`: 将迭代器中满足条件的旧值替换为新值

`remove(@begin, @end, value)`: 移除等于`value`的值

`remove_if(@begin, @end, f(o) -> bool)`: 移除满足条件的值

```cpp
/* Example */

#include <algorithm>
#include <iostream>
#include <string>
#include <vector>

int main(void) {
  std::vector<int> nums{1, 9, 8, 8, 2, 2, 4, 5};
  auto fun = [](int n) -> void { std::cout << n << ' '; };
  auto condition = [](int n) -> bool { return n > 7; };

  // replace
  std::for_each(nums.begin(), nums.end(), fun);
  std::replace(nums.begin(), nums.end(), 2, 0);
  std::cout << "| ";
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << std::endl;
  // replace_if
  std::for_each(nums.begin(), nums.end(), fun);
  std::replace_if(nums.begin(), nums.end(), condition, 10);
  std::cout << "| ";
  std::for_each(nums.begin(), nums.end(), fun);
  std::cout << std::endl;
  // remove
  std::for_each(nums.begin(), nums.end(), fun);
  auto removeEnd = std::remove(nums.begin(), nums.end(), 0);
  std::cout << "| ";
  std::for_each(nums.begin(), removeEnd, fun);
  std::cout << std::endl;
  // remove_if
  std::for_each(nums.begin(), removeEnd, fun);
  auto removeIfEnd = std::remove_if(nums.begin(), removeEnd, condition);
  std::cout << "| ";
  std::for_each(nums.begin(), removeIfEnd, fun);
  std::cout << std::endl;

  return 0;
}

/*
1 9 8 8 2 2 4 5 | 1 9 8 8 0 0 4 5 
1 9 8 8 0 0 4 5 | 1 10 10 10 0 0 4 5 
1 10 10 10 0 0 4 5 | 1 10 10 10 4 5 
1 10 10 10 4 5 | 1 4 5 
*/
```

### 智能指针

#### `unique_ptr`

*`unique_ptr`拥有资源的唯一所有权, 当`unique_ptr`被销毁或重置时, 资源自动释放.
因为拥有资源唯一所有权, 无法进行赋值/复制操作, 但可以在函数中传递引用.*

```cpp
/* Example */

/* user.hpp */

#ifndef USER_HPP_
#define USER_HPP_

#include <algorithm>
#include <iostream>
#include <string>

class User {
 private:
  int m_id;
  std::string m_name;

 public:
  User() { std::cout << "无参构造" << std::endl; }
  User(int id, const std::string &name) : m_id(id), m_name(name) {
    std::cout << "id = " << m_id << " 带参构造" << std::endl;
  }
  ~User() { std::cout << "析构函数" << std::endl; }
  void sayHello() const { std::cout << "Hello " << m_name << '!' << std::endl; }
  friend std::ostream &operator<<(std::ostream &os, const User &user) {
    os << "id: " << user.m_id << " name: " << user.m_name;
    return os;
  }
};

#endif  // !USER_HPP_

/* main.cpp */

#include <iostream>
#include <memory>

#include "user.hpp"

void printUser(User *user);

int main(void) {
  // 创建智能指针
  std::unique_ptr<User> user = std::make_unique<User>(1, "Steve");
  // 调用方法
  user->sayHello();
  // get 方法获取底层指针
  printUser(user.get());
  // release 断开与底层指针的连接
  User *o_user = user.release();
  std::cout << "is nullptr: " << std::boolalpha << (user == nullptr)
            << std::endl;
  // reset 释放底层指针, 根据需要改成另一个指针
  user.reset(o_user);
  std::cout << *user << std::endl;

  return 0;
}

void printUser(User *user) { std::cout << *user << std::endl; }

/*
id = 1 带参构造
Hello Steve!
id: 1 name: Steve
is nullptr: true
id: 1 name: Steve
析构函数
*/
```

此外, 可以用智能指针存数组:

```cpp
// 构造大小为10的数组
unique_ptr<int[]> p = make_unique<int[]>(10);
```

#### `shared_ptr`

*有多个对象或者代码段需要同一指针的副本, 可以使用`shared_ptr`, 它是一个可复制的
支持共享所有权的智能指针. `shared_ptr`的初始化及使用和`unique_ptr`基本相同.*

`shared_ptr`何时释放资源:

> 通过引用计数: 引用计数用于跟踪正在使用的某个类的实例或特定对象的个数. 
引用计数的智能指针跟踪[为引用一个真实指针而建立的智能指针的数目], 每次复制这样
一个引用计数的智能指针时, 都会创建一个指向同一资源的新实例, 并且引用计数会增加. 
当此类智能指针实例超出作用域或被重置时, 引用计数减少. 当引用计数降至0时, 资源
不再有其它所有者, 智能指针将资源释放.

```cpp
/* Example */

#include <iostream>
#include <memory>

#include "user.hpp"

int main(void) {
  std::shared_ptr<User> u1 = std::make_shared<User>(1, "steve");
  std::cout << "use count: " << u1.use_count() << std::endl;
  {
    std::shared_ptr<User> u2 = u1;
    std::cout << "use count: " << u1.use_count() << std::endl;
  }
  std::cout << "use count: " << u1.use_count() << std::endl;
  std::shared_ptr<User> u3 = u1;
  std::cout << "use count: " << u1.use_count() << std::endl;

  return 0;
}

/*
id = 1 带参构造
use count: 1
use count: 2
use count: 1
use count: 2
析构函数
*/
```
