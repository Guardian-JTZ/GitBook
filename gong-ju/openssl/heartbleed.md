# Heartbleed

> “任何能上网的人都可以通过这个”心脏出血(Heartbleed)”bug读取你的服务器的内存信息——只要你的系统是用这个有漏洞的OpenSSL软件做安全保护的。它会泄露用来认证服务提供商和用来加密内容传输的密钥，还会泄露用户的用户名和密码，以及用户正在使用的内容信息。黑客能利用这个漏洞窃听你和服务器交流的信息，直接窃取你和服务器的数据，并把自己伪造成服务提供商或用户。

基本上，利用”心脏出血(Heartbleed)”漏洞，一个黑客每次能从你的服务器上抓取64K的内存信息。这种入侵行为不会留下任何痕迹，而且可以多次反复随机抓取不同的64K内存内容。这意味着内存中的任何东西——SLL私钥，用户密钥，任何东西——都有被泄露的危险。事实上你必须假设它们全被泄露了。全部。

![](../../.gitbook/assets/Heartbleed2.png)

## 检测

### 1. nmap NSE 脚本

```shell
nmap --script=ssl-heartbleed -p 443 IP
```

### 2. MSF

```shell
msf > use auxiliary/scanner/ssl/openssl_heartbleed msf auxiliary(openssl_heartbleed) > show options

Module options (auxiliary/scanner/ssl/openssl_heartbleed):
```

## POC

> Github 地址 --> [链接](https://github.com/sensepost/heartbleed-poc)
