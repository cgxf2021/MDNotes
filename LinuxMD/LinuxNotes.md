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
