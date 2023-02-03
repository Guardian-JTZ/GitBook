---
description: 讲解 Impacket 的一些使用
---

# 🔧 Impacket

## 远程执行

| 脚本名         | 脚本介绍                                                                                                |
| ----------- | --------------------------------------------------------------------------------------------------- |
| psexec.py   | 使用了RemComSvc的实现了PSEXEC功能的脚本。                                                                        |
| smbexec.py  | 类似PSECEX的执行方式，但未使用RemComSvc。这个脚本使用了一个本地SMB Server接收返回的结果，可以避免目标SMB没有可写的目录                           |
| atexec.py   | 这个脚本通过MS-TSCH协议控制计划任务在目标机器上执行命令并获得回显                                                                |
| wmiexec.py  | 通过WMI实现了半交互式的Shell，不需要在目标安装任何服务或软件。而且高度隐蔽的以管理员权限运行                                                  |
| dcomexec.py | 类似wmiexec.py的半交互式Shell，但是使用了DCOM接口，目前支持的接口有`MMC20.Application`、`ShellWindows`、`ShellBrowserWindows` |

## Kerberos协议

| 脚本名称               | 介绍                                                                                                      |
| ------------------ | ------------------------------------------------------------------------------------------------------- |
| GetTGT.py          | 提供密码、hash或aeskey用来请求TGT并且保存为ccache格式                                                                    |
| GetST.py           | 提供密码、hash、aeskey或ccache格式的TGT，可以请求服务票据并保存为ccache格式。如果提供的账户存在约束委派且支持协议转换，那么可以使用-impersonate选项模拟为其他用户请求票据 |
| GetPac.py          | 这个脚本会为指定用户请求经过身份验证的PAC，通过使用MS-SFU协议的S4USelf和U2U的Kerberos认证实现                                            |
| GetUserSPNs.py     | 这个脚本会找出和普通用户账户关联的SPN，输出格式与JtR和hashcat兼容                                                                 |
| GetNPUsers.py      | 这个脚本会尝试获得并列出不需要Kerberos域认证(UF\_DONT\_REQUIRE\_PREAUTH)的用户，输出和JtR兼容。                                     |
| rbcd.py            | 这个脚本可以处理目标机器的msDS-AllowedToActOnBehalfOfOtherIdentity属性                                                 |
| ticketConverter.py | 这个脚本可以在mimikatz常用的kirbi文件和Impacket常用的ccache文件之间进行转换                                                     |
| ticketer.py        | 这个脚本可以基于模板或自己创建金、银票据，并允许你自定义PAC\_LOGON\_INFO、groups、ExtraSids、duration等属性                               |
| raiseChild.py      | 这个脚本通过金票据和ExtraSids实现从子域到域森林的提权                                                                         |

## Windows密码

| 脚本名称           | 介绍                                                                                                                                                                                                                                                                     |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| secretsdump.py | 提供各种技术以不运行任何程序远程dump密码。对SAM和LSA以及缓存的凭据，会尝试从目标注册表中读取并将hives保存在`%SYSTEMROOT%\Temp`目录，再将hives读取回来。对于DIT文件，会使用`DL_DRSGetNCChanges`函数来dump目标的NTLM hash、明文密码和Kerberos keys。也可以通过smbexec或wmiexec执行vssadmin得到NTDS.dit，并对其进行解密。这个脚本在服务不可用的情况下会打开对应的服务，例如远程注册表。在执行结束后，会将激活的服务还原。 |
| mimikatz.py    | 一个用来控制远程mimikatz RPC服务器的Shell，由@gentikiwi开发。                                                                                                                                                                                                                           |

## 参考文章

* [先知社区](https://xz.aliyun.com/t/11877#toc-0)
