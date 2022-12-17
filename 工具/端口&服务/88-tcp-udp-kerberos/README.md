# 88 tcp/udp -- Kerberos

Kerberos 介绍 -->[Kerberos 介绍.md](<../../../计算机网络/端口&协议/Kerberos 介绍.md> "mention")

## 攻击漏洞方法

1. Pass\_the\_ticket : 伪造会话密钥并将该伪造作为凭证提供给资源的过程
2. Golden\_Ticket : 授予用户域管理员访问权限的凭证
3. Silver\_Ticket : 一种伪造的票证，授予对服务的访问权限
4. 凭据填充/暴力破解 ： 自动持续尝试猜测密码
5. Encryption downgrade with Skeleton Key Malware：一种可以绕过Kerberos的恶意软件，但攻击必须有管理员权限
6. DCShadow 攻击：一种新的攻击方式，攻击者在网络中获得足够的访问权限来设置自己的 DC 以用于进一步渗透

## 参考文章

1. [windows-protocol](https://daiker.gitbook.io/windows-protocol/)
