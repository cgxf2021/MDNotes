# Linux 笔记

[toc]

## 常用命令

### 用户及权限管理

用户在 Linux 下分为三种不同的类型: root 用户、系统用户、普通用户. 

* 用户存放文件: /etc/passwd
* 密码存放文件: /etc/shadow
* 登陆配置文件: /etc/login.defs
* `useadd`配置文件: /etc/default/useradd

组用来管理用户, 分为主组和附属组. 

* 组相关文件: /etc/group

**useradd: 创建用户命令**

```shell
# 创建一个 user 用户
useradd user
# 为用户初始化密码
passwd user
# 查看用户信息
cat /etc/passwd | grep user

# 指定家目录位置
useradd -b /tmp user
# 指定家目录名称
useradd -d /tmp/myuser user
# 为用户添加备注
useradd -C "test user" user
# 为用户指定附属组
useradd -G root, smxrfx user
# 为用户指定shell
useradd -s /bin/zsh user
# 查看useradd默认设置
useradd -D
# 修改useradd默认设置
useradd -D -s /bin/zsh
# 创建系统用户
useradd -r user
```

**usermod: 修改用户属性**

```shell
# 修改用户添加备注
usermod -C "test user" user
# 修改用户附属组(a表示追加)
usermod -G bin user
usermod -aG bin user
# 修改用户名
usermod -l user2 user1
# 修改密码
usermod -p password user
```

**userdel: 删除用户**

```shell
# 默认不删除家目录
userdel user
# 删除家目录
userdel -r user
```

**groupadd: 创建组**

```shell
# 创建组
groupadd group
# 指定组id
groupadd -g 1010 group
```

**groupmod: 修改组属性**

```shell
# 修改组属性
groupmod -g 1111 group
# 修改组名
groupmod -n group2 group1
```

**groupdel: 删除组**

```shell
# 删除组
groupdel group
```

### 文件及权限管理

**chown: 修改文件属主或属组**

```shell
# 文件改变
chown user:group file
# 目录改变
chown user:group directory
# 递归改变
chown -R user:group directory
```

**chmod: 改变文件/目录权限**

* 文件用户: u(文件所有者), g(同组其他用户), o(其他用户)
* 文件权限: r(读权限 4), w(写权限 2), x(执行权限 1)

```shell
# ---x------

# 为文件所有者添加读写权限(-rwx------)
chmod u+rw file
# 为同组其他用户指定读写权限(-rwxrw----)
chmod 760 file
# 解除同组其他用户读写权限
chmod g-rw file
chmod 700 file

# drwx------

# 为其他用户添加读写权限
chmod 706 directory
# 递归的给目录下指定权限
chmod 777 -R directory

# 权限改变时打印
chmod -c a-x directory
chmod -v a-x directory
```

## shell

## Makefile

### GCC

