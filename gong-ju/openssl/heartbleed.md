# Heartbleed

> 一个属于 OpenSSL 的逻辑错误漏洞，允许攻击者获取他们不应该访问的随机内存块

![](F:%5CNote%5CNote%5CGitBook.gitbook%5Cassets%5Cxkcd-heartbleed\_explaination.png)

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
