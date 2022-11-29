---
layout: editorial
---

# SSH



## 介绍

> SSH 是一种网络协议，用于计算机之间的加密登陆

### 中间人攻击

SSH 的使用过程：

1. 远程主机收到用户登陆请求，将自己公钥发给用户
2. 用户使用公钥将登录密码加密后，发送给远程主机
3. 远程主机使用自己的私钥，解密登陆密码，如果密码正确，就同意用户登录

> 如果在实施过程中，有人截获了登录请求，然后冒充远程主机，将伪造的公钥发送给用户，用户无法辨别真伪，当用户发送了密码后，攻击者就可以得到登录密码

## 一、基本用法

### 1. SSH 远程登录 -- 口令

```shell
ssh user@IP [-p ]
```

### 2. SSH 远程登录 -- 公钥

> 公钥登录定义： 用户将自己的公钥储存在远程主机上，登录时远程主机就会向用户发送一段随机字符串，用户使用自己的公钥进行加密后，在发送回来，远程主机使用事先储存的私钥进行解密，如果成功就证明用户是可信的

```shell
# 生成公钥,运行结束后会在 $HOME/.ssh/ 目录下生成 id_rea.pub 和 id_rsa ，前者为公钥，后者为私钥
ssh-keygen
# 检查是否已经添加对应主机的密钥
ssh-keygen -F 10.42.0.47
# 删除对应主机密钥
ssh-keygen -R 10.42.0.47
# 将公钥传送到远程主机上
ssh-copy-id user#IP
```

### 3. 持久连接 SSH

> 如果使用 iTerm2 连接 SSH 时，要求不断线需要做以下配置

```shell
vi ~/.ssh/config
# 添加下方语句
Host *
    ServerAliveInterval 30  # SSH  客户端每 30 秒发送一个 no-op 包给远程主机
```

## 二、 SSH 远程操作

### 1. SSH 数据传输

```shell
# 将 $HOME/src/ 目录下的文件复制到远程主机的 $HOME/src/ 目录
cd && tar czv src | ssh user@host 'tar xz'
# 将远程主机$HOME/src/目录下面的所有文件，复制到用户的当前目录。
ssh user@host 'tar cz src' | tar xzv
# 查看远程主机是否运行进程httpd， 单引号中的内容就是要指定的命令
ssh user@host 'ps ax | grep [h]ttpd'
```

### 2. scp 跨机远程拷贝

> scp 用于在 Linux 下进行远程拷贝文件的命令，传输的时候加密其内容

```shell
scp -help
# 命令参数
    -1 强制scp命令使用协议ssh1
    -2 强制scp命令使用协议ssh2
    -4 强制scp命令只使用IPv4寻址
    -6 强制scp命令只使用IPv6寻址
    -B 使用批处理模式（传输过程中不询问传输口令或短语）
    -C 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）
    -p 留原文件的修改时间，访问时间和访问权限。
    -q 不显示传输进度条。
    -r 递归复制整个目录。
    -v 详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。
    -c cipher 以cipher将数据传输进行加密，这个选项将直接传递给ssh。
    -F ssh_config 指定一个替代的ssh配置文件，此参数直接传递给ssh。
    -i identity_file 从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。
    -l limit 限定用户所能使用的带宽，以Kbit/s为单位。
    -o ssh_option 如果习惯于使用ssh_config(5)中的参数传递方式，
    -P port 注意是大写的P, port是指定数据传输用到的端口号
    -S program 指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项

# 本机复制远程文件
scp root@www.test.com:/val/test/test.tar.gz /val/test/test.tar.gz
# 远程复制本地文件
scp /val/test.tar.gz root@www.test.com:/val/test.tar.gz
# 本地复制远程文件夹
scp -r root@www.test.com:/val/test/ /val/test/
# 远程复制本地文件夹
scp -r ./ubuntu_env/ root@192.168.0.111:/home/pipi
# 本地复制远程文件到指定目录
scp root@www.test.com:/val/test/test.tar.gz /val/test/
# 远程复制本地文件到指定目录
scp /val/test.tar.gz root@www.test.com:/val/
# 在两个远程主机之间复制文件
scp user1@remotehost1:/some/remote/dir/foobar.txt user2@remotehost2:/some/remote/dir/
```

