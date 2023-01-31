# PEDA

## 介绍

PEDA 是一个 GDB 的插件，主要功能是高亮显示反汇编代码、寄存器、内存信息，提高 debug 的效率，同时 PEDA 还为 GDB 添加一些实用新的命令，比如 checksec 可以查看程序开启了那些安全机制等

## 安装

> 三选一

### 1. peda

> [链接](https://github.com/longld/peda)

```shell
$ git clone https://github.com/longld/peda.git ~/peda
$ echo "source ~/peda/peda.py" >> ~/.gdbinit 
```

### 2. gef

> [链接](https://github.com/hugsy/gef)

```shell
$ wget -O ~/.gdbinit-gef.py -q https://github.com/hugsy/gef/raw/master/gef.py
$ echo "source ~/.gdbinit-gef.py" >> ~/.gdbinit
```

### 3. pwndbg

> 链接

```shell
git clone https://github.com/pwndbg/pwndbg
cd pwndbg && sudo ./setup.sh
cd ..
mv pwndbg ~/pwndbg-src
echo "source ~/pwndbg-src/gdbinit.py" > ~/.gdbinit_pwndbg
```

### 4. 确认插件是否有效

使用`gdb -q xxx`命令启动调试程序，出现类似字符串，说明已经成功安装

```shell
GEF for linux ready, type `gef' to start, `gef config' to configure
75 commands loaded for GDB 7.11.1 using Python engine 3.5
[*] 5 commands could not be loaded, run `gef missing` to know why.
Reading symbols from execve...done.
```

### 5. 分别启用插件

> 修改`.gdbinit`文件的内容为如下配置：

```shell
define init-peda
source ~/peda/peda.py
end
document init-peda
Initializes the PEDA (Python Exploit Development Assistant for GDB) framework
end

define init-gef
source ~/.gdbinit-gef.py
end
document init-gef
Initializes GEF (GDB Enhanced Features)
end

define init-pwndbg
source ~/.gdbinit_pwndbg
end
document init-pwndbg
Initializes PwnDBG
end
```

此外，在 /usr/bin 文件夹中创建以下 3 个文件：

1.  首先创建 /usr/bin/gdb-peda 并粘贴以下内容：

    ```shell
    #!/bin/sh
    exec gdb -q -ex init-peda "$@"
    ```
2.  /usr/bin/gdb-pwndbg

    ```shell
    #!/bin/sh
    exec gdb -q -ex init-pwndbg "$@"
    ```
3.  /usr/bin/gdb-gef

    ```shell
    #!/bin/sh
    exec gdb -q -ex init-gef "$@"
    ```

## 命令

### 基础命令

```shell
file 路径　-　附加文件
break *0x400100 (b main) - 在 0x400100 处下断点
tb  - 一次性断点
info b - 查看断点信息
enable   -   激活断点
disable  -   禁用断点
delete [number]  -  删除断点
watch *(int *)0x08044530  -  在内存0x0804453处的数据改变时stop
p $eax - 输出eax的内容
set $eax=4 - 修改变量值

c - 继续运行
r - 开始运行
ni - 单步步过
si - 单步步入
fini - 运行至函数刚结束处
return expression - 将函数返回值指定为expression
bt - 查看当前栈帧
info f - 查看当前栈帧
context - 查看运行上下文
stack - 查看当前堆栈
call func - 强制函数调用
stack 100 - 插件提供的，显示栈中100项
find xxx　 - 快速查找，很实用

x/<n/f/u> <addr>     n、f、u是可选的参数。
x /4xg $ebp：查看ebp开始的4个8字节内容
x/wx $esp 　　以4字节16进制显示栈中内容
b表示单字节，h表示双字节，w表示四字 节，g表示八字节
s 按字符串输出
x 按十六进制格式显示变量。
d 按十进制格式显示变量。
u 按十六进制格式显示无符号整型。
o 按八进制格式显示变量。
t 按二进制格式显示变量。
a 按十六进制格式显示变量。
c 按字符格式显示变量。
f 按浮点数格式显示变量。
i：反汇编

但是实际的组合就那么几种：
x/s 地址　　查看字符串
x/wx 地址　　查看DWORD
x/c 地址　　单字节查看
x/16x $esp+12 查看寄存器偏移

set args  - 可指定运行时参数。（如：set args 10 20 30 40 50）
show args  - 命令可以查看设置好的运行参数。
```

### 插件命令

```shell
┌──(jtz㉿JTZ)-[~/Desktop/Temp/Frolic]
└─$ gdb -q ./rop
Reading symbols from ./rop...
(No debugging symbols found in ./rop)
```

*   `aslr` --- 显示/设定GDB的ASLR(地址空间配置随机加载)设置

    ```shell
    gdb-peda$ aslr
    ASLR is OFF
    ```
*   `checksec` - 检查二进制文件的各种安全选项

    ```shell
    gdb-peda$ checksec
    CANARY    : disabled
    FORTIFY   : disabled
    NX        : ENABLED
    PIE       : disabled
    RELRO     : Partial
    ```
* `dumpargs` - 函数将要被调用时，显示将要被传入函数的所有参数(默认会在反汇编代码下方自动显示)
* `dumprop` - 在给定内存范围中Dump出所有ROP gadgets
* `elfheader` - 从被调试的ELF文件中获取标题信息
* `elfsymbol` - 从ELF文件获取非调试符号信息（plt表）
* `lookup` - 搜索所有地址/参考地址属于一个内存范围
* `patch` - 修补程序内存以string / hexstring / int的地址开始
* `procinfo` - 显示/ proc / pid /
* `pshow` - 显示各种PEDA选项和其他设置
* `pset` - 设置各种PEDA选项和其他设置
* `pattern` - 生成字符串模板 写入内存 用于定位溢出点
  * `pattern create size` 生成特定长度字符串
  * `pattern offset value` 定位字符串
* `procinfo` – Display various info from /proc/pid/
* `pshow` – Show various PEDA options and other settings
* `pset`– Set various PEDA options and other settings
* `readelf` - 从ELF文件获取标题信息
* `ropgadget` - 获取二进制或库的通用ROP小工具
* `ropsearch` - 在内存中搜索ROP小工具
* `searchmem` - 用搜索内存
  * `searchmem|find` - 在内存中查找字符串，支持正则表达式，例如\`searchmem "/bin/sh" libc\`\`
* \`\`shellcode\` - 生成或下载常用的shellcode。
* `skeleton` - 生成python漏洞利用代码模板
* `vmmap` - 可以用来查看栈、bss段是否可以执行
* `xormem` - 用一个键异或存储区域
* `ptype struct link_map` - 查看link\_map定义
* `p &((struct link_map*)0)->l_info` - 查看l\_info成员偏移
