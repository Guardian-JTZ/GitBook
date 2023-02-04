# 子域枚举

## SSL/TLS 证书

> 当 CA 为域创建 SSL/TLS 证书时， CA 会参 "证书透明度日志 (CT)"，这是为域名创建的每个 SSL/TLS 证书都可以访问的日志

* [crt.sh](http://crt.sh/)
* [ctsearch](https://ui.ctsearch.entrust.com/ui/ctsearchui)

## Google

> 我们可以使用黑客语法来对目标的范围进行缩小

```shell
-site:www.tryhackme.com site:*.tryhackme.com，
```

## DNS 暴力破解

| 名称       | 介绍       |
| -------- | -------- |
| DNSRecon | DNS 枚举脚本 |

## Sublist3r

| 名称        | 介绍              |
| --------- | --------------- |
| Sublist3r | 使用 OSINT 枚举网站子域 |
| SubBrute  | 使用 OSINT 枚举网站子域 |

## VHOST

```shell
user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://MACHINE_IP
```

| 名称       | 介绍     |
| -------- | ------ |
| FFUF     | 暴力破解工具 |
| goButser | 暴力破解工具 |

## nslookup & dig

> 我们可以利用 `Nslookup` 和 `Dig` 工具对 DNS 信息进行查询
>
> * 公共 DNS服务：
>   * CloudFlare : 1.1.1.1、1.0.0.1
>   * Google : 8.8.8.8、8.8.4.4
>   * Quad9 : 9.9.9.9、149.112.112.112

## 在线工具

| 名称                                                       | 介绍       |
| -------------------------------------------------------- | -------- |
| VirusTotal                                               | 在线搜索指定子域 |
| [DnsDumpster](https://dnsdumpster.com/)                  |          |
| [DNSCHECKER](https://dnschecker.org/)                    |          |
| [certificates](https://search.censys.io/certificates)    |          |
| [TheHarvester](https://github.com/laramies/theHarvester) |          |
| [Net craft](https://www.netcraft.com/)                   |          |
| [hackertarget](https://hackertarget.com/zone-transfer/)  |          |
