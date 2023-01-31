# enum4linux-ng

> 是 enum4linux 的进阶版本
>
> * 支持YAML和JSON输出；
> * 颜色丰富的终端输出；
> * 包含了ldapsearch和polenum的原生实现；
> * 支持遗留的SMBv1连接；
> * 自动检测IPC签名支持；
> * “智能”枚举；
> * 支持超时；
> * 支持IPv6；

## enum4linux

> Enum4linux 是一个用于从 `Windows` 和 `Samba` 系统枚举信息的工具, 基本上是 Samba 工具 smbclient、rpclient、net 和 nmblookup 的包装器

```shell
sudo apt install enum4linux
```

## enum4linux-ng

```shell
git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
pip3 install -r requirements.txt

./enum4linux-ng.py 10.129.14.128 -A
```
