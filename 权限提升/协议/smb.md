# SMB

## 引言

* SMB ： 网络文件共享系统，一种应用层网络传输协议，主要功能是使网络上的机器可以共享计算机文件、打印机、串行端口和通讯等资源，也提供经过认证的进程间通信技能
* 使用 C/S 架构，工作端口：
  * \\\共享计算机地址\资源路径 ： 使用 TCP 协议，445 端口
  * \\\共享计算机名\资源路径 : 使用 NetBIOS 协议， 139 端口
* SMB 工作流程： 版本协商--> 会话请求凭据 --> 文件路径凭据 --> 对共享资源进行操作

### 1. 使用 TCP 协议

![image-20221106091405491](https://typora-1311404666.cos.ap-beijing.myqcloud.com/img/20221106130120.png)

我们可以看到 189 192 193 是在建立 TCP 连接， 第 194 197 198 是一个版本协商的过程，第 199 200 201 202 是在处理远程登陆凭据， 第一次连接错误，第二次正确， 203 205 是在处理远程文件路径的凭据

### 2. 使用 NETBIOS 协议

> 自己电脑无法使用计算机名称访问

## 应用

### 1. Samba

> 默认配置：
>
> ```shell
> cat /etc/samba/smb.conf | grep -v "#\|\;"
> ```

```shell
# 危险设置
browseable = yes  # 允许在当前共享中列出可用共享吗
read only = no  # 禁止创建和修改文件
writable = yes  # 允许用户创建和修改文件
guest ok = yes  # 允许在不使用密码的情况下连接到服务
enable privileges = yes  # 尊重分配给特定 SID 的特权
create mask = 0777  # 必须为新创建的文件分配什么权限
directory mask = 0777  # 必须为新创建的目录分配什么权限
logon script = script.sh  # 用户登录时需要执行什么脚本
magic script = script.sh  # 当脚本关闭时应该执行哪个脚本
magic output = script.out  # 魔术脚本的输出需要存储在哪里
```

### 2. 开启 SMB 服务

```bash
# 1. smbserver.py
python3 smbserver.py share 共享目录
```

## 工具使用

### 1. [smbclient](<../../工具/端口&服务/139&445 -- SMB/smbclient.md>)

### 2.[ rpcclient](<../../工具/端口&服务/139&445 -- SMB/rpcclient.md>)

### 3. [smbmap](<../../工具/端口&服务/139&445 -- SMB/smbmap.md>)

### 4. [crackmapexec](../../工具/crackmapexec.md)

### 5. [enum4linux-ng](../../工具/enum4linux-ng.md)
