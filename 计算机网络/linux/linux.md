---
cover: ../../.gitbook/assets/背景5.jpg
coverY: 0
layout: editorial
---

# Linux

> 一些简单又实用的工具：
>
> * [Linux 命令解释](https://explainshell.com/)，该工具可以对要执行的命令的每一个参数作用做出解释

## 一、文件以及目录管理

### 1. 创建和删除文件

```shell
# 创建文件
mkdir files
# 强力删除文件或文件夹
rm -rf file
# 移动文件
mv sourceFile targetFile
# 复制文件[/目录]
cp [-r] sourceFile targetFile
```

### 2. 列出目录项

```shell
# 显示当前目录下所有文件
ls
```

### 3. 查找目录或文件

```shell
# 实时查找
find
# 使用索引数据库查找
locate
```

### 4. 查看文件内容

```shell
# 查看文件
cat filename
# 按页显示列表内容
ls -al | more
# 只看前 n 行
head -n filename
# 显示文件倒数第五行
tail -5 filename
# 查看文件间差别
diff file1 file2
```

### 5. 查找文件内容

```shell
# 使用 grep 查找文件内容
```

### 6. 文件或目录权限修改

```shell
# 改变文件所有者
chown
# 改变文件的权限
chmod
# 递归子目录修改
chown -R tuxapp source/
```

### 7. 管道和重定向

```shell
# 批处理命令连接执行 使用 |
# 串联 使用 ;
# 前面成功，则执行后面一条，否则，不执行:&&
# 前面失败，则后一条执行: ||
```

### 8. 打包/解包、压缩/解压缩

```shell
# 打包
tar -cvf etc.tar /etc
# 解包
tar -xvf demo.tar
# 压缩
gzip demo
# 解压
gunzip demo.zip
```

### 9. lsof 查看系统文件

具体使用，查看链接文章 --> [链接跳转](https://linuxtools-rst.readthedocs.io/zh\_CN/latest/tool/lsof.html)

> 1. 作用： 是一个查看当前系统文件的工具
> 2. 支持打开的类型：
> 3. 普通文件
> 4. 目录
> 5. 网络文件系统的文件
> 6. 字符或设备文件
> 7. (函数)共享库
> 8. 管道，命名管道
> 9. 符号链接
> 10. 网络文件（例如：NFS file、网络socket，unix域名socket）
> 11. 还有其它类型的文件，等等

## 二、文本处理

### 1. Find 文件查找

```shell
find --help
find [-H] [-L] [-P] [-Olevel] [-D debugopts] [path...] [expression]
	-name  # 按照文件名称查找
	-user  # 按照文件拥有者查找
	-group # 按照文件拥有组查找
	-type  # 按文件类型查找
	-perm  # 按文件权限查找
	-exec  # 对查找的文件执行指定动作
	-maxdepth/ -mindepth # 指定查找范围最大/最小为几级目录
	-cmin  # 按照文件修改时间查找
	-size  # 按文件大小查找
	-o/-a/-not # 查找条件 或/且/非
```

### 2. grep 文本搜索

### 3. xargs 命令行参数转换

> xargs 能够将输入数据转化为特定命令的命令行参数；这样，可以配合很多命令来组合使用。比如grep，比如find； - 将多行输出转化为单行输出

```shell
# 将单行转化为多行输出
cat single.txt | xargs -n 3
```

### 4. sort 排序

### 5. uniq 消除重复行

```shell
# 消除重复行
sort unsort.txt | uniq
# 统计各行在文件中出现的次数
sort unsort.txt | uniq -c
# 统计重复行
sort unsort.txt | uniq -d
```

### 6. tr 进行转换

### 7. cut 按列切分文本

### 8. paste 按列拼接文本

### 9. wc 统计行和字符的工具

```shell
wc -l file // 统计行数
wc -w file // 统计单词数
wc -c file // 统计字符数
```

### 10. sed 文本替换器

### 11. awk 数据流处理工具

## 三、进程管理工具

### 1. 查询进程

```shell
# 查看正在运行的进程信息
ps -ef
# 显示进程信息，并实时更新
top
# 使用 lsof
lsof
```

> ps 命令列出的是当前进行的快照，ps 食用文章 --> [跳转链接](https://linuxtools-rst.readthedocs.io/zh\_CN/latest/tool/ps.html)

### 2. 终止进程

```shell
# 杀死指定进程
kill PID
# 杀死相关进程
kill -9 3434
# 杀死 job 用户的全部工作
kill %job
```

| 状态   | 状态码 | 描述                                              |
| ---- | --- | ----------------------------------------------- |
| 运行   | R   | 正在运行或在运行队列中等待                                   |
| 中断   | S   | 休眠中, 受阻, 在等待某个条件的形成或接受到信号                       |
| 不可中断 | D   | 收到信号不唤醒和不可运行, 进程必须等待直到有中断发生                     |
| 僵死   | Z   | 进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放          |
| 停止   | T   | 进程收到SIGSTOP, SIGTSTP, SIGTTIN, SIGTTOU信号后停止运行运行 |

### 3. 进程监控

> top 以及 htop 的食用教程 --> [跳转链接](https://linuxtools-rst.readthedocs.io/zh\_CN/latest/tool/top.html)

```shell
# 实时显示系统中各个进程的资源占用情况
top
# top 的加强版本， Linux 交互式的进程浏览器
htop
```

### 4. 分析线程栈

```shell
# 使用命令pmap，来输出进程内存的状况，可以用来分析线程堆栈
pmap PID
# pstack 可显示每个进程的栈跟踪，必须由相应进程的宿主或 root 运行
pstack PID
```

### 5. crontab 定时任务

> * 执行时间的计算 --> [网页跳转](https://tool.lu/crontab)

```shell
# -u   #<用户名称> 是指设定指定<用户名称>的定时任务，这个前提是要有其权限，默认是设置自己定时任务
# -l 　#列出该用户的定时任务设置。
# -r 　#删除该用户的定时任务设置。
# -e 　#编辑该用户的定时任务设置。
crontab [-u <用户名称>][配置文件] 或 crontab { -l | -r | -e }

# 使用
crontab -e  # 编辑定时任务
i #默认文字编辑器为vim，按i字母键即可添加cron任务
30 3 * * * /usr/local/etc/rc.d/lighttpd restart #将命令代码放入，此命令意义为每天的03:30 重启apache
ESC #按ESC键退出编辑模式
:wq #键入:wq保存
service crond restart #重启crontab服务
```

## 四、性能监控

### 1. 监控 CPU

```shell
# 查看 CPU  使用率
sar -u
# 查看 CPU 凭据负载
sar -q 1 2
```

### 2. 查询内存

```shell
# 查看内存使用状况
sar -r
# 查看内存使用量
free -m
```

### 3. 查询页面交换

```shell
sar -W
```

### 4. 查询硬盘使用

```shell
# 查看磁盘空间利用情况
df -h
# 查询当前目录下空间使用情况
df -sh
# 查看该目录下所有文件夹的排序后的大小
du -sh 'ls'
```

### 5. 综合应用

```shell
#  查看cpu、内存、使用情况： vmstat n m （n 为监控频率、m为监控次数）
vmstat 1 3
# 
watch
```

## 五、网络工具

### 1. 查询网络服务和端口

```shell
# netstat 命令用于显示各种网络相关信息，如网络连接，路由表，接口状态, masquerade 连接，多播成员
# 列出所有端口
netstat -a

# lsof 是一个列出当前系统打开文件的工具
# 查询使用该端口的进程 PID
lsof -i:7902  # 查询使用该端口的进程的 PID

ps -fe | grep 30294 # 查询 PID 的进程详细信息
```

### 2. 网络路由

```shell
# 查看路由状态
route -n

# 探测前往地址IP的路由路径
traceroute IP

# DNS查询，寻找域名domain对应的IP
host domain

# 反向DNS查询
host IP
```

### 3. 镜像下载

```shell
# 下载文件或者网页
# –limit-rate :下载限速
# -o：指定日志文件；输出都写入日志；
# -c：断点续传
wget url
```

### 4. FTP|SFTP|LFTP|SSH

### 5. 网络复制

```shell
scp
```

## 六、系统管理以及 IPC 资源管理

### 1. 系统管理

```shell
# 查询系统版本
uname -a

# 查看硬件信息
sar -u 5 10
```

### 2. ldd 查看程序依赖库

> 查看程序运行所需要的共享库

```shell
# 第一列： 程序需要依赖什么库
# 第二列： 系统提供的与程序需要的库所对应的库
# 第三列： 库加载的开始地址
/opt/app/todeav1/test$ ldd test
    libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x00000039a7e00000)
    libm.so.6 => /lib64/libm.so.6 (0x0000003996400000)
    libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00000039a5600000)
    libc.so.6 => /lib64/libc.so.6 (0x0000003995800000)
    /lib64/ld-linux-x86-64.so.2 (0x0000003995400000)
```

### 3. IPCS 查询进程间通信状态

> IPCS 是 Linux 显示进程间通信设施状态的工具，可以显示消息队列、共享内存和信号量的信息

#### 1. IPC 资源查询

```shell
# 查看系统使用的 IPC 资源
# -m : 查看系统使用的 IPC 共享内存资源
# -q : 查看系统使用的 IPC 队列资源
# -s : 查看系统使用的 IPC 信号量资源
# -l : 系统 IPC 参数查询
ipcs [-m|q|s|l]
```

#### 2. 清除 IPC 资源

> 使用ipcrm 命令来清除IPC资源：这个命令同时会将与ipc对象相关联的数据也一起移除。当然，只有root用户，或者ipc对象的创建者才有这项权利

```shell
ipcrm -M shmkey  移除用shmkey创建的共享内存段
ipcrm -m shmid    移除用shmid标识的共享内存段
ipcrm -Q msgkey  移除用msqkey创建的消息队列
ipcrm -q msqid  移除用msqid标识的消息队列
ipcrm -S semkey  移除用semkey创建的信号
ipcrm -s semid  移除用semid标识的信号

# 清除当前用户创建的所有 IPC 资源
ipcs -q | awk '{ print "ipcrm -q "$2}' | sh > /dev/null 2>&1;
ipcs -m | awk '{ print "ipcrm -m "$2}' | sh > /dev/null 2>&1;
ipcs -s | awk '{ print "ipcrm -s "$2}' | sh > /dev/null 2>&1;
```
