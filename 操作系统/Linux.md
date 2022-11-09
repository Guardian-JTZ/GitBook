# Linux

> 一些简单又实用的工具：
>
> - [Linux 命令解释](https://explainshell.com/)，该工具可以对要执行的命令的每一个参数作用做出解释

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
updatedb # 建立或更新数据库
locate
```

### 4. 查看文件内容

```shell
# 查看文件
cat filename
# 按页显示列表内容
ls -al | more
# 只看前 n 行
head - n filename
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

```shell
```

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
# 显示进程信息，并实时根性
top
# 查看端口占用进程状态
lsof -i:3306
# 查看用户 user 的进程所打开的文件
lsof -u user
# 查看 Init  进程当前打开的文件
lsof -c init
# 查看指定进程 ID (2222) 打开的文件
lsof -p 23295
# 查询指定目录下被进程开启的文件
lsof +d mydir1/
```

### 2. 终止进程

```shell
# 杀死指定进程
kill PID
# 杀死相关进程
kill -9 3434
# 杀死 job 工作
kill %job
```

### 3. 进程监控

```shell
# 查看系统中使用CPU、使用内存最多的进程
top
(->)命令
# P：根据CPU使用百分比大小进行排序。
# M：根据驻留内存大小进行排序。
# i：使top不显示任何闲置或者僵死进程。
```

### 4. 分析线程栈

```shell
# 使用命令pmap，来输出进程内存的状况，可以用来分析线程堆栈
pmap PID
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
# 列出所有 tcp 端口
netstat -at
# 列出所有有监听的服务状态
netstat -l
# 使用netstat工具查询端口
netstat -antp | grep 6379

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

### 5. 网络赋值

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

