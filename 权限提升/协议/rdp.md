# RDP

> RDP 是用于 Windows 远程访问的计算机协议，该协议允许通过 IP 网络加密的 GUI 传输显示和控制命令
>
> * TCP/UDP 使用端口 ： 3389

```shell
# nmap 扫描服务
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*

# RDP 安全检查
sudo cpan
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
./rdp-sec-check.pl 10.129.201.248

# 启动 RDP 会话
xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```
