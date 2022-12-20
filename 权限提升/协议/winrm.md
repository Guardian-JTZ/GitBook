# WInRM

> 是一个简单的基于命令行的 WIndows 集成远程管理协议， WinRM 使用简单对象访问协议 SOAP 来建立与远程主机及其应用程序的连接。
>
> * 使用 TCP 端口： 5985(HTTP)、5986（HTTPS）

```shell
# nmap 扫描
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

如果我们想知道是否可以通过 WinRM 访问一个或多个远程服务器，我们可以在 Powershell 的帮助下达到，使用 [Test-WsMan](https://learn.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2), 负责此操作，并将相关主机名称传递给他，在 Liux 环境中，我们可以使用名为[evil-winrm](https://github.com/Hackplayers/evil-winrm)的工具，这是另一个旨在与 WinRM 交互的渗透测试工具。
