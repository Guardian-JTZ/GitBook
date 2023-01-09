# smbClient

{% code overflow="wrap" %}
```shell
smbclient --help
  -M, --message=HOST 发送消息
  -I, --ip-address=IP 使用这个IP连接到
  -E, --stderr 将消息写入标准错误,而不是标准输出
  -L, --list=HOST 获取主机可用共享列表
  -T, --tar=<c|x>IXFvgbNan 命令行 tar
  -D, --directory=DIR 开始目录
  -c, --command=STRING 执行命令使用分号分隔
  -b, --send-buffer=BYTES 更改发送/发送缓冲区
  -t, --timeout=SECONDS 更改每个操作暂停时间
  -p, --port=PORT 要连接的端口
  -g, --grepable 产生 grepable 输出
  -q, --quiet 禁止显示帮助信息
  -B, --browse 使用 DNS 浏览 SMB 服务器


常见的 Samba 选项：
  -d, --debuglevel=DEBUGLEVEL 设置调试级别
      --debug-stdout 将调试输出发送到标准输出
  -s, --configfile=CONFIGFILE 使用替代配置文件
      --option=name=value 设置 smb.conf 选项
  -l, --log-basename=LOGFILEBASE 日志/调试文件的基本名称
      --leak-report 启用talloc泄漏报告
      --leak-report-full 启用完整的talloc泄漏

连接选项：
  -R, --name-resolve=NAME-RESOLVE-ORDER 使用这些名称解析服务
  -O, --socket-options=SOCKETOPTIONS 要使用的套接字选项
  -m, --max-protocol=MAXPROTOCOL 设置最大协议级别
  -n, --netbiosname=NETBIOSNAME 主 netbios 名称
      --netbios-scope=SCOPE 使用这个 Netbios 范围
  -W, --workgroup=WORKGROUP 设置工作组名称
      --realm=REALM 设置领域名称

凭证选项：
  -U, --user=[DOMAIN/]USERNAME[%PASSWORD] 设置网络用户名
  -N, --no-pass 不要求输入密码
      --password=STRING 密码
      --pw-nt-hash 提供的密码是 NT 哈希
  -A, --authentication-file=FILE 从文件中获取凭据
  -P, --machine-pass 使用存储的机器账号密码
      --simple-bind-dn=DN 用于简单绑定的 DN
      --use-kerberos=desired|required|off 使用 Kerberos 身份验证
      --use-krb5-ccache=CCACHE 凭证缓存位置Kerberos
      --use-winbind-ccache 使用 winbind ccache验证
      --client-protection=sign|encrypt|off 配置使用的保护客户端连接

版本选项：
  -V, --version 打印版本
```
{% endcode %}

```shell
# 展示某个 IP 地址提供的共享文件夹
smclient -L IP -U username%password

# 使用 SMB 下载文件夹， C 是要下载的文件夹名称
smb: \testdir\> prompt
smb: \testdir\> recurse
smb: \testdir\> mget C
```