[GCC官方文档](https://gcc.gnu.org/onlinedocs)

* g++: GCC的一个版本, 默认语言设置为C++, 在链接的时候包含标准C++库. 
* gcc: 该驱动程序等同于执行编译程序和链接程序以产生需要的输出. 
* gdb: GNU调试器, 用于检查程序运行时的值和行为. 

### 编译过程

1. **预处理阶段**: (.c -- .i)将所有的宏展开, 去掉所有的条件预编译指令, 
将所有的头文件包含进来, 删除注释等.
2. **编译阶段**: (.i -- .s)对代码的语法, 语义和词法等进行分析, 生成汇编文件. 
3. **汇编阶段**: (.s -- .o)生成二进制文件. 
4. **链接阶段**: 将各个模块之间的相互引用处理好. 
把所有的静态库用到的目标文件装入程序中, 并进行统一编址, 然后进行重定位, 
即逻辑地址到物理地址的转换.

### C 编译

#### C 语言相关的文件后缀

| 后缀 | 文件内容 |
| ----- | ----- |
| .a | 静态库文件 |
| .c | 源文件 |
| .h | 头文件 |
| .i | 预处理文件 |
| .s | 汇编文件 |
| .o | 二进制文件 |
| .so | 动态库文件/共享库 |

#### 编译一个C源文件

1. 预处理阶段

```shell
gcc -E main.c -o main.i
```

* -E: 告诉编译器只进行预处理操作; 
* -o: 把预处理结果输出到指定的文件; 

2. 编译阶段

```shell
gcc -S main.c -o main.s
```

* -S: 告诉编译器执行预处理和编译成汇编语言操作; 

3. 汇编阶段

```shell
gcc -c main.c -o main.o
```

* -c: 将源文件编译成目标二进制文件; 

4. 编译为可执行文件

```shell
gcc main.c -o main
./main
```

#### C语言静态库(static library)

1. 编译成.o文件

```shell
gcc -c [.c] -o [自定义文件名]
gcc -c add.c minus.c
```

2. 静态库

```shell
ar -r [lib自定义库名.a] [.o] [.o]
ar -r liboperation.a add.o minus.o
```

3. 链接成可执行文件

```shell
gcc [.c] [.a] -o [自定义文件名]
gcc [.c] -o [自定义文件名] -l[库名] -L[库所在路径]

gcc main.c liboperation.a -o main
```

#### C语言动态库(shared library)

1. 编译成.o文件

```shell
gcc -c -fpic [.c] [.c]
gcc -c -fpic add.c minus.c 
```

2. 动态库

```shell
gcc -shared [.o] [.o] -o [lib自定义库名.so]
gcc -shared add.o minus.o -o liboperation.so 
```

3. 链接成可执行文件

```shell
gcc [.c] -o [自定义文件名] -l[库名] -L[库路径] -Wl,-rpath=[库路径]
```

### C++ 编译

> 将上述C编译中的gcc替换为g++

### Makefile 基本语法

* [Make 官方文档](https://www.gnu.org/software/make/manual/make.html)

#### 基本格式

```Makefile
targets(生成项) : prerequisties(依赖项)
[tab]command
```

#### 基本规则

* `make`会在当前目录下找到一个名叫Makefile或makefile的文件;
* 如果找到, 它会找文件中第一个目标文件, 并将这个文件作为最终的目标文件;
* 如果target文件不存在, 或者target文件依赖的prerequisties文件修改时间要比这个target文件新, 
就会执行后面所定义的command来生成target这个文件; 
* 如果target依赖的prerequisties也存在, `make`会在当前文件中找到target为prerequisties的依赖性, 
如果找到再根据那个规则生成prerequisties文件; 

#### 伪目标

* 伪目标不是一个文件, 只是一个标签. 需要显式地指明这个目标才能让它生效. 
* 伪目标的取名不能和文件同名, 否则不能执行. 
* 可以使用`.PHONY`显式地指明一个目标是伪目标.

```Makefile
clean : 
    @rm -rf src
debug : 
    @echo hello
.PHONY : clean debug
```

#### Makefile变量的定义和使用

变量在声明时需要给予初值, 使用时需要在变量名前加上`$`符号, 并用小括号`()`将变量括起来. 

**变量的定义**

```Makefile
cpp := src/main.cpp
obj := objs/main.o
```

**变量的引用**

```Makefile
cpp := src/main.cpp
obj := objs/main.o

$(obj) : $(cpp)
    @g++ -c $(cpp) -o $(obj)

compile : $(obj)
```

**预定义变量**

* `$@`: 目标(target)的完整名称;
* `$<`: 第一个依赖文件的名称;
* `$^`: 所有的依赖文件, 以空格分开, 不包含重复的依赖文件;

```Makefile
cpp := src/main.cpp
obj := objs/main.o

$(obj) : $(cpp)
    @g++ -c $< -o $@
    @echo $^

compile : $(obj)

.PHONY : compile
```

#### Makefile常用符号

1. `=`: 简单的赋值运算, 用于将右边的值分配给左边的变量, 
如果在后面的语句中重新定义了该变量, 则将使用新的值(copy);

```Makefile
HOST_ARCH = aarch64
TARGET_ARCH = $(HOST_ARCH)
# ...
HOST_ARCH = amd64

debug : 
    @echo $(TARGET_ARCH)

.PHONY : debug

# amd64
```

2. `:=`: 立即赋值运算符, 用于在定义变量时立即求值, 该值在定以后不再改变, 
即使在后面的语句中重新定义了该变量(deep copy). 

```Makefile
HOST_ARCH := aarch64
TARGET_ARCH := $(HOST_ARCH)

HOST_ARCH := amd64

debug : 
    @echo $(TARGET_ARCH)

.PHONY : debug

# aarch64
```

3. `?=`: 默认赋值运算符, 如果变量已经定义, 则不做任何操作, 否则进行求值分配. 

```Makefile
HOST_ARCH := aarch64
HOST_ARCH ?= amd64

debug : 
    @echo $(HOST_ARCH)

.PHONY : debug

# aarch64
```

4. `+=`: 累加

```Makefile
include_paths := src
CXXFLAGS := -m64 -fpic -g -std=c++11 -w -fopenmp
CXXFLAGS += $(include_paths)

debug : 
    @echo $(CXXFLAGS)

.PHONY : debug

# -m64 -fpic -g -std=c++11 -w -fopenmp src
```

5. `\`: 续行符

```Makefile
include_paths := src
CXXFLAGS := -m64 -fpic -g \
            -std=c++11 -w -fopenmp
CXXFLAGS += $(include_paths)

debug : 
    @echo $(CXXFLAGS)

.PHONY : debug

# -m64 -fpic -g -std=c++11 -w -fopenmp src
```

#### Makefile常用函数(shell)

**函数的调用**

```Makefile
$(function arguments)

# function: 函数名;
# arguments: 函数参数, 参数间以逗号分隔;
```

1. `shell`

调用`shell`命令

```Makefile
$(shell <command> <arguments>)

# return: shell命令的执行结果;
cpp_srcs := $(shell find src -name "*.cpp")
# src/main.cpp src/add.cpp src/minus.cpp
```

2. `subst`

将字符串`<text>`中的`<from>`替换成`<to>`

```Makefile
$(subst <from>,<to>,<text>)

# return: 替换后的字符串
cpp_srcs := $(shell find src -name "*.cpp")
cpp_objs := $(subst src/,objs/,$(cpp_srcs))
cpp_objs := $(subst .cpp,.o,$(cpp_objs))
# objs/main.o objs/add.o objs/minus.o
```

3. `patsubst`

可以使用`%`通配符, 表示任意长度的字符串, 
从`<text>`中取出`<pattern>`替换为`<replacement>`. 

```Makefile
$(patsubst <pattern>,<replacement>,<text>)

# return: 替换后的字符串
cpp_srcs := $(shell find src -name "*.cpp")
cpp_objs := $(patsubst src/%.cpp,objs/%.o,$(cpp_srcs))
# objs/main.o objs/add.o objs/minus.o
```

4. `foreach`

将`<list>`中的元素逐一取出, 执行`<text>`中包含的表达式. 

```Makefile
$(foreach <var>,<list>,<text>)

# return: 替换后的字符串
include_paths := /usr/include \
                 /usr/core \
                 /usr/opencv
include_paths := $(foreach item,$(include_paths),-I$(item))
# -I/usr/include -I/usr/core -I/usr/opencv
```

> 同等效果

```Makefile
I_flags := $(include_paths:%=-I%)
# -I/usr/include -I/usr/core -I/usr/opencv
```

5. `dir`

从文件名序列中取出目录部分. 目录部分是指最后一个`/`之前的部分, 没有则返回`./`. 

```Makefile
$(dir <names...>)

# return: 文件名序列的目录部分
cpp_srcs := $(shell find src -name "*.cpp")
cpp_objs := $(patsubst src/%.cpp,objs/%.o,$(cpp_srcs))

# create dir
objs/%.o : src/%.cpp
    @mkdir -p $(dir $@)
    @g++ -c $^ -o $@

compile : $(cpp_objs)

debug : 
    @echo $(cpp_objs)

.PHONY : debug compile
```

6. `notdir`

```Makefile
$(notdir <names ...>)

# return: 不带路径的文件列表
libs := $(notdir $(shell find /usr/lib -name lib*))
```

7. `filter`

```Makefile
$(filter <names ...>)

# return: 返回过滤后的文件列表
libs := $(notdir $(shell find /usr/lib -name lib*))
static_libs := $(filter %.a,$(libs))
dynamic_libs := $(filter %.so,$(libs))
```

8. `basename`

```Makefile
$(basename <names ...>)

# return: 去掉文件的后缀
static_libs := $(subst lib,,$(basename $(filter %.a,$(libs))))
dynamic_libs := $(subst lib,,$(basename $(filter %.so,$(libs))))
```

### Makefile 编译

#### 编译单个CPP文件示例

```Makefile
cpp_srcs := $(shell find src -name *.cpp)
cpp_i := $(patsubst src/%.cpp,src/%.i,$(cpp_srcs))
cpp_s := $(patsubst src/%.cpp,src/%.s,$(cpp_srcs))
cpp_o := $(patsubst src/%.cpp,obj/%.o,$(cpp_srcs))

# preprocess
src/%.i : src/%.cpp
    @g++ -E $< -o $@

preprocess : $(cpp_i)

# assemble
src/%.s : src/%.cpp
    @g++ -S $< -o $@

assemble : $(cpp_s)

# object
obj/%.o : src/%.cpp
    @mkdir -p $(dir $@)
    @g++ -c $< -o $@

objects : $(cpp_o)

# run
workspace/main : $(cpp_o)
    @mkdir -p $(dir $@)
    @g++ $< -o $@

run : workspace/main
    @./$<

debug :
    @echo $(cpp_o)

clean : 
    @rm -rf src/*.i src/*.s obj workspace

.PHONY : preprocess assemble objects run clean debug 
```

#### 编译选项和链接选项

> 编译选项

* `-m64`: 指定编译为64位应用程序;
* `-std=`: 指定编译标准, 如`-std=c++11`;
* `-g`: 包含调试信息;
* `-w`: 不显示警告;
* `-O`: 优化等级, 通常使用`-O3`;
* `-I`: 加在头文件路径前;
* `-fPIC`: 产生的没有绝对地址, 全部使用相对地址, 代码可以被加载到内存任意位置, 
且可以正确的执行; (共享库)

> 链接选项

* `-l`: 加在库名前;
* `-L`: 加载库路径前;
* `-Wl`: 将逗号分隔的<选项>传递个连接器;
* `rpath=`: 运行的时候去找的目录, 运行时要找.so文件, 会从这个选项指定的地方去找;

#### 编译带头文件的CPP文件示例

```Makefile
cpp_srcs := $(shell find src -name *.cpp)
cpp_objs := $(patsubst src/%.cpp,obj/%.o,$(cpp_srcs))
include_paths := /home/smxrfx/TrainClass/CodeNotes/CPPLearning/MakeLearning/test6/include
I_flags := $(include_paths:%=-I%)
compile_option := -g -O3 -w -std=c++11
compile_option += $(I_flags)

obj/%.o : src/%.cpp
    @mkdir -p $(dir $@)
    @g++ -c $< -o $@ $(compile_option)

workspace/main : $(cpp_objs)
    @mkdir -p $(dir $@)
    @g++ $^ -o $@

run : workspace/main
    @./$<

clean : 
    @rm -rf obj workspace/main

debug : 
    @echo $(compile_option)

.PHONY : debug clean run
```

> `$(cpp_objs)`是包含多个文件的列表, 故需要用`$^`. 

#### 编译一个静态库

```Makefile
lib_srcs := $(filter-out src/main.cpp,$(shell find src -name *.cpp))
lib_objs := $(patsubst src/%.cpp,obj/%.o,$(lib_srcs))
include_paths := ./include
lib_paths := ./lib
linking_libs := $(subst lib,,$(basename $(notdir $(shell find $(lib_paths) -name *.a))))
I_option := $(include_paths:%=-I%)
l_option := $(linking_libs:%=-l%)
L_option := $(lib_paths:%=-L%)

compile_option := -g -O3 -std=c++11 $(I_option)
linking_option := $(l_option) $(L_option)

obj/%.o : src/%.cpp
    @mkdir -p $(dir $@)
    @g++ -c $< -o $@ $(compile_option)

# 编译静态库
lib/liboperation.a : $(lib_objs)
    @mkdir -p $(dir $@)
    @ar -r $@ $^

static_lib : lib/liboperation.a

# 链接静态库
obj/main.o : src/main.cpp
    @mkdir -p $(dir $@)
    @g++ -c $< -o $@ $(compile_option)

workspace/main : obj/main.o
    @mkdir -p $(dir $@)
    @g++ $< -o $@ $(linking_option)

run : workspace/main
    @./$<

clean : 
    @rm -rf obj workspace/main

debug : 
    @echo $(lib_objs)
    @echo $(linking_libs)

.PHONY : static_lib run clean debug
```

#### 编译一个动态库

> TODO: 存在无法找到动态库的问题

```Makefile
cpp_srcs := $(shell find src -name *.cpp)
cpp_objs := $(patsubst src/%.cpp,obj/%.o,$(cpp_srcs))
include_paths := /home/smxrfx/TrainClass/CodeNotes/CPPLearning/MakeLearning/test8/include
# library_paths := /home/smxrfx/TrainClass/CodeNotes/CPPLearning/MakeLearning/test8/lib
library_paths := $(CURDIR)/lib
I_flag := $(include_paths:%=-I%)
compile_option := -g -O3 -w -std=c++11 -fPIC $(I_flag)
so_objs := $(filter-out obj/main.o,$(cpp_objs))
linking_libs := $(subst lib,,$(basename $(notdir $(shell find $(library_paths) -name *.so))))
l_option := $(linking_libs:%=-l%)
L_option := $(library_paths:%=-L%)
r_option := $(library_paths:%=-Wl,rpath=%)
linking_option := $(l_option) $(L_option) $(r_option)

# 编译object文件
obj/%.o : src/%.cpp
    @mkdir -p $(dir $@)
    @g++ -c $< -o $@ $(compile_option)

compile : $(cpp_objs)

# 编译动态库
lib/liboperation.so : $(so_objs)
    @mkdir -p $(dir $@)
    @g++ -shared $^ -o $@ 

dynamic : lib/liboperation.so

# 链接动态库
workspace/main : obj/main.o compile dynamic
    @mkdir -p $(dir $@)
    @g++ $< -o $@ $(linking_option)

run : workspace/main
    @./$<

clean : 
    @rm -rf obj workspace/main lib

debug :
    @echo $(so_objs)
    @echo $(linking_libs)
    @echo $(linking_option)

.PHONY : compile dynamic run clean debug
```

## Linux中c编程

### 进程

> 进程是运行在内存中的程序的执行实例. 

#### 进程的状态及转换

* **就绪**: 进程已经具备执行的一切条件, 正在等待分配`CPU`的使用时间. 
* **执行**: 进程占用`CPU`运行. 
* **等待**: 进程因不具备某些执行条件而暂时无法继续执行(如`CPU`). 

> Linux 中的进程调度: 时间片轮转调度、优先级调度、完全公平调度. 

#### 进程控制块

进程控制块(PCB), 用于保存一个进程信息的结构体. 
操作系统根据PCB来对并发执行的进程进行控制和管理, 
操作系统在创建一个进程的时候会开辟一段内存空间存放与此进程相关的PCB. 

#### 特殊的进程号

在Linux系统中进程号由0开始, 进程号为0和1的进程由内核创建. 
进程号为0的进程通常是调度进程, 常被成为交换进程(swapper). 
进程号为1的进程通常是init进程, init进程是所有进程的祖先, 
除了调度进程外, Linux下所有的进程都由init进程直接或间接创建.  

* PPID: 当前进程的父进程号.
* PID: 当前进程的进程号.
* PGID: 当前进程所在组的进程组号.

Linux 提供了3个获得进程号的函数:

```c
#include <unistd.h>
#include <sys/types.h>

pid_t getpid(void);

pid_t getppid(void);

pid_t getpgid(pid_t pid);
```

#### 创建进程

使用`fork`函数可以在已有的进程基础上创建一个子进程.

```c
pid_t fork(void);
pid_t vfork(void);

/*
return: 成功(>=0); 失败(-1)
(>0): 子进程的<进程号>, 父进程代码区
(=0): 子进程代码区
*/
```

> 地址空间: 包括进程上下文、进程堆栈、打开的文件描述、信号控制设定、进程优先级、
进程组号等. 子进程独有的只有进程号, 计时器. 因此使用`fork`函数代价很大.

**`fork`函数和`vfork`函数的区别:**

`fork`被调用之后, 父进程和子进程的执行顺序是不确定的; `vfork`函数保证子进程先运行, 
父进程会被阻塞, 在子进程调用`exit`函数之后, 父进程才可能别调度运行. 

`fork`会创建一个新的子进程, 该子进程是父进程的副本, 
父进程和子进程将在`fork`调用之后的代码行处开始执行, 并且在之后的代码中彼此独立; 
`vfork`也会创建一个新的子进程, 但子进程会共享父进程的地址空间, 
当`vfork`被调用后, 子进程会在父进程的地址空间中运行, 直到它调用`exec`或`exit`退出. 

`fork`被调用后, 父进程和子进程会拥有各自独立的地址空间副本, 
当父进程或子进程修改自己的地址空间时, 对方是不可见的; 
`vfork`被调用后, 父进程和子进程共享同一个地址空间, 在子进程中对地址空间修改会影响父进程. 

**创建区分主进程和子进程**

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
  pid_t pid = 0;

  // 创建子进程
  pid = fork();
  if (pid == -1) {
    printf("子进程创建失败\n");
    exit(1);
  } else if (pid == 0) {
    printf("子进程id: %d\n", getpid());
  } else {
    printf("主进程id: %d\n", getpid());
  }

  return 0;
}

/*
主进程id: 4722
子进程id: 4723
*/
```

**父进程拥有独立的地址空间**

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
  pid_t pid = 0;
  int num = 100;

  // 创建子进程
  pid = fork();
  if (pid == -1) {
    printf("子进程创建失败\n");
    exit(1);
  } else if (pid == 0) {
    printf("子进程id: %d <--> num: %d\n", getpid(), num);
  } else {
    num++;
    printf("主进程id: %d <--> num: %d\n", getpid(), num);
  }

  return 0;
}

/*
主进程id: 5020 <--> num: 101
子进程id: 5021 <--> num: 100
*/
```

**子进程和父进程共享同一块空间**

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

// 全局区
int x = 10;

int main(void) {
  // 静态区
  static int y = 10;
  // 栈区
  int z = 0;
  pid_t pid = vfork();

  if (pid < 0) {
    perror("fail to create process");
    exit(1);
  } else if (pid > 0) {
    // parent process
    printf("parent process is running ...\n");
    printf("x = %d; y = %d; z = %d\n", x, y, z);
  } else {
    // child process
    printf("child process is running ...\n");
    x++;
    y++;
    z++;
    printf("x = %d; y = %d; z = %d\n", x, y, z);
    exit(0);
  }

  return 0;
}

/*
child process is running ...
x = 11; y = 11; z = 1
parent process is running ...
x = 11; y = 11; z = 1
*/
```

#### 进程睡眠状态

`sleep`函数用于让进程进入睡眠状态(进程等待)

```c
unsigned int sleep(unsigned int seconds);

/*
seconds: 指定挂起的秒数
return: 若进程挂起到指定的时间则返回0, 否则返回剩余的秒数
*/
```

> 若进程挂起到指定的时间则返回0, 若有信号中断则返回剩余秒数. 进程挂起指定的秒数后程序并不会立即执行，系统只是将此进程切换到就绪态. 
进程睡眠到指定的秒数后程序并不会立即执行, 系统只是将此进程切换到就绪态.

#### 进程等待函数

`wait`函数等待子进程结束, 回收子进程资源.

```c
#include <sys/wait.h>

pid_t wait(int *status);

/*
status: 子进程退出时的状态信息, 使用定义宏取出其中的每个字段
return: 成功(子进程的进程号); 失败(-1)
*/
```

> 调用`wait`函数的进程会挂起, 直到它的一个子进程退出或收到一个不能忽略的信号时才被唤醒. 
若调用进程没有子进程或子进程已经结束, 该函数立即返回. 

* 取出子进程的退出信息: `WIFEXITED(status)`(使用前需要判断是否正常退出);
* 取出子进程的退出状态: `WEXITSTATUS(status)`;

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main(void) {
  pid_t pid = 0;
  pid = fork();

  if (pid < -1) {
    perror("fail to create the process.");
    return -1;
  } else if (pid == 0) {
    for (int i = 0; i < 5; i++) {
      printf("child process id: %d\n", getpid());
      sleep(1);
    }
    exit(3);
  } else {
    int status = 0;
    if (wait(&status) > 0) {
      printf("status of the child process: %d\n", WEXITSTATUS(status));
      printf("information of the child process: %d\n", WIFEXITED(status));
    }
    printf("parent process.\n");
  }

  return 0;
}

/*
child process id: 18170
child process id: 18170
child process id: 18170
child process id: 18170
child process id: 18170
status of the child process: 3
information of the child process: 1
parent process.
*/
```

`waitpid`函数等待子进程终止, 回收子进程的资源. 

```c
pid_t waitpid(pid_t pid, int *status, int options);

/*
pid: 指定的进程或进程组
pid > 0: 等待进程id等于pid的子进程;
pid = 0: 等待同一个进程组中的任何子进程;
pid = -1: 等待任何一个子进程;
pid < -1: 等待指定进程组中的任何子进程, 这个进程组的id等于pid的绝对值;
status: 保存子进程退出的状态信息
options: 选项
0: 同wait函数;
WNOHANG: 没有可用子进程终止时立即返回，父进程不会阻塞等待;
WUNTRACED: 除了终止的子进程, 如果子进程暂停(信号暂停)了则立刻返回, 
并且不予理会子进程的结束状态;
return: 子进程的进程号; (WNOHANG且进程存在返回0); 失败(-1)
*/
```

`WNOHANG`参数的使用: 

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main(void) {
  pid_t pid = fork();
  if (pid < -1) {
    perror("fail to create the process.");
    exit(EXIT_FAILURE);
  } else if (pid == 0) {
    for (int i = 0; i < 5; i++) {
      printf("child process id: %d\n", getpid());
      sleep(1);
    }
    exit(10);
  } else {
    int status = 0;
    int options = WNOHANG;

    while (1) {
      pid_t result = waitpid(pid, &status, options);
      if (result == -1) {
        perror("fail to waitpid\n");
        exit(EXIT_FAILURE);
      } else if (result == 0) {
        printf("child process is continuing...\n");
        sleep(1);
      } else {
        if (WIFEXITED(status)) {
          printf("status of the child process: %d\n", WEXITSTATUS(status));
        } else {
          printf("fail to exit\n");
        }
        break;
      }
    }
  }

  return 0;
}

/*
child process is continuing...
child process id: 18822
child process id: 18822
child process is continuing...
child process id: 18822
child process is continuing...
child process id: 18822
child process is continuing...
child process is continuing...
child process id: 18822
child process is continuing...
status of the child process: 10
*/
```

#### 进程终止函数

`[_]exit`函数用于退出当前进程;

```c
#include <unistd.h>

void exit(int status);
void _exit(int status);

/*
status: 退出状态, 由父进程通过wait函数接收这个状态
一般失败设置为非0, 否则设置为0
*/
```

`exit`和`_exit`函数的区别: 

* `exit`为库函数, 而`_exit`为系统调用;
* `exit`会刷新缓冲区, 而`_exit`不会刷新缓冲区;

**进程退出清理函数**

`atexit`函数注册进程正常结束前调用的函数, 进程退出执行注册函数. 

```c
#include <stdlib.h>

int atexit(void (*function)(void));

/*
function: 进程结束前, 调用函数的入口地址;
一个进程可以多次调用atexit函数注册清理函数;
正常结束前调用函数的顺序和注册时顺序相反;

return: 成功(0); 失败(!0)
*/
```

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void function1(void) { printf("%s\n", __func__); }

void function2(void) { printf("%s\n", __func__); }

void function3(void) { printf("%s\n", __func__); }

int main(void) {
  atexit(function1);
  atexit(function2);
  atexit(function3);

  printf("atexit test ...\n");
  sleep(3);

  return 0;
}

/*
atexit test ...
function3
function2
function1
*/
```

#### `exec`函数族

`exec`函数族, 由6个`exec`函数组成: 

1. `exec`函数提供了6个在进程中启动另一个程序的方法; 
2. `exec`函数族可以根据指定的文件名或目录名找到可执行文件; 
3. 调用`exec`函数的进程并不创建新的进程, 故调用`exec`函数前后, 进程号并不改变, 
其执行的程序完全由新的程序替换, 而新的程序则从其`main`函数开始执行; 

```c
#include <unistd.h>

int execl(const char *path, const char *arg, ..., NULL);
int execlp(const char *file, const char *arg, ..., NULL);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execle(const char *path, const char *arg, ..., NULL, char *const envp[]);
int execvpe(const char *file, char *const argv[], char *const envp[]);

/*
path: 命令或程序的路径;
l: 如果函数带l, 对应的命令或者程序是通过每一个参数进行传递, NULL表示结尾;
例如: "ls", "-l", NULL;
v: 如果函数带v, 对应的命令或程序是通过一个指针数组来传递的, NULL表示结尾; 
例如: char *const str[] = {"ls", "-l", NULL};
p: 如果函数带p, 第一个参数可以是绝对路径, 也可以是相对路径, 否则必须是绝对路径; 
return: 失败(-1)
*/
```

使用`exec`函数执行命令

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
  pid_t pid = fork();
  if (pid < 0) {
    perror("fail to create process\n");
    exit(1);
  } else if (pid > 0) {
    printf("parent process is running\n");
    wait(NULL);
    printf("child process has ended\n");
  } else {
    printf("child process is running\n");
    if (execl("/bin/ls", "ls", "-l", NULL) == -1) {
      perror("fail to execl\n");
      exit(1);
    }
    // char *const str[] = {"ls", "-l", NULL};
    // execv("/bin/ls", str);

    exit(0);
  }

  return 0;
}

/* 执行 ls -l */
```

#### `system`函数

`system`函数用于执行一个`shell`命令或脚本. `system`会调用`fork`函数产生子进程, 
子进程调用`exec`启动`/bin/sh -c string`, 其中`string`为要执行的命令. 

```c
#include <stdlib.h>

int system(const char *command);

/*
command: 要执行的命令的字符串
return: 失败(非0)
*/

#include <stdlib.h>

int main(void) {
  system("clear");
  system("ls -l");
  system("./atexit_test");

  return 0;
}
```

### 进程间通信

进程是一个独立的资源分配单元, 不同进程之间的资源是独立的, 
不能在一个进程中直接访问另一个进程的资源. 
进程不是孤立的, 不同的进程需要信息的交互和状态的传递, 因此需要进程间通信. 

进程间通信功能: 

* 数据传输: 一个进程需要将他的数据发送给另一个进程;
* 资源共享: 多个进程之间共享同样的资源; 
* 通知事件: 一个进程需要向另一个或一组进程发送消息, 通知某种事件; 
* 进程控制: 一个进程控制另一个进程;

**进程间通信的实质**

系统只要创建一个进程, 就会为当前进程分配一个虚拟内存, 
虚拟内存包含用户空间和内核空间, 用户空间是进程所私有的, 
每一个进程的用户空间只能自己访问和使用(栈区、堆区、数据区、代码区等), 
内核空间是所有进程所共有的, 这意味着大多数进程通信, 
本质上是对内核空间的操作. 

**特殊的进程通信方式**

主要有管道、命名管道、消息队列、共享内存、信号量、`socket`、信号;

`socket`通信可以实现不同主机进程间通信, 其余只能在一台主机的多进程间通信. 

信号通信是唯一的一种异步通信机制. 

共享内存是所有进程间通信方式中效率最高的, 它是直接对物理内存进行操作. 

#### 信号

信号是一种异步通信方式, 进程不必等待信号到达, 进程也不知道信号什么时候到达. 

信号是软件中断, 是在软件层次上对中断机制的一种模拟. 
信号可以使一个正在运行的进程被另一个正在运行的进程异步进程中断, 
转而处理某个突发事件. 

信号可以直接进行用户空间进程和内核空间进程的交互, 
内核空间进程可以利用信号来通知用会空间进程发生了哪些系统事件. 

**产生信号的方式**

* 终端按键(如`ctrl + c`、`ctrl + z`);
* 硬件异常产生信号; 
* 软件异常产生信号;
* 调用`kill`函数将产生信号, 运行`kill`函数将发送信号; 

**信号的默认处理方式**

当进程中产生信号, 就会让当前进程作出一定的反应;

1. 终止进程: 当信号产生后, 当前进程就会立即结束; 
2. 缺省处理: 当信号产生后, 当前进程不做任何处理; 
3. 停止进程: 当信号产生后, 当前进程停止; 
4. 恢复运行: 当信号产生后, 停止的进程会恢复执行;

> 每个信号只有一个默认处理方式. 

**进程接收到信号后的处理方式**

1. 执行系统默认操作: 对大多数信号, 系统默认操作是终止进程;
2. 忽略: 不做任何操作;
3. 自定义信号处理函数: 执行用户自定义函数; 

| 信号 | 值 | 性质 | 默认处理方式 |
| ----- | ----- | ----- | ----- |
| `SIGKILL` | 9 | - | 退出进程 |
| `SIGSTOP` | 19 | 无法捕捉 | 停止进程 |
| `SIGINT` | 2 | `ctrl + c`产生信号 | 退出进程 |
| `SIGQUIT` | 3 | `ctrl + \`产生信号 | 退出进程 |
| `SIGTSTP` | 20 | `ctrl + z`产生信号 | 停止进程 |
| `SIGCONT` | 18 | - | 恢复运行 |
| `SIGALRM` | 14 | 调用`alarm`函数设置的时间到达时产生信号 | 退出进程 |
| `SIGPIPE` | 13 | 管道破裂时产生信号 | 退出进程 |
| `SIGABRT` | 6 | 调用`abort`函数时产生信号 | 退出进程 |
| `SIGCHLD` | 17 | 使用`fork`创建一个子进程, 子进程状态改变, 产生信号 | 缺省 |
| `SIGUSR1` | 10 | 用户自定义信号 | 缺省 |
| `SIGUSR2` | 12 | 用户自定义信号 | 缺省 |

##### `kill`函数

`kill`函数给指定的进程或进程组发送信号.

```c
#include <signal.h>

int kill(pid_t pid, int sig);

/*
pid > 0: 将信号发送给进程ID为pid的进程;
pid = 0: 将信号发送给当前进程所在进程组中的所有进程;
pid = -1: 将信号发送给系统内所有的进程, 除了init进程;
pid < -1: 将信号发送给指定进程组的所有进程(进程组号 = |pid|);
sig: 指定的信号;
return: 成功(0) 失败(!0)
*/
```

**子进程发送信号使父进程退出**

```c
/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
  pid_t pid = fork();

  if (pid < 0) {
    perror("fail to create process\n");
    exit(1);
  } else if (pid > 0) {
    while (1) {
      printf("parent process\n");
      sleep(1);
    }
  } else {
    printf("child process\n");
    sleep(3);
    kill(getppid(), SIGINT);
  }

  return 0;
}

