# DNS 介绍

## 介绍

* DNS: 域名系统，互联网的一项服务，作为将 域名和 IP 地址相互映射的分布式数据库
* DNS 使用 TCP 和 UDP 的 53 端口

## 学习文章

* [DNS 图解](https://blog.csdn.net/crazymakercircle/article/details/120521694?ops\_request\_misc=%257B%2522request%255Fid%2522%253A%2522167283221216800186542776%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D\&request\_id=167283221216800186542776\&biz\_id=0\&utm\_medium=distribute.pc\_search\_result.none-task-blog-2\~all\~top\_positive\~default-1-120521694-null-null.142^v68^pc\_rank\_34\_queryrelevant25,201^v4^add\_ask,213^v2^t3\_control1\&utm\_term=DNS\&spm=1018.2226.3001.4187)

## 工具

### 1. [DIG](../../工具/端口&服务/53-dns/dig.md)

### 2. 子域暴力破解

```shell
for sub in $(cat /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

### 3. DNSenum

> 子域暴力破解

```shell
dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```
