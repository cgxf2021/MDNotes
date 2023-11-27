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

## CPP 面向对象

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