/*
parent process
child process
parent process
parent process

*/
```

##### `alarm`函数

`alarm`函数设定一个时间, 当设定的时间到时, 产生`SIGALRM`信号. 

```c
#include <unistd.h>

unsigned int alarm(unsigned int seconds);

seconds: 秒数
return: 如果alarm函数之前没有alarm设置, 返回0, 如果有, 返回上一个alarm剩余时间. 
```

```c
/* Example */

#include <stdio.h>
#include <unistd.h>

int main(void) {
  unsigned int second;
  second = alarm(5);
  sleep(3);
  second = alarm(7);

  printf("second = %d\n", second);
  while (1) {
    printf("continue ...\n");
    sleep(1);
  }

  return 0;
}

/*
second = 2
continue ...
continue ...
continue ...
continue ...
continue ...
continue ...
continue ...
[1]    5301 alarm      ./alarm_test
*/
```

##### `raise`函数

`raise`函数给调用进程本身发送信号.

```c
#include <signal.h>

int raise(int sig);

/*
sig: 信号
return: 成功(0); 失败(!0)
*/

/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
  int count = 0;
  while (1) {
    count++;
    printf("%d continue ...\n", count);
    sleep(1);
    if (count == 5) {
      raise(SIGINT);
    }
  }

  return 0;
}

