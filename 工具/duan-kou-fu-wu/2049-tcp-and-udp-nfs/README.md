# 2049-TCP/UDP-NFS

## 前言

关于 NFS 的介绍查看文章--> [链接](../../../网络&系统/duan-kou-xie-yi/nfs-jie-shao.md)

默认端口： 2048-TCP/UDP （除版本4 之外只需要 TCP 和 UDP）

```shell
2049/tcp open  nfs     2-3 (RPC #100003
```

## 枚举

### 1. nmap 脚本

```shell
nfs-ls 	# 列出 NFS 并检查权限
nfs-showmount 	# 类似于 showmount -e
nfs-statfs 	# 来自 NFS 共享的磁盘统计和信息
```

### 2. metasploit 模块

```shell
scanner/nfs/nfsmount 	# 扫描 NFS 挂载并列出权限
```

### 3. showmount

```shell
showmount -e <IP> # 列出可挂载的文件夹
```

## 挂载

> 建议使用版本 2 ，因为这个版本没有任何身份验证或授权

```shell
mount -t nfs [-o vers=2] <ip>:<remote_folder> <local_folder> -o nolock
```

## 权限

> 如果挂载的一个文件夹，其中包含只能由某些用户 (通过 UID )访问的文件或文件夹，可以在本地创建一个 UID 相同的用户，并使用该用户访问

## NFSSHELL

可以使用 [NFSSHELL](https://github.com/NetDirect/nfsshell) 通过更改 UID和 GID 访问文件

> NFSSHELL 使用教程--> [链接](https://www.pentestpartners.com/security-blog/using-nfsshell-to-compromise-older-environments/)

## 利用 NFS 错误配置进行权限升级

## 总结

```shell
进程名称 ： NFS
端口号 ：2409
协议说明 ： 网络文件系统

笔记1：
	名称： 笔记
	说明： NFS 注意事项
	注意： 这是一个 C/S 系统，允许用户通过网络访问文件并将他们当做本地文件一样
	#apt install nfs-common
	showmount 10.10.10.180      ~or~showmount -e 10.10.10.180
	mount -t nfs -o ver=2 10.10.10.180:/home /mnt/

笔记2：
	名称： Nmap
	描述： Nmap 的 NFS 脚本程序
	命令： nmap --script=nfs-ls.nse,nfs-showmount.nse,nfs-statfs.nse -p 2049 {IP}
```
