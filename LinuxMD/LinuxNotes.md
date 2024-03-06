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

## linux中c编程

### 进程

* PPID: 当前进程的父进程号.
* PID: 当前进程的进程号.
* PGID: 当前进程所在组的进程组号.

Linux 提供了3个获得金称号的函数:

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

`sleep`函数用于让进程进入睡眠状态

```c
unsigned int sleep(unsigned int seconds);
```

> 若进程挂起到指定的时间则返回0, 若有信号中断则返回剩余秒数. 进程挂起指定的秒数后程序并不会立即执行，系统只是将此进程切换到就绪态. 
进程睡眠到指定的秒数后程序并不会立即执行, 系统只是将此进程切换到就绪态.

**进程等待函数**

`wait`函数等待子进程结束, 回收子进程资源.

```c
#include <sys/wait.h>

pid_t wait(int *status);
```

> 调用`wait`函数的进程会挂起, 直到它的一个子进程退出或收到一个不能忽略的信号时才被唤醒. 
若调用进程没有子进程或子进程已经结束, 该函数立即返回. 