/*
1 continue ...
2 continue ...
3 continue ...
4 continue ...
5 continue ...

*/
```

##### `pause`函数

`pause`函数阻塞进程等待一个信号的产生. 

```c
#include <unistd.h>

int pause(void);

/* return: 有信号产生时返回-1 */

/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
  pid_t pid = fork();
  if (pid < 0) {
    perror("fail to create process\n");
    exit(1);
  } else if (pid > 0) {
    printf("parent process running ...\n");
    // 捕捉信号
    pause();
  } else {
    printf("child process running ...\n");
    sleep(3);
    kill(getppid(), SIGINT);
    printf("child send signal to parent\n");
  }

  return 0;
}

/*
parent process running ...
child process running ...
child send signal to parent

*/
```

##### `signal`函数

当进程中产生某个信号时, `signal`函数对当前信号进行处理. 

```c
#include <signal.h>

typedef void (*sighandler_t)(int);
sighandler_t signal(int signum, sighandler_t handler);

/*
sig: 信号;
handler: 处理方式;
SIG_IGN: 当信号产生时, 忽略
SIG_DFL: 当信号产生时, 默认处理方式
void handler(int sig): 当信号产生时, 自定义处理方式
return: 
成功: 返回函数的地址, 为此信号上一次注册的信号处理函数地址
失败: SIG_ERR
*/
```

**以自定义函数方式处理信号**

```c
/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void handler(int sig);

