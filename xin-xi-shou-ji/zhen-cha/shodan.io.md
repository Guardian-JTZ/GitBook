# Shodan.io

> [Shodan.io](https://www.shodan.io/)
>
> * 使用没有弄完

## 使用

```shell
asn:AS14064  	# 查找指定 ASN 下的记录
product:MYSQL 	# 查找 MYSQL 服务器
vuln: ms17-010  # 查找指定漏洞
os:windows  	# 查找操作系统
city:"london"	# 城市
country:"IN"	# 国家
geo:"56.913055,118.250862"	# 通过地理位置查询
server: "gws" hostname:"google"	# 查找 hostname
net:210.214.0.0/16	# 查找网络
proftpd port:21 # 查找端口
apache after:22/02/2009 before:14/3/2010	# 查找指定时间
title:"citrix gateway"	# 查找 citrix 网关
html:"def_wirelesspassword"	# 查找明文 WIFI 密码
```

## 参考

* https://github.com/humblelad/Shodan-Dorks