### 3. SSH 端口操作

#### 1. 绑定本地端口

```shell
# SSH 会建立一个 SOCKET ，去监听本地 8080端口，一旦有数据传向那个端口，就自动转移到 SSH 连接上面，发往远程主机
ssh -D 8080 user@host
```

#### 2. 本地端口转发

> 假定host1是本地主机，host2是远程主机。由于种种原因，这两台主机之间无法连通。但是，另外还有一台host3，可以同时连通前面两台主机。因此，很自然的想法就是，通过host3，将host1连上host2

```shell
# 在 host1 指定下面命令
# 本地端口:目标主机:目标主机端口
# 作用： 指定 SSH 绑定本地端口 2121，然后指定 host3 将所有数据转发到目标主机 host2 的 21 端口
ssh -L 0.0.0.0:2121:host2:21 host3
```

#### 3. 远程端口转发

> host1与host2之间无法连通，必须借助host3转发。但是，特殊情况出现了，host3是一台内网机器，它可以连接外网的host1，但是反过来就不行，外网的host1连不上内网的host3。解决办法是，既然host3可以连host1，那么就从host3上建立与host1的SSH连接，然后在host1上使用这条连接就可以了。

```shell
# 在 host3 执行下面命令
# 执行命令后, host1 监听 2121 端口，host 3 就会发起请求建立连接，数据就会经过 host3 传送给 host2
ssh -R 2121:host2:21 host1
```

## 常用命令

```shell
# 复制 SSH 密钥到目标主机，开启无密码 SSH 登录
ssh-copy-id user@host
# 从某主机的80端口开启到本地主机2001端口的隧道
ssh -N -L2001:localhost:80 somemachine
# 将你的麦克风输出到远程计算机的扬声器
dd if=/dev/dsp | ssh -c arcfour -C username@host dd of=/dev/dsp
# 比较远程和本地文件
ssh user@host cat /path/to/remotefile | diff /path/to/localfile –
# 通过SSH挂载目录/文件系统
sshfs name@server:/path/to/folder /path/to/mount/point
# 通过中间主机建立SSH连接
ssh -t reachable_host ssh unreachable_host
# 直接连接到只能通过主机B连接的主机A
ssh -t hostA ssh hostB
# 通过SSH连接屏幕
ssh -t remote_host screen –r
# 端口检测
knock <host> 3000 4000 5000 && ssh -p <port> user@host && knock <host> 5000 4000 3000
# 通过SSH运行复杂的远程shell命令
ssh host -l user $(<cmd.txt)
# 通过SSH将MySQL数据库复制到新服务器
mysqldump –add-drop-table –extended-insert –force –log-error=error.log -uUSER -pPASS OLD_DB_NAME | ssh -C user@newhost “mysql -uUSER -pPASS NEW_DB_NAME”
# 建立一个可以重新连接的远程GNU screen
ssh -t user@some.domain.com /usr/bin/screen –xRR
# SCP大文件
rsync –partial –progress –rsh=ssh $file_source $user@$host:$destination_file local -> remote
rsync –partial –progress –rsh=ssh $user@$host:$remote_file $destination_file remote -> local
# 通过SSH W/ WIRESHARK分析流量
ssh root@server.com ‘tshark -f “port !22″ -w -' | wireshark -k -i –
ssh root@example.com tcpdump -w – ‘port !22′ | wireshark -k -i –
# 保持 SSH 会话永久打开
autossh -M50000 -t server.example.com ‘screen -raAd mysession’
# 将标准输入（stdin）复制到你的X11缓冲区
ssh user@host cat /path/to/some/file | xclip
```

## 参考文章

* [SSH原理与运用（一）：远程登录](https://www.ruanyifeng.com/blog/2011/12/ssh\_remote\_login.html)
* [iTerm2中ssh保持连接不断开](http://bluebiu.com/blog/iterm2-ssh-session-idle.html)
* [12个scp传输文件的命令栗子](https://www.cnblogs.com/voidy/p/4215891.html)
* [scp 跨机远程拷贝](https://linuxtools-rst.readthedocs.io/zh\_CN/latest/tool/scp.html)