int main(void) {
  if (signal(SIGINT, handler) == SIG_ERR) {
    perror("fail to handle\n");
    exit(1);
  }
  if (signal(SIGQUIT, handler) == SIG_ERR) {
    perror("fail to handle\n");
    exit(1);
  }
  if (signal(SIGUSR1, handler) == SIG_ERR) {
    perror("fail to handle\n");
    exit(1);
  }
  raise(SIGUSR1);
  while (1) {
    printf("main is running\n");
    sleep(1);
  }
}

void handler(int sig) {
  if (sig == SIGINT) {
    printf("SIGINT is handling\n");
  }
  if (sig == SIGQUIT) {
    printf("SIGQUIT is handing\n");
  }
  if (sig == SIGUSR1) {
    printf("SIGUSR1 is handling\n");
  }
}

/*
SIGUSR1 is handling
main is running
main is running
^CSIGINT is handling
main is running
main is running
^\SIGQUIT is handing
main is running
*/
```

**`signal`函数返回值**

```c
/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

typedef void (*sighandler_t)(int);

sighandler_t ret_handler = NULL;
void handler(int sig);

int main(void) {
  if ((ret_handler = signal(SIGINT, handler)) == SIG_ERR) {
    perror("fail to handle\n");
    exit(1);
  }
  while (1) {
    printf("main is running ...\n");
    sleep(1);
  }

  return 0;
}

void handler(int sig) {
  printf("%s: has been called\n", __func__);
  if (signal(SIGINT, ret_handler) == SIG_ERR) {
    perror("fail to handle\n");
    exit(1);
  }
}

/*
main is running ...
main is running ...
^Chandler: has been called
main is running ...
main is running ...
^C
*/
```

##### 可重入函数

可重入函数是指函数可以由多个任务并发使用, 而不必担心数据错误. 即可以被中断的函数, 
可以在任何时刻中断, 并执行另一块代码, 执行完毕后, 回到原来的代码还可以正常运行. 

可重入函数具备以下特点: 

* 不使用或仅使用线程本地存储的静态数据; 
* 不修改全局变量或共享数据; 
* 不调用非可重入的函数; 

**`sleep`函数**, 当执行信号处理函数之后, 不会回到原来的位置继续睡眠. 

```c
/* Example */

#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <signal.h>

void handler(int sig) {
  printf("handle signal ...\n");
}

int main(void) {
  signal(SIGINT, handler);
  sleep(6);
  alarm(5);
  while (1) {
    printf("process is running ...\n");
    sleep(1);
  }

  return 0;
}

/*
^Chandle signal ...
process is running ...
process is running ...
process is running ...
process is running ...
process is running ...
[1]    8439 alarm      ./sleep_test
*/
```

**`read`函数**, 在等待终端输入时, 如果产生信号并执行信号处理函数, 
可以继续输入数据, 但是之前输入的数据会丢失. 

```c
/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define N 32

void handler(int sig) { printf("handle signal ...\n"); }

int main(void) {
  signal(SIGINT, handler);

  char buffer[N] = {};
  if (read(0, buffer, N) == -1) {
    perror("fail to read\n");
    exit(1);
  }
  printf("buffer = %s\n", buffer);

  return 0;
}

/*
hello^Chandle signal ...
world!
buffer = world!

*/
```

##### 信号集

信号集是用来表示多个信号的数据类型. 

```c
#include <signal.h>

int sigemptyset(sigset_t *set);

/*
初始化由set指向的信号集, 清除其中所有的信号, 即初始化一个空信号集. 
set: 信号集标示的地址; 
return: 成功(0); 失败(-1);
*/

int sigfillset(sigset_t *set);

/*
初始化信号集set, 将信号集设置为所有信号的集合. 
set: 信号集标示的地址; 
return: 成功(0); 失败(-1);
*/

int sigaddset(sigset_t *set, int signum);

/*
将信号signum加入到信号集set中. 
set: 信号集标示的地址; 
signum: 信号;
return: 成功(0); 失败(-1);
*/

int sigdelset(sigset_t *set, int signum);

/*
将信号signum从信号集中删除. 
set: 信号集标示的地址; 
signum: 信号;
return: 成功(0); 失败(-1);
*/

int sigismember(const sigset_t *set, int signum);

/*
查询signum是否在信号集set中. 
set: 信号集标示的地址; 
signum: 信号;
return: 成功(1/0); 失败(-1);
*/
```

```c
/* Example */

#include <signal.h>
#include <stdio.h>

int main(void) {
  sigset_t set = {};
  int ret = 0;

  // initialize an empty sigset
  sigemptyset(&set);
  ret = sigismember(&set, SIGINT);
  if (ret == 0) {
    printf("SIGINT is not a member of sigset\n");
  } else {
    printf("SIGINT is a member of sigset\n");
  }
  // add signal
  sigaddset(&set, SIGINT);
  sigaddset(&set, SIGQUIT);
  ret = sigismember(&set, SIGINT);
  if (ret == 0) {
    printf("SIGINT is not a member of sigset\n");
  } else {
    printf("SIGINT is a member of sigset\n");
  }

  return 0;
}

/*
SIGINT is not a member of sigset
SIGINT is a member of sigset
*/
```

##### 信号阻塞集

每个进程都有一个阻塞集, 用来描述哪些信号发送到该进程时被阻塞. 
(在信号发生时候记住, 直到进程准备好时, 再将信号通知进程); 

所谓的阻塞并不是禁止传送信号, 而是暂缓信号的传送. 
若将被阻塞的信号从信号阻塞集中删除, 且对应的信号在被阻塞时发生, 进程会收到相应的信号. 

```c
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);

