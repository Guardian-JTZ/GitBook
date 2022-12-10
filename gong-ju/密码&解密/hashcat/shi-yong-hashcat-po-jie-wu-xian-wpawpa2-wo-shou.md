# 使用 Hashcat 破解无线 (WPA/WPA2) 握手

另一个例子是无线安全评估。客户经常要求将无线评估作为内部渗透测试的一部分。虽然无线并不总是最令人兴奋的，但如果您可以捕获 WPA/WPA2 握手，它就会变得有趣。无线网络通常没有正确地与公司的企业网络分开，对无线网络的成功身份验证可能会授予对内部企业网络的完全访问权限。

Hashcat可用于成功破解 MIC（4 次握手）和 PMKID（第一次数据包/握手）。

## 破解 MIC

当连接到无线网络的客户端和无线接入点 (AP) 通信时，它们必须确保它们都拥有/知道无线网络密钥，但不会通过网络传输密钥。密钥由 AP 加密和验证。

要执行此类离线破解攻击，我们需要通过发送解除身份验证帧来强制客户端（用户）与 AP 断开连接来捕获有效的 4 次握手。当客户端重新验证（通常是自动）时，攻击者可以在他们不知情的情况下尝试嗅出 WPA 4 次握手。此握手是在客户端和关联的 AP 之间的身份验证过程中交换的一组密钥。注意：无线攻击不在本模块的讨论范围内，但会在其他模块中介绍。

这些密钥用于生成称为消息完整性检查 (MIC) 的通用密钥，AP 使用该密钥来验证每个数据包是否未被破坏并以其原始状态接收。

4次握手如下图所示：

