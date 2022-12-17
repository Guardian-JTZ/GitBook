# Linux 访问控制列表 (ACL) 简介

```shell
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #ls -al | grep Temp
drwxrwx---+  2 jtz  jtz    4096 12-р сар 16 19:57 Temp
```

## 查看当前ACL

```shell
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #getfacl Temp/
# file: Temp/
# owner: jtz
# group: jtz
user::rwx
group::rwx
other::---

```

> 这个目录并没有 ACL 因为列出的唯一权限是针对用户、组和其他人

## 设置访问控制列表

```shell
# 语法
setfacl [option] [action/specification] file
# -m 修改
# -x 删除
# -d 默认值
# action/specification : 是指用户和组，后跟我们要设置的权限
# 在设置组 ACL 时需要在组名前加 g: (eg: g:jtz:rwx) 对于用户将 g 改为 u 即可，不过 u 为默认
```

现在我们设置一个默认 ACL

```shell
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #setfacl -d -m jtz:rwx Temp/
# mask 有效权限掩码，此条目限制所有组和指定用户的有效权限，文件所有者和其他人不受此权限影响
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #getfacl Temp/
# file: Temp/
# owner: jtz
# group: jtz
user::rwx
group::rwx
other::---
default:user::rwx
default:user:jtz:rwx
default:group::rwx
default:mask::rwx
default:other::---
```

> 现在在我们当前组添加一个用户 Fred

```shell
┌─[✗]─[root@parrot]─[/home/jtz/Desktop]
└──╼ #useradd fred
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #usermod -g jtz fred
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #cat /etc/passwd | grep 1003
jtz:x:1000:1003:JTZ:/home/jtz:/bin/bash
fred:x:1001:1003::/home/fred:/bin/sh
```

> 我们让 fred 在 Temp 目录中创建一个文件

```shell
┌─[fred@parrot]─[/home/jtz/Desktop/Temp]
└──╼ $touch test
┌─[fred@parrot]─[/home/jtz/Desktop/Temp]
└──╼ $ls -al
总用量 8
drwxrwx---+ 2 jtz  jtz 4096 12-р сар 17 10:17 .
drwxr-xr-x  3 jtz  jtz 4096 12-р сар 17 09:33 ..
-rw-rw----+ 1 fred jtz    0 12-р сар 17 10:17 test
┌─[fred@parrot]─[/home/jtz/Desktop/Temp]
└──╼ $getfacl test 
# file: test
# owner: fred
# group: jtz
user::rw-
user:jtz:rwx			#effective:rw-
group::rwx			#effective:rw-
mask::rw-
other::---
```

> 现在我们创建第三个用户 Kenny ，使用该用户尝试在 Temp 中创建一个文件

```shell
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #useradd Kenny
┌─[✗]─[root@parrot]─[/home/jtz/Desktop]
└──╼ #su Kenny
$ bash
┌─[Kenny@parrot]─[/home/jtz/Desktop]
└──╼ $cd Temp/
bash: cd: Temp/: 权限不够

# 现在我们设置权限让 Kenny 可以创建文件
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #setfacl -m Kenny:rwx Temp/
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #getfacl Temp/
# file: Temp/
# owner: jtz
# group: jtz
user::rwx
user:Kenny:rwx
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:jtz:rwx
default:group::rwx
default:mask::rwx
default:other::---
```

> 现在我们创建 Kenny 自己的文件夹，赋予他所有权，让 jtz 组成为组所有者，以便 jtz 组中其他人可以查看其中内容

```shell
┌─[root@parrot]─[/home/jtz/Desktop]
└──╼ #cd Temp/
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #mkdir Kenny
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #chown Kenny:jtz Kenny/
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #getfacl Kenny/
# file: Kenny/
# owner: Kenny
# group: jtz
user::rwx
user:jtz:rwx
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:jtz:rwx
default:group::rwx
default:mask::rwx
default:other::---
# 现在 Kenny 用户就可以在 Kenny 文件夹中创建文件
```

> 现在 Kenny 文件夹归 jtz 组所有，这个组的所有人都可以将文件放在那里，但是如果我们r让 Kenny 成为首席审计师并对 Fred 保密

```shell
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #setfacl -m fred:- Kenny/
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #getfacl Kenny/
# file: Kenny/
# owner: Kenny
# group: jtz
user::rwx
user:jtz:rwx
user:fred:---
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:jtz:rwx
default:group::rwx
default:mask::rwx
default:other::---
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #su fred
$ bash
┌─[fred@parrot]─[/home/jtz/Desktop/Temp]
└──╼ $cd Kenny/
bash: cd: Kenny/: 权限不够
```

> 如果我们不让所有人查看 Kenny 文件，应该怎么设置

```shell
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #setfacl -m g:jtz:- Kenny/
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #chmod g-rwx Kenny/
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #ls -al
总用量 16
drwxrwx---+ 3 jtz   jtz 4096 12-р сар 17 10:35 .
drwxr-xr-x  3 jtz   jtz 4096 12-р сар 17 09:33 ..
drwx------+ 2 Kenny jtz 4096 12-р сар 17 10:35 Kenny
-rw-rw----+ 1 fred  jtz    0 12-р сар 17 10:17 test
┌─[root@parrot]─[/home/jtz/Desktop/Temp]
└──╼ #getfacl Kenny/
# file: Kenny/
# owner: Kenny
# group: jtz
user::rwx
user:jtz:rwx			#effective:---
group::rwx			#effective:---
group:jtz:---
mask::---
other::---
default:user::rwx
default:user:jtz:rwx
default:group::rwx
default:mask::rwx
default:other::---
┌─[jtz@parrot]─[~/Desktop/Temp]
└──╼ $cd Kenny/
bash: cd: Kenny/: 权限不够
```

## 参考文章

* [An introduction to Linux Access Control Lists (ACLs)](https://www.redhat.com/sysadmin/linux-access-control-lists)
* [getfacl](https://linux.die.net/man/1/getfacl)
* [setfacl](https://linux.die.net/man/1/setfacl)