/*
检查或修改信号阻塞集, 根据how指定的方法对进程信号阻塞集进行修改, 
新的信号阻塞集由set指定, 而原先的信号阻塞集由oldset保存. 

how: 信号阻塞集的修改方法
SIG_BLOCK: 向信号阻塞集中添加set信号集
SIG_UNBLOCK: 从信号阻塞集中删除set信号集
SIG_SETMASK: 将信号阻塞集设置为set信号集

return: 成功(0); 失败(-1)
*/

/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
  sigset_t set = {};
  sigemptyset(&set);
  sigaddset(&set, SIGINT);

  while (1) {
    sigprocmask(SIG_BLOCK, &set, NULL);
    printf("SIGINT is in sigblock set\n");
    sleep(3);
    sigprocmask(SIG_UNBLOCK, &set, NULL);
    printf("SIGINT is not in sigblock set\n");
    sleep(3);
  }

  return 0;
}

/*
阻塞后退出
SIGINT is in sigblock set
SIGINT is not in sigblock set
SIGINT is in sigblock set
^C

立即退出
SIGINT is in sigblock set
SIGINT is not in sigblock set
^C
*/
```

#### 管道

##### 无名管道

管道又称无名管道, 无名管道是一种特殊类型的文件, 在应用层体现为两个打开的文件描述符. 

> 任何一个进程在创建的时候, 系统都会为其分配虚拟内存, 虚拟内存又分为用户空间和内核空间, 
内核空间是所有进程公有的, 无名管道就是创建在内核空间的, 故可以使用无名管道进行通信. 

无名管道虽然在内核空间创建, 但会给当前进程提供两个文件描述符, 即读和写操作. 

**管道的特点**: 

* 半双工, 数据在同一时刻只能在一个方向上流动;
* 数据只能从管道的一端写入, 从另一端读出;
* 写入管道中的数据遵循先入先出的规则;
* 管道传输的数据是无格式的, 这要求管道的读写方事先约定好数据的格式;
* 管道不是普通的文件, 不属于某个文件系统, 只存在于内存中;
* 管道在内存中对应一个缓冲区;
* 从管道读数据是一次性操作, 数据一旦被读走, 就会被管道抛弃, 释放空间读取别的数据;
* 管道没有名字, 只能在具有公共祖先的进程之间使用;

**创建无名管道**

```c
#include <unistd.h>

int pipe(int pipefd[2]);

/*
创建一个无名管道, 给出两个文件描述符进行读写操作

pipefd[0]: 读操作
pipefd[1]: 写操作
return: 成功(0); 失败(-1)
*/
```

**无名管道实现进程间通信**

```c
/* Example */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>

#define N 32

int main(int argc, char *argv[]) {
  int pipefd[2] = {};
  // create a pipe
  if (pipe(pipefd) == -1) {
    perror("fail to create a pipe\n");
    exit(1);
  }

  pid_t pid = fork();
  if (pid < 0) {
    perror("fail to create a process\n");
    exit(1);
  } else if (pid > 0) {
    char buffer[N] = {};
    while (1) {
      fgets(buffer, sizeof(buffer), stdin);
      buffer[N - 1] = '\0';
      if (write(pipefd[1], buffer, N) == -1) {
        perror("fail to write\n");
        exit(1);
      }
    }
  } else {
    char buffer[N] = {};
    while (1) {
      if (read(pipefd[0], buffer, N) == -1) {
        perror("fail to read\n");
        exit(1);
      }
      printf("%d receive the data: %s", getpid(), buffer);
    }
  }

  return 0;
}

/*
hello world
15854 receive the data: hello world
good game
15854 receive the data: good game
^C
*/
```

> 利用无名管道进行进程间通信, 都是父进程创建无名管道, 然后再创建子进程, 
子进程继承父进程的无名管道的文件描述符, 从而实现父进程与子进程间通信. 

**无名管道读写规律**

* 读写端都存在, 只读不写, 如果管道中有数据, 会正常读取数据, 如果管道中没有数据, 
则读操作会阻塞等待, 直到管道中有数据为止. 

* 读写端都存在, 只写不读, 如果一直执行写操作, 则无名管道对应的缓冲区会被写满, 
写满之后, write函数也会阻塞等待. 

* 只有读端, 没有写端, 如果原本管道中有数据, 则读操作正常读取数据, 
否则`read`函数返回0. 

* 只有写端, 没有读端, 一旦执行写操作, 就会产生一个管道破裂的信号, 
这个信号的默认处理方式是退出进程. 

```c
/* Example */

#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void handler(int sig) {
  printf("SIGPIPE %s\n", __func__);
  exit(1);
}

int main(int argc, char *argv[]) {
  signal(SIGPIPE, handler);
  const int N = 32;

  int pipefd[2] = {};
  if (pipe(pipefd) == -1) {
    perror("fail to create a pipe\n");
    exit(1);
  }
  close(pipefd[0]);
  if (write(pipefd[1], "hello world\n", N) == -1) {
    perror("fail to write\n");
    exit(1);
  }

  return 0;
}

/* SIGPIPE handler */
```

##### `fcntl`函数

`fcntl`是用于对文件描述符进行操作的系统调用. 可以用于执行各种操作, 
例如设置文件描述符的属性、获取文件描述符的状态以及对文件描述符进行锁定. 

```c
#include <fcntl.h>

int fcntl(int fd, int cmd, .../* arg */);

/*
fd: 文件描述符, 表示要进行操作的文件
cmd: 要执行的操作命令
F_DUPFD: 复制文件描述符
F_GETFD: 获取文件描述符的标志
F_SETFD: 设置文件描述符的标志
F_GETFL: 获取文件状态标志
F_SETFL: 设置文件状态标志
F_GETLK: 获取文件锁定状态
F_SETLK: 设置文件锁定状态
F_SETLKW: 设置文件锁定状态
arg: 额外参数
*/

// 设置为阻塞
// 管道中没有数据, read会一直等待, 直到有数据. 
fcntl(fd, F_SETFL, 0);

// 设置为非阻塞
// 如果有数据, read正常读取数据, 否则, read立即返回. 
fcntl(fd, F_SETFL, O_NONBLOCK);
```

**`fcntl`函数设置文件的阻塞性**

```c
/* Example */

#include <errno.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
  int pipefd[2] = {};
  char buffer[] = "what can I say";
  pid_t pid = 0;

  if (pipe(pipefd) == -1) {
    perror("fail to create a pipe\n");
    exit(1);
  }

  pid = fork();
  if (pid < 0) {
    perror("fail to create a process\n");
    exit(1);
  } else if (pid > 0) {
    while (1) {
      sleep(5);
      write(pipefd[1], buffer, strlen(buffer));
    }
  } else {
    // 设置为非阻塞
    fcntl(pipefd[0], F_SETFL, O_NONBLOCK);
    while (1) {
      memset(buffer, 0, sizeof(buffer));
      if (read(pipefd[0], buffer, sizeof(buffer)) == -1) {
        if (errno != EAGAIN && errno != EWOULDBLOCK) {
          perror("fail to read\n");
          exit(1);
        }
      }
      printf("buffer = %s\n", buffer);
      sleep(1);
    }
  }

  return 0;
}

/*
buffer = 
buffer = 
buffer = 
buffer = 
buffer = 
buffer = what can I say
buffer = 
buffer = 
^C
*/
```

##### 文件描述符

文件描述符是一个非负整数, 是文件的标识. 用户使用`open`打开一个文件时, 
内核会返回一个文件描述符. 每个进程都有一张文件描述符的表, 进程被创建时, 
标准输入、标准输出、标准错误输出对应的文件描述符为0、1、2. 

> Linux中一个进程最多打开`NR_OPEN_DEFAULT = 1024`个文件. 

```c
/* Example */

#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>

int main(void) {
  int fd = open("test.txt", O_RDONLY | O_CREAT, 0664);
  if (fd < 0) {
    perror("fail to open the file\n");
    exit(1);
  }
  printf("fd = %d\n", fd);

  return 0;
}

/* fd = 3 */
```

**`dup`函数**

`dup`函数复制旧文件的文件描述符, 并分级一个新的文件描述符, 
新的文件描述符是调用进程文件描述符表中最小可用的文件描述符. 

```c
#include <unistd.h>

int dup(int oldfd);

/*
oldfd: 要复制的文件描述符
return: 成功(新的文件描述符); 失败(-1)
*/

/* Example */

#include <stdio.h>
#include <string.h>
#include <unistd.h>

