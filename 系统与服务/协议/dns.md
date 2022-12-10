# DNS

\[TOC]

## 介绍

* DNS: 域名系统，互联网的一项服务，作为将 域名和 IP 地址相互映射的分布式数据库
* DNS 使用 TCP 和 UDP 的 53 端口
*   记录类型：

    | 类型               | 描述                                                                                                                                                        |
    | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | 主机记录(A 记录)       | 用于名称解析的重要记录，将特定的主机名映射到对应主机的 IP 地址上                                                                                                                        |
    | 别名记录 (CNAME 记录)  | CNAME 用于将某一个别名指向到某个 A 记录上                                                                                                                                 |
    | IPV6 记录(AAAA 记录) | 与 A 记录对应，用于将特定的主机名映射到一个主机的 IPV6 地址                                                                                                                        |
    | 服务位置记录 (SRV 记录)  | 用来定义提供特定服务的服务器的位置，如 主机、端口等                                                                                                                                |
    | 域名服务器记录 (NS 记录)  | 用来指定该域名由哪个DNS服务器来进行解析。 您注册域名时，总有默认的DNS服务器，每个注册的域名都是由一个DNS域名服务器来进行解析的，DNS服务器NS记录地址一般以以下的形式出现： ns1.domain.com、ns2.domain.com等。 简单的说，NS记录是指定由哪个DNS服务器解析你的域名。 |
    | 邮件交换记录 (MX 记录)   | 用于指定负责处理发往收件人域名的邮件服务器                                                                                                                                     |
    | TXT 记录           | 此类记录通常包含不同第三方提供商的验证密钥和 DNS 的其他安全方面                                                                                                                        |
* 域名解析的方法：
  * 迭代法--服务器
  * 递归法--客户端
    * 客户端发送查询报文"query zh.wikipedia.org"至DNS服务器，DNS服务器首先检查自身缓存，如果存在记录则直接返回结果。
    * 如果记录老化或不存在，则：
      1. DNS服务器向根域名服务器发送查询报文"query zh.wikipedia.org"，根域名服务器返回顶级域 .org 的顶级域名服务器地址。
      2. DNS服务器向 .org 域的顶级域名服务器发送查询报文"query zh.wikipedia.org"，得到二级域.wikipedia.org 的权威域名服务器地址。
      3. DNS服务器向 .wikipedia.org 域的权威域名服务器发送查询报文"query zh.wikipedia.org"，得到主机 zh 的A记录，存入自身缓存并返回给客户端。

## 应用

> 所有的 DNS 服务器都使用三种不同类型的配置文件
>
> * 本地 DNS 配置文件
> * 区域文件
> * 反向名称解析文件

### 1. Bind9

> * 本地配置文件 : named.conf
> * 区域文件 : /etc/bind/db.domain.com
> * 反向名称解析文件： /etc/bind/db.10.129.14

## 工具

### 1. [DIG](../../工具/dig.md)

### 2. 子域暴力破解

```shell
for sub in $(cat /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

### 3. DNSenum

> 子域暴力破解

```shell
dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```