![](https://academy.hackthebox.com/storage/modules/20/NEW\_4-way-handshake.png)

一旦我们使用[airodump-ng](https://www.aircrack-ng.org/doku.php?id=airodump-ng)等工具成功捕获了 4 次握手，我们需要将其转换为可提供Hashcat用于破解的格式。所需的格式是hccapx，并Hashcat托管在线服务以转换为这种格式（不推荐用于实际的客户数据，但适合实验室/实践练习)：[cap2hashcat 在线](https://hashcat.net/cap2hashcat/)。

有了转换文件，我们就可以继续使用本模块前面讨论的一种或多种技术进行破解。对于这个例子，我们将执行直接字典攻击来破解 WPA 握手。为了尝试破解这个哈希值，我们将使用模式`22000`，因为之前的模式`2500`已被弃用。我们破解这个哈希的命令看起来像 hashcat -a 0 -m 22000 mic\_to\_crack.hccapx /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt。

让我们继续尝试恢复密钥！

#### Hashcat - 破解 WPA 握手

```shell
JTZ@htb[/htb]$ hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

18cbc1c03cd674c75bb81aee4a75a086:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
62b1bb7345e110abaaf8304c096239b0:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
be2430ce7a4ed2ddb36fc94373197add:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
15c472b7641042af642fc9ec0b65b500:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PBKDF2-PMKID+EAPOL
Hash.Target......: mic_to_crack.hccapx
Time.Started.....: Wed Mar  9 11:20:36 2022 (0 secs)
Time.Estimated...: Wed Mar  9 11:20:36 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    10052 H/s (8.25ms) @ Accel:128 Loops:512 Thr:1 Vec:8
Recovered........: 4/4 (100.00%) Digests
Progress.........: 2888/14344385 (0.02%)
Rejected.........: 2120/2888 (73.41%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:3-7
Candidates.#1....: 123456789 -> celtic07

Started: Wed Mar  9 11:20:26 2022
Stopped: Wed Mar  9 11:20:38 2022
```

有了这个密钥，我们现在可以尝试对无线网络进行身份验证并尝试访问内部公司网络。

***

## 破解PMKID

这种攻击可以针对使用 WPA/WPA2-PSK（预共享密钥）的无线网络执行，并允许我们通过直接攻击 AP 来获取目标无线网络正在使用的 PSK。攻击不需要目标 AP 的任何用户解除身份验证 (deauth)。PMK 与 MIC（4 次握手）攻击中的相同，但通常可以更快地获得并且不会中断任何用户。

成对主密钥标识符 (PMKID) 是 AP 的唯一标识符，用于跟踪客户端使用的成对主密钥 (PMK)。PMKID 位于 4 次握手的第一个数据包中，并且更容易获得，因为它不需要捕获整个 4 次握手。PMKID 是使用 HMAC-SHA1 计算得出的，其中 PMK（无线网络密码）用作密钥、字符串“PMK 名称”、接入点的 MAC 地址和站点的 MAC 地址。下面是 PMKID 计算的可视化表示：

![](https://academy.hackthebox.com/storage/modules/20/NEW\_PMKID\_calc.png)

要执行 PMKID 破解，我们需要获取 pmkid 哈希。 第一步是使用 hcxtools 中的 hcxpcaptool 等工具从捕获 (.cap) 文件中提取它。 我们可以使用 apt 在 Parrot 上安装 hcxtools：sudo apt install hcxtools。

> 注意：此工具已被替换为hcxpcapngtool 我们可以直接从[hcxtools](https://github.com/ZerBea/hcxtools) GitHub 存储库编译和安装的工具

我们将使用这两种工具来完成示例，每个工具都安装在 Pwnbox 上。

使用 apt成功安装`hcxtools`后（如果使用自己的 VM），我们可以发出命令`hcxpcaptool -h`来检查该工具可用的选项。

### Hcxpcaptool - 帮助

```shell
JTZ@htb[/htb]$ hcxpcaptool -h

hcxpcaptool 6.0.3-23-g1c078e4 (C) 2020 ZeroBeat
usage:
hcxpcaptool <options>
hcxpcaptool <options> [input.pcap] [input.pcap] ...
hcxpcaptool <options> *.cap
hcxpcaptool <options> *.*

options:
-o <file> : output hccapx file (hashcat -m 2500/2501)
-O <file> : output raw hccapx file (hashcat -m 2500/2501)
            this will disable all(!) 802.11 validity checks
            very slow!
-k <file> : output PMKID file (hashcat hashmode -m 16800 new format)
-K <file> : output raw PMKID file (hashcat hashmode -m 16801 new format)
            this will disable usage of ESSIDs completely
-z <file> : output PMKID file (hashcat hashmode -m 16800 old format and john)
-Z <file> : output raw PMKID file (hashcat hashmode -m 16801 old format and john)
            this will disable usage of ESSIDs completely
-j <file> : output john WPAPSK-PMK file (john wpapsk-opencl)
-J <file> : output raw john WPAPSK-PMK file (john wpapsk-opencl)
            this will disable all(!) 802.11 validity checks
            very slow!
-E <file> : output wordlist (autohex enabled) to use as input wordlist for cracker
-I <file> : output unsorted identity list
-U <file> : output unsorted username list
-M <file> : output unsorted IMSI number list
-P <file> : output possible WPA/WPA2 plainmasterkey list
-T <file> : output management traffic information list
            format = mac_sta:mac_ap:essid
-X <file> : output client probelist
            format: mac_sta:probed ESSID (autohex enabled)
-D <file> : output unsorted device information list
            format = mac_device:device information string
-g <file> : output GPS file
            format = GPX (accepted for example by Viking and GPSBabel)
-V        : verbose (but slow) status output
-h        : show this help
-v        : show version

<SNIP>
```

现在我们必须使用`hcxpcaptool`提取 pmkid 哈希来运行`Hashcat`

#### 提取 PMKID - 使用 Hcxpcaptool

```shell
JTZ@htb[/htb]$ hcxpcaptool -z pmkidhash_corp cracking_pmkid.cap 

reading from cracking_pmkid.cap
summary capture file:                           
---------------------
file name........................: cracking_pmkid.cap
file type........................: pcapng 1.0
file hardware information........: x86_64
capture device vendor information: 00c0ca
file os information..............: Linux 5.7.0-kali1-amd64
file application information.....: hcxdumptool 6.0.7-22-g2f82e84 (custom options)
network type.....................: DLT_IEEE802_11_RADIO (127)
endianness.......................: little endian
read errors......................: flawless
minimum time stamp...............: 17.07.2020 14:07:19 (GMT)
maximum time stamp...............: 17.07.2020 14:14:21 (GMT)
packets inside...................: 75
skipped damaged packets..........: 0
packets with GPS NMEA data.......: 0
packets with GPS data (JSON old).: 0
packets with FCS.................: 75
beacons (total)..................: 1
probe requests...................: 3
probe responses..................: 1
association responses............: 1
EAPOL packets (total)............: 69
EAPOL packets (WPA2).............: 69
PMKIDs (not zeroed - total)......: 1
PMKIDs (WPA2)....................: 45
PMKIDs from access points........: 1
best handshakes (total)..........: 1 (ap-less: 0)
best PMKIDs (total)..............: 1
summary output file(s):
-----------------------
1 PMKID(s) written to pmkidhash_corp
```

我们将再次执行直接字典攻击，试图破解 WPA 握手。为了尝试破解这个哈希，我们将`22000`再次使用模式，因为之前的模式`16800`也已被取消。这里我们的命令将采用格式 hashcat -a 0 -m 22000 pmkidhash\_corp /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

#### Hashcat——破解PMKID

```shell
JTZ@htb[/htb]$ hashcat -a 0 -m 22000 pmkidhash_corp /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

7943ba84a475e3bf1fbb1b34fdf6d102:10da43bef746:80822381a9c8:CORP-WIFI:cleopatra
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PBKDF2-PMKID+EAPOL
Hash.Target......: pmkidhash_corp
Time.Started.....: Wed Mar  9 11:27:21 2022 (1 sec)
Time.Estimated...: Wed Mar  9 11:27:22 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    12563 H/s (3.49ms) @ Accel:1024 Loops:32 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 18130/14344385 (0.13%)
Rejected.........: 11986/18130 (66.11%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456789 -> celtic07

Started: Wed Mar  9 11:27:20 2022
Stopped: Wed Mar  9 11:27:23 2022
```