int main(void) {
  char buffer[] = "what can I say\n";
  // 通过dup复制一个标准输出文件
  int fd = dup(1);

  printf("fd = %d\n", fd);
  write(fd, buffer, strlen(buffer));

  return 0;
}

/*
fd = 3
what can I say
*/
```

**`dup2`函数**

`dup2`函数复制一份打开的文件描述符`oldfd`, 并分配新的文件描述符`newfd`, 
`newfd`也标识`oldfd`所标识的文件. 

*`newfd`是小于文件描述符最大允许值的非负整数, 如果`newfd`是一个已经打开的文件描述符, 
则首先关闭该文件, 然后再复制*

```c
#include <unistd.h>

int dup2(int oldfd, int newfd);

/*
oldfd: 要复制的文件描述符
newfd: 分配的新的文件描述符
return: 成功(newfd); 失败(-1)
*/

/* Example */

#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
  int fd1 = 0;
  int fd2 = 3;

  dup2(1, fd2);
  printf("fd2 = %d\n", fd2);
  fd1 = open("test.txt", O_CREAT | O_RDWR, 0664);
  // 关闭文件描述法1, 将fd1复制一份给1
  dup2(fd1, 1);
  // 输出到test.txt文件
  printf("what can I say\n");
  // 关闭文件描述符1, 将fd2复制一份给1
  dup2(fd2, 1);
  // 输出到stdin
  printf("what can I say\n");

  return 0;
}

/*
fd2 = 3
what can I say
*/
```

##### 有名管道

**有名管道(`FIFO`)的特点**: 

* 半双工, 数据在同一时刻只能在一个方向上流动;
* 写入`FIFO`中的数据遵循先入先出的规则;
* `FIFO`传输的数据是无格式的, 这要求`FIFO`的读写方事先约定好数据的格式;
* `FIFO`在文件系统中作为一个特殊文件而存在且在文件系统中可见, 
故有名管道可以实现不相关的进程间通信, 但`FIFO`的内容却可以存放在内存中; 
* 有名管道在内存中对应一个缓冲区;
* 从`FIFO`读数据是一次性操作, 数据一旦被读走, 就会被管道抛弃, 释放空间读取别的数据;
* 当使用`FIFO`的进程退出后, `FIFO`文件将继续保存在文件系统中以便以后使用; 
* `FIFO`有名字, 不相关的进程可以通过打开有名管道进行通信;

**有名管道的创建**

```c
// mkfifo fifo (shell命令)

#include <sys/types.h>
#include <sys/stat.h>

int mkfifo(const char *pathname, mode_t mode);

/*
pathname: 有名管道创建后生成的文件
mode: 管道文件的权限, 如0664
return: 成功(0); 失败(-1)
*/
```

**有名管道读写操作**

```c
/* Example */

#include <errno.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
  // create fifo
  if (mkfifo("myfifo", 0664) == -1) {
    if (errno != EEXIST) {
      perror("fail to create fifo\n");
      exit(1);
    }
  }

  int fd = open("myfifo", O_RDWR);
  if (fd == -1) {
    perror("fail to open\n");
    exit(1);
  }
  // write
  char message[] = "what can I say";
  if (write(fd, message, strlen(message)) == -1) {
    perror("fail to write\n");
    exit(1);
  }
  // read
  char buffer[32] = {};
  if (read(fd, buffer, sizeof(buffer)) == -1) {
    perror("fail to read\n");
    exit(1);
  }
  printf("buffer = %s\n", buffer);
  // close
  close(fd);

  return 0;
}

/* buffer = what can I say */
```

**有名管道进程间通信**

* **发送端(`send`)**

```c
/* Example */

#include <errno.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

#define N 128

int main(int argc, char *argv[]) {
  if (mkfifo(argv[1], 0664) == -1) {
    if (errno != EEXIST) {
      perror("fail to create fifo\n");
      exit(1);
    }
  }

  if (mkfifo(argv[2], 0664) == -1) {
    if (errno != EEXIST) {
      perror("fail to create fifo\n");
      exit(1);
    }
  }

  // open fifo
  int fd_w = open(argv[1], O_WRONLY);
  if (fd_w == -1) {
    perror("fail to open fifo\n");
    exit(1);
  }
  int fd_r = open(argv[2], O_RDONLY);
  if (fd_r == -1) {
    perror("fail to open fifo\n");
    exit(1);
  }

  char buffer[N] = {};

  while (1) {
    memset(buffer, 0, N);
    fgets(buffer, N, stdin);
    buffer[N - 1] = '\0';
    // send message
    if (write(fd_w, buffer, N) == -1) {
      perror("fail to send\n");
      exit(1);
    }
    // receive message
    memset(buffer, 0, N);
    if (read(fd_r, buffer, N) == -1) {
      perror("fail to receive\n");
      exit(1);
    }
    printf("receive message: %s", buffer);
  }

  return 0;
}
```

* **接收端(`recv`)**

```c
/* Example */

#include <errno.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

#define N 128

int main(int argc, char *argv[]) {
  if (mkfifo(argv[1], 0664) == -1) {
    if (errno != EEXIST) {
      perror("fail to create fifo\n");
      exit(1);
    }
  }

  if (mkfifo(argv[2], 0664) == -1) {
    if (errno != EEXIST) {
      perror("fail to create fifo\n");
      exit(1);
    }
  }

  int fd_r = open(argv[1], O_RDONLY);
  if (fd_r == -1) {
    perror("fail to open\n");
    exit(1);
  }
  int fd_w = open(argv[2], O_WRONLY);
  if (fd_w == -1) {
    perror("fail to open\n");
    exit(1);
  }

  char buffer[N] = {};

  while (1) {
    memset(buffer, 0, N);
    if (read(fd_r, buffer, N) == -1) {
      perror("fail to read\n");
      exit(1);
    }
    printf("receive message: %s", buffer);
    memset(buffer, 0, N);
    fgets(buffer, N, stdin);
    buffer[N - 1] = '\0';
    if (write(fd_w, buffer, N) == -1) {
      perror("fail to write\n");
      exit(1);
    }
  }

  return 0;
}
```

**有名管道读写规律**

* 读写端都存在, 只读不写, 如果管道中有数据, 会正常读取数据, 如果管道中没有数据, 
则读操作会阻塞等待, 直到管道中有数据为止. 

* 读写端都存在, 只写不读, 如果一直执行写操作, 则无名管道对应的缓冲区会被写满, 
写满之后, write函数也会阻塞等待. 

* 只有读端, 没有写端, 会在`open`函数位置被阻塞. 

* 只有写端, 没有读端, 会在`open`函数位置被阻塞. 

### 线程

每个进程都有自己的数据段、代码段和堆栈段, 这就造成了进程在创建、切换、撤销操作时, 
需要较大的系统开销, 为了减少系统开销, 从进程中演化出了线程. 

线程存在于进程中, 共享进程的资源. 线程是进程中独立控制流(具有独立执行的一条路径). 
由环境和一系列的执行指令组成. 每个进程都有一个地址空间和一个控制线程. 

**线程和进程的比较**

* 线程是CPU调度和分配的基本单位, 进程是系统中程序执行和资源分配的基本单元. 
线程一般不拥有资源(除程序计数器, 寄存器, 栈), 但可以访问其所属进程的资源. 
* 同一个进程中的多个线程可以共享同一地址空间; 
在进程切换时涉及到整个当前进程CPU环境的保存以及新被调度运行的进程的CPU环境设置, 
而线程切换只需要保存和设置少量的寄存器内容, 并不涉及存储器管理方面的操作, 
有效地使用系统资源和提高系统吞吐量. 
* 不仅进程间可以并发执行, 在一个进程中的多个线程间也可以并发执行. 

> 一个进程可以创建多个线程, 多个线程可以共享一个进程资源. 
每个进程创建的时候系统会分配虚拟内存(用户空间和内核空间), 当进程切换时, 
用户空间也会切换, 所以会增加系统开销, 而一个进程中的多个线程共享一个进程的资源, 
所以线程切换时不会切换这些资源, 从而效率更高. 

**多线程的用途**

* 多任务程序的设计; 
* 并发程序的设计; 
* 网络程序的设计; 
* 数据共享; 
* 多个CPU, 实现并行处理; 

#### 线程的基本操作

##### 线程的创建

`pthread_create`函数用于创建一个新的子线程

```c
#include <pthread.h>

int pthread_create(pthread_t *thread, const pthread_attr_t *attr, 
                    void *(*start_routine) (void *), void *arg);

/*
thread: 线程id
attr: 线程的属性, 默认属性(NULL)
start_routine: 线程处理函数(当前函数执行完毕, 子线程执行完毕)
arg: 线程处理函数的参数
*/
```

> 与`fork`函数不同, `pthread_create`函数创建的线程不与主线程在同一点开始执行, 
而是从指定的处理函数开始运行, 该函数执行完毕后线程退出. 
线程依赖进程的存在, 如果创建线程的进程结束, 线程也就结束.

```c
/* Example */

