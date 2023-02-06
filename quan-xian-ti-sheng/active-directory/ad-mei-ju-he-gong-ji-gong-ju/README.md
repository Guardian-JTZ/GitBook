---
description: 这是一个关于 AD 攻击中可能会使用的一些工具
---

# 🦯 AD 枚举和攻击 -- 工具



| 工具                                                                                                                                            | 描述                                                                                                                                                                                                                                                        |
| --------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)/[SharpView](https://github.com/dmchell/SharpView) | PowerShell 工具和相同的 .NET 端口用于在 AD 中获得态势感知。 这些工具可用作各种 Windows net\* 命令等的替代品。 PowerView 和 SharpView 可以帮助我们收集 BloodHound 所做的大部分数据，但需要做更多的工作才能在所有数据点之间建立有意义的关系。 这些工具非常适合检查我们可以使用一组新凭据进行哪些额外访问，针对特定用户或计算机，或查找一些“快速获胜”，例如可以通过 Kerberoasting 或 ASREPRoasting 攻击的用户 |
| [Impacket toolkit](https://github.com/SecureAuthCorp/impacket)                                                                                | 用 Python 编写的用于与网络协议交互的工具集合。 该工具套件包含用于枚举和攻击 Active Directory 的各种脚本                                                                                                                                                                                         |
| [Responder](https://github.com/lgandx/Responder)                                                                                              | Responder 是一个专门用来毒化 LLMNR、NBT-NS 和 MDNS 的工具，具有许多不同的功能                                                                                                                                                                                                     |
| [Inveigh.ps1](https://github.com/Kevin-Robertson/Inveigh/blob/master/Inveigh.ps1)                                                             | 类似于 Responder，一种用于执行各种网络欺骗和中毒攻击的 PowerShell 工具                                                                                                                                                                                                            |
| [C# Inveigh（InveighZero）](https://github.com/Kevin-Robertson/Inveigh/tree/master/Inveigh)                                                     | Inveigh 的 C# 版本带有半交互式控制台，用于与捕获的数据（如用户名和密码哈希）进行交互                                                                                                                                                                                                          |
| [rpcclient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)                                                               | Linux 发行版上 Samba 套件的一部分，可用于通过远程 RPC 服务执行各种 Active Directory 枚举任务                                                                                                                                                                                          |
| [CrackMapExec (CME)](https://github.com/byt3bl33d3r/CrackMapExec)                                                                             | CME 是一个枚举、攻击和后期开发工具包，可以极大地帮助我们对收集到的数据进行枚举和执行攻击。 CME 试图“靠地为生”并滥用内置的 AD 功能和协议，如 SMB、WMI、WinRM 和 MSSQL                                                                                                                                                       |
| [Rubeus](https://github.com/GhostPack/Rubeus)                                                                                                 | Rubeus 是为 Kerberos 滥用构建的 C# 工具                                                                                                                                                                                                                            |
| [GetUserSPNs.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetUserSPNs.py)                                              | 另一个 Impacket 模块旨在查找与普通用户相关的服务主体名称                                                                                                                                                                                                                         |
| [Hashcat](https://hashcat.net/hashcat/)                                                                                                       | 暴力破解工具                                                                                                                                                                                                                                                    |
| [enum4linux](https://github.com/CiscoCXSecurity/enum4linux)                                                                                   | 用于从 Windows 和 Samba 系统枚举信息的工具                                                                                                                                                                                                                             |
| [enum4linux-ng](https://github.com/cddmp/enum4linux-ng)                                                                                       | 原始 Enum4linux 工具的返工，其工作方式略有不同                                                                                                                                                                                                                             |
| [ldapsearch](https://linux.die.net/man/1/ldapsearch)                                                                                          | 用于与 LDAP 协议交互的内置接口                                                                                                                                                                                                                                        |
| [windapsearch](https://github.com/ropnop/windapsearch)                                                                                        | 用于使用 LDAP 查询枚举 AD 用户、组和计算机的 Python 脚本。 用于自动化自定义 LDAP 查询                                                                                                                                                                                                   |
| [DomainPasswordSpray.ps1](https://github.com/dafthack/DomainPasswordSpray)                                                                    | DomainPasswordSpray 是一种用 PowerShell 编写的工具，用于对域用户执行密码喷射攻击                                                                                                                                                                                                  |
| [LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit)                                                                                      | 该工具包包括用 PowerShell 编写的函数，这些函数利用 PowerView 来审计和攻击已部署 Microsoft 本地管理员密码解决方案 (LAPS) 的 Active Directory 环境                                                                                                                                                    |
| [smbmap](https://github.com/ShawnDEvans/smbmap)                                                                                               | 跨域的 SMB 共享枚举                                                                                                                                                                                                                                              |
| [psexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py)                                                        | 作为 Impacket 工具包的一部分，它以半交互式 shell 的形式为我们提供了类似 Psexec 的功能                                                                                                                                                                                                   |
| [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)                                                      | 作为 Impacket 工具包的一部分，它提供了通过 WMI 执行命令的能力                                                                                                                                                                                                                    |
| [Snaffler](https://github.com/SnaffCon/Snaffler)                                                                                              | 用于在具有可访问文件共享的计算机上的 Active Directory 中查找信息（例如凭据）                                                                                                                                                                                                           |
| [smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py)                                                  | 用于与 Windows 主机交互的简单 SMB 服务器执行。 在网络中传输文件的简便方法                                                                                                                                                                                                              |
| [setspn.exe](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc731241\(v=ws.11\))           | 添加、读取、修改和删除 Active Directory 服务帐户的服务主体名称 (SPN) 目录属性                                                                                                                                                                                                       |
| [Mimikatz](https://github.com/ParrotSec/mimikatz)                                                                                             | 执行许多功能。 值得注意的是，传递哈希攻击、提取明文密码以及从主机内存中提取 Kerberos 票证                                                                                                                                                                                                        |
| [secretsdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py)                                              | 从主机远程转储 SAM 和 LSA 机密                                                                                                                                                                                                                                      |
| [evil-winrm](https://github.com/Hackplayers/evil-winrm)                                                                                       | 通过 WinRM 协议在主机上为我们提供交互式 shell                                                                                                                                                                                                                             |
| [mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)                                              | 作为 Impacket 工具包的一部分，它提供了与 MSSQL 数据库交互的能力                                                                                                                                                                                                                  |
| [noPac.py](https://github.com/Ridter/noPac)                                                                                                   | 利用 CVE-2021-42278 和 CVE-2021-42287 组合从标准域用户冒充 DA                                                                                                                                                                                                          |
| [rpcdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/rpcdump.py)                                                      | Impacket 工具集的一部分，RPC 端点映射器                                                                                                                                                                                                                                |
| [CVE-2021-1675.py](https://github.com/cube0x0/CVE-2021-1675/blob/main/CVE-2021-1675.py)                                                       | Python 中的 Printnightmare PoC                                                                                                                                                                                                                              |
| [ntlmrelayx.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py)                                                | 作为 Impacket 工具集的一部分，它执行 SMB 中继攻击                                                                                                                                                                                                                          |
| [PetitPotam.py](https://github.com/topotam/PetitPotam)                                                                                        | CVE-2021-36942 的 PoC 工具强制 Windows 主机通过 MS-EFSRPC EfsRpcOpenFileRaw 或其他函数向其他机器进行身份验证                                                                                                                                                                       |
| [gettgtpkinit.py](https://github.com/dirkjanm/PKINITtools/blob/master/gettgtpkinit.py)                                                        | 用于操作证书和 TGT 的工具                                                                                                                                                                                                                                           |
| [getnthash.py](https://github.com/dirkjanm/PKINITtools/blob/master/getnthash.py)                                                              | 此工具将使用现有的 TGT 为使用 U2U 的当前用户请求 PAC                                                                                                                                                                                                                         |
| [adidnsdump](https://github.com/dirkjanm/adidnsdump)                                                                                          | 用于从域中枚举和转储 DNS 记录的工具。 类似于执行 DNS 区域传输                                                                                                                                                                                                                      |
| [gpp-decrypt](https://github.com/t0thkr1s/gpp-decrypt)                                                                                        | 从组策略首选项文件中提取用户名和密码                                                                                                                                                                                                                                        |
| [GetNPUsers.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetNPUsers.py)                                                | Impacket 工具包的一部分。 用于执行 ASREPRoasting 攻击以列出和获取设置为“不需要 Kerberos 预身份验证”的用户的 AS-REP 哈希。 然后将这些哈希输入到 Hashcat 等工具中，以尝试进行离线密码破解                                                                                                                                   |
| [lookupsid.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/lookupsid.py)                                                  | SID 暴力破解工具                                                                                                                                                                                                                                                |
| [ticketer.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketer.py)                                                    | 用于创建和定制 TGT/TGS 票证的工具。 它可用于创建金票、孩子对父母的信任攻击等                                                                                                                                                                                                               |
| [raiseChild.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/raiseChild.py)                                                | Impacket 工具包的一部分，它是一个自动子域到父域权限升级的工具                                                                                                                                                                                                                       |
| [Active Directory Explorer](https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer)                                               | Active Directory Explorer (AD Explorer) 是一个 AD 查看器和编辑器。 它可用于导航 AD 数据库和查看对象属性。 它还可以用于保存 AD 数据库的快照以供离线分析。 加载 AD 快照时，可以将其作为数据库的实时版本进行探索。 它还可用于比较两个 AD 数据库快照，以查看对象、属性和安全权限的变化                                                                                 |
| [PingCastle](https://www.pingcastle.com/documentation/)                                                                                       | 用于基于风险评估和成熟度框架（基于适配AD安全的CMMI）对AD环境的安全级别进行审计。                                                                                                                                                                                                              |
| [Group3r](https://github.com/Group3r/Group3r)                                                                                                 | Group3r 可用于审核和查找 AD 组策略对象 (GPO) 中的安全配置错误。                                                                                                                                                                                                                 |

## AD 侦查

### BloodHound

| 名称                                                                              | 作用                                                                                                                              |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| [SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors) | C# 数据收集器从 Active Directory 收集有关不同 AD 对象的信息，例如用户、组、计算机、ACL、GPO、用户和计算机属性、用户会话等。 该工具生成 JSON 文件，然后可以将这些文件提取到 BloodHound GUI 工具中进行分析 |
| [BloodHound.py](https://github.com/fox-it/BloodHound.py)                        | 基于 Impacket 工具包的 Python  BloodHound 摄取器。 它支持大多数 BloodHound 收集方法，并且可以从未加入域的攻击主机运行。 可以将输出提取到 BloodHound GUI 中进行分析                 |
| BloodHound                                                                      |                                                                                                                                 |

### ADRecon

| 名称                                            | 作用                                                                           |
| --------------------------------------------- | ---------------------------------------------------------------------------- |
| [ADRecon](https://github.com/adrecon/ADRecon) | 用于从目标 AD 环境中提取各种数据的工具。 数据可以 Microsoft Excel 格式输出，带有摘要视图和分析，以协助分析并绘制环境整体安全状态图 |

## Kerberos

| 名称                                             | 介绍链接              | 作用                                                        |
| ---------------------------------------------- | ----------------- | --------------------------------------------------------- |
| [Kerbrute](https://github.com/ropnop/kerbrute) | [链接](kerbrute.md) | 暴力破解用户、密码、密码喷洒                                            |
| Impacket-GetNPUsers                            | 链接                | 这个脚本会尝试获得并列出不需要Kerberos域认证(UF\_DONT\_REQUIRE\_PREAUTH)的用户 |
