# FTP

\[TOC]

## 介绍

* FTP： 文件传输协议，是一个用于在计算机网络上在客户端和服务器之间进行文件传输的应用层协议
* FTP 一般运行在 20 和 21 两个端口，端口 20 用于在客户端和服务器之间传输数据流，端口 21 用于传输控制流，并且是命令通向 FTP 服务器的进口。当数据通过数据流传输的时候，控制流时，控制流处于空闲状态，而当控制流`空闲较长时间后，客户端的防火墙会将该会话设置为超时`，这样当大量数据通过防火墙时，会产生一些问题，此时，虽然文件可以成功传输，但是会因为控制会话，被防火墙断开。
* 运行 FTP 服务的站点大都开放匿名服务，在这种设置下，用户不需要账号就可以登陆服务器，默认情况下，匿名用户的用户名是 anonymous, 这个账号不需要密码
* 两种模式：
  * 主动模式： 要求客户端和服务器端同时打开并监听一个端口以创建连接
  * 被动模式： 服务端产生一个监听相应端口的进程
* [FTP 命令列表](https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/)
* [FTP 服务器返回状态码](https://en.wikipedia.org/wiki/List\_of\_FTP\_server\_return\_codes)

## 应用

### 1. vsFTPd

> 基于 Linux 发行版的常用 FTP 服务器之一， vsFTPd 的默认配置位与 /etc/vsftpd.conf
>
> [vsFtpd 的配置手册](http://vsftpd.beasts.org/vsftpd\_conf.html)

```shell
# 一些危险的配置
anonymous_enable=YES  # 允许匿名登录
anon_upload_enable=YES  # 允许匿名上传文件
anon_mkdir_write_enable=YES  # 允许匿名创建新目录
no_anon_password=YES  # 不要匿名询问密码
anon_root=/home/username/ftp  # 匿名目录
write_enable=YES  # 允许使用 FTP 命令：STOR、DELE、RNFR、RNTO、MKD、RMD、APPE 和 SITE


# 获取服务器的一些概览设置
ftp> status # 获取 vsFTPd 的状态
ftp> debug
ftp> trace
```

## 工具使用

### 1. FTP

```shell
# 在本机执行，下载所有可用文件
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
# 连接 FTP 服务器
FTP IP-address

# 下载文件
get [remote-file] [local-file] # 下载单个文件
mget [remote-files] # 下载多个文件

# 上传文件
put local-file [remote-file]  # 上传单个文件
mput lical-files  # 上传多个文件

# 断开连接
byte
```

## 安全问题

> * [暴力破解](https://zh.wikipedia.org/wiki/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3)
> * [FTP反弹攻击](https://zh.wikipedia.org/w/index.php?title=FTP%E5%8F%8D%E5%BC%B9%E6%94%BB%E5%87%BB\&action=edit\&redlink=1)
> * [数据包捕获](https://zh.wikipedia.org/wiki/%E6%95%B8%E6%93%9A%E5%8C%85%E5%88%86%E6%9E%90%E5%99%A8)
> * 端口窃取（猜测下一个开放端口并篡夺合法连接）
> * [欺骗攻击](https://zh.wikipedia.org/wiki/%E6%AC%BA%E9%AA%97%E6%94%BB%E5%87%BB)
> * [用户名](https://zh.wikipedia.org/wiki/%E7%94%A8%E6%88%B7)枚举
