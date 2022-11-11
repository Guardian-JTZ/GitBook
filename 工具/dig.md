# dig

> DNS 服务器的足迹是由于我们发送的请求而完成的。因此，首先，可以向 DNS 服务器查询哪些其他名称服务器是已知的。我们使用 NS 记录和要使用`@`字符查询的 DNS 服务器的规范来执行此操作

```shell
# NS  查询
dig ns inlanefreight.htb @10.129.14.128
# any 查看所有可用记录
dig any inlanefreight.htb @10.129.14.128
# AXFR 区域转移
dig axfr inlanefreight.htb @10.129.14.128
```
