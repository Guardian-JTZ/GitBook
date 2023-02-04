# Windows 端口转发 WSL 流量

> &#x20;WSL 没有图形化，为了方便看我把 WSL 流量转发到 WIndows  的 OpenVPn 的 指定端口

```bash
# 使用的时候记得改一下 IP 地址
PS C:\Users\14894> netsh interface portproxy show v4tov4
PS C:\WINDOWS\system32> netsh interface portproxy add v4tov4 listenport=8000 listenaddr=10.18.4.251  connectport=8000 connectaddress= $($(wsl hostname -I).Trim());
PS C:\Users\14894> netsh interface portproxy show v4tov4

侦听 ipv4:                 连接到 ipv4:

地址            端口        地址            端口
--------------- ----------  --------------- ----------
10.18.4.251     8000        172.21.31.34    8000
```
