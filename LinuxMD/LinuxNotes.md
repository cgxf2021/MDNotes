# Linux 笔记

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

**创建进程**

使用`fork`函数可以在已有的进程基础上创建一个子进程.

```c
pid_t fork(void);

/*
return: 成功(>=0); 失败(-1)
(>0): 子进程的<进程号>, 父进程代码区
(=0): 子进程代码区
*/
```

使用`fork`函数得到的子进程是父进程的一个复制品, 继承了父进程整个地址空间.

> 地址空间: 包括进程上下文、进程堆栈、打开的文件描述、信号控制设定、进程优先级、
进程组号等. 子进程独有的只有进程号, 计时器. 因此使用`fork`函数代价很大.

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

**进程睡眠状态**

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

**进程等待函数**

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
