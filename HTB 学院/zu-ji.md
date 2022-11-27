# 足迹

在进行渗透测试时，我们需要对目标的一些信息进行相关的收集以及掌握，关于收集的信息的相关内容如下： ![image-20221101215628752](https://typora-1311404666.cos.ap-beijing.myqcloud.com/img/20221101215635.png)

| 层                      | 描述                      | 信息类别                                           |
| ---------------------- | ----------------------- | ---------------------------------------------- |
| 1. Internet Presence   | 识别互联网存在和外部可访问的基础设施。     | 域、子域、虚拟主机、ASN、网络块、IP 地址、云实例、安全措施               |
| 2. Gateway             | 确定保护公司外部和内部基础设施的可能安全措施。 | 防火墙、DMZ、IPS/IDS、EDR、代理、NAC、网络分段、VPN、Cloudflare |
| 3. Accessible Services | 识别外部或内部托管的可访问接口和服务。     | 服务类型、功能、配置、端口、版本、接口                            |
| 4. Processes           | 识别与服务相关的内部流程、来源和目的地。    | PID、已处理数据、任务、源、目标                              |
| 5. Privileges          | 识别可访问服务的内部权限和特权。        | 组、用户、权限、限制、环境                                  |
| 6. OS Setup            | 识别内部组件和系统设置。            | 操作系统类型、补丁级别、网络配置、操作系统环境、配置文件、敏感私有文件            |

## 域信息

1. 查找的第一个存在点的是 SSL 证书，通常这样的证书用于多个域
2. 查找子域的另一个来源是 [crt.sh](https://crt.sh/)
3. 使用网络空间搜索引擎进行查找
4. 进行 DNS 记录的查找

## 云资源

1. 使用 Google 搜索语法进行查找
2. [domain.glass](https://domain.glass/) ： 告诉我们关于公司基础设施的信息
3. [grayhatwarfare](https://buckets.grayhatwarfare.com/) ： 可以进行许多不同的搜索

## 基于主机服务的枚举

* FTP
* SMB
* NFS
* DNS
* SMTP
* IMAP/POP3
* SNMP
* MySQL
* MSSQL
* IPMI

## 远程管理协议

### 1. Linux

* SSH
* Rsync