#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

void *run(void *arg);

int main(int argc, char *argv[]) {
  // create child thread
  pthread_t thread = 0;
  if (pthread_create(&thread, NULL, run, NULL) != 0) {
    perror("fail to create child thread\n");
    exit(1);
  }
  while (1) {
    printf("main thread is running ...\n");
    sleep(1);
  }

  return 0;
}

void *run(void *arg) {
  while (1) {
    printf("child thread is running ...\n");
    sleep(1);
  }

  return NULL;
}

/*
main thread is running ...
child thread is running ...
main thread is running ...
child thread is running ...
child thread is running ...
main thread is running ...
^C
*/
```

> 线程处理函数是并行执行的, 是来回交替执行的. 

**线程处理函数传参**

```c
/* Example */

#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int n = 200;

void *run1(void *arg);
void *run2(void *arg);

int main(int argc, char *argv[]) {
  printf("main thread is running ...\n");
  int k = 100;
  // create thread
  pthread_t thread1 = 0;
  pthread_t thread2 = 0;

  if (pthread_create(&thread1, NULL, run1, (void *)&k) != 0) {
    perror("fail to create thread1\n");
    exit(1);
  }
  if (pthread_create(&thread2, NULL, run2, (void *)&k) != 0) {
    perror("fail to create thread2\n");
    exit(1);
  }
  while (1) {
  }

  return 0;
}

void *run1(void *arg) {
  printf("thread 1: n = %d\n", n);
  n++;
  printf("thread 1: k = %d\n", *(int *)arg);
  *(int *)arg *= 3;

  return NULL;
}

void *run2(void *arg) {
  sleep(1);
  printf("thread 2: n = %d\n", n);
  printf("thread 2: k = %d\n", *(int *)arg);

  return NULL;
}

/*
main thread is running ...
thread 1: n = 200
thread 1: k = 100
thread 2: n = 201
thread 2: k = 300
^C
*/
```

### 网络编程

#### 网络分层结构

为了实现不同类型的计算机, 不同类型的操作系统之间进行通信, 引入了分层的概念. 
最早的分层体系结构是OSI开放系统互联模型, 是由国际化标准组织(ISO)指定的, 
由于OSI过于复杂, 所以到现在为止也没有适用, 而使用的是TCP/IP协议族.
OSI一共分为7层, TCP/IP协议族一共4层, 虽然TCP/IP协议族层数少, 
但却干了OSI7层的所有任务. 

**OSI 7层**

1. 物理层: 负责传输比特流, 处理物理接口和介质; 
2. 数据链路层: 提供可靠的点对点数据传输, 通过帧来管理物理地址和错误检测; 
3. 网络层: 处理分组传输, 包括路由和逻辑寻址, 以便在网络中正确地传输数据; 
4. 传输层: 提供端到端的数据传输, 通常使用传输控制协议(TCP)或用户数据报协议(UDP); 
5. 会话层: 建立、管理和终止会话连接, 允许不同设备之间的通信; 
6. 表示层: 处理数据的表示和转换, 包括加密、压缩和格式转换; 
7. 应用层: 提供应用程序之间的通信服务, 例如电子邮件, 文件传输和远程访问; 

**TCP/IP 4层**

1. 网络接口层: 类似于OSI模型的物理层和数据链路层, 处理物理接口和网络访问; 
2. 网络层: 相当于OSI模型的网络层, 处理分组的传输和逻辑寻址, 以及IP协议的功能; 
3. 传输层: 与OSI模型的传输层相对应, 提供端到端的数据传输(TCP/UDP); 
4. 应用层: 与OSI模型的会话层、表示层和应用层相对应, 提供应用程序之间的通信服务; 

#### 网络字节序转换

字节序指多字节数据的存储顺序, 分为小端格式和大端格式.

* 小端格式: 将低位字节数据存储在低地址;

* 大端格式: 将高位字节数据存储在低地址;

> 低地址(LSB), 高地址(MSB).

##### 判断当前系统字节序

```c
/* Example */

#include <stdio.h>

union ByteOrder {
  int m;
  char n;
};

int main(void) {
  union ByteOrder byteOrder;

  byteOrder.m = 0x12345678;
  printf("m == %#x\n", byteOrder.m);
  printf("n == %#x\n", byteOrder.n);

  if (byteOrder.m == 0x78) {
    printf("Little Endian Format\n");
  } else {
    printf("Big Endian Format\n");
  }

  return 0;
}

/*
m == 0x12345678
n == 0x78
Big Endian Format
*/
```

##### 字节序转换函数

网络协议指定了通信字节序(大端格式), 只有在多字节数据处理时才需要考虑字节序. 
运行在同一台计算机上的进程相互通信时, 一般不用考虑字节序; 异构计算机之间通信, 
需要转换字节序为网络字节序.

**host字节序转network字节序**

```c
#include <arpa/inet.h>

uint32_t htonl(uint32_t hostint32);

/*
将32位主机字节序数据转为网络字节序数据
hostint32: 待转换的32位主机字节序数据
return: 网络字节序数据的值
*/

uint16_t htons(uint16_t hostint16);

/*
将16位主机字节序数据转为网络字节序数据
hostint16: 待转换的16位主机字节序数据
return: 网络字节序数据的值
*/
```

**network字节序转host字节序**

```c
#include <arpa/inet.h>

uint32_t ntohl(uint32_t netint32);

/*
将32位网络字节序数据转为主机字节序数据
hostint32: 待转换的32位网络字节序数据
return: 主机字节序数据的值
*/

uint16_t ntohs(uint16_t netint16);

/*
将16位网络字节序数据转为主机字节序数据
hostint16: 待转换的16位网络字节序数据
return: 主机字节序数据的值
*/
```

```c
/* Example */

#include <arpa/inet.h>
#include <stdio.h>

int main(void) {
  int m = 0x12345678;
  short n = 0x1234;

  printf("host --> network: m = %#x\n", htonl(m));
  printf("host --> network: n = %#x\n", htons(n));

  return 0;
}

/*
host --> network: m = 0x78563412
host --> network: n = 0x3412
*/
```

#### 网络地址转换

一般见到的IP地址是点分十进制的字符串形式, 但是计算机或网络中识别的IP地址是整形数据, 
所以需要将IP地址字符串转换为整形数据. 

##### 地址转换函数

**`inet_pton`**

将点分十进制字符串转成32位无符号整形.

```c
#include <arpa/inet.h>

int inet_pton(int family, const char *strptr, void *addrptr);

/*
family: 协议族(AF_INET/AF_INET6)
strptr: 点分十进制字符串
addrptr: 32位无符号整形地址
return: 1(成功) / !1(失败)
*/

#include <arpa/inet.h>
#include <stdio.h>

int main(void) {
  char ipStr[] = "192.168.100.100";
  unsigned int ipInt = 0;
  unsigned char *ipPtr = NULL;

  inet_pton(AF_INET, ipStr, &ipInt);
  ipPtr = (unsigned char *)&ipInt;
  printf("ipInt: %d\n", ipInt);
  printf("ip: %d.%d.%d.%d\n", *ipPtr, *(ipPtr + 1), *(ipPtr + 2), *(ipPtr + 3));

  return 0;
}

/*
ipInt: 1684318400
ip: 192.168.100.100
*/
```

**`inet_ntop`**

将32位无符号整形转换为点分十进制字符串.

```c
#include <arpa/inet.h>

const char *inet_ntop(int family, const void *addrptr, char *strptr, size_t len);

/*
family: 协议族
addrptr: 32位无符号整形
strptr: 点分十进制字符串
len: strptr缓存区长度
len宏定义
#define INET_ADDRSTRLEN 16 // IPV4
#define INET6_ADDRSTRLEN 46  // IPV6
return: 返回字符串的首地址 / NULL
*/

#include <arpa/inet.h>
#include <stdio.h>

int main(void) {
  unsigned char ipInt[] = {192, 168, 100, 100};
  char ipStr[INET_ADDRSTRLEN] = {};

  inet_ntop(AF_INET, &ipInt, ipStr, INET_ADDRSTRLEN);
  printf("IP: %s\n", ipStr);

  return 0;
}

/*
IP: 192.168.100.100
*/
```

**`inet_addr`和`inet_ntoa`**

只能用于IPV4地址转换. 

```c
#include <arpa/inet.h>

in_addr_t inet_addr(const char *cp);

/*
将点分十进制IP转换为整形
cp: 点分十进制IP地址
return: 整形数据
*/

char *inet_ntoa(struct in_addr in);

/*
将整形转换为点分十进制的IP
in: 保存ip地址的结构体
return: 点分十进制IP地址字符串
*/
```
