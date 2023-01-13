---
description: 端口扫描
---

# masscan

> 该工具只适合扫描端口

## 安装

```bash
sudo apt install masscan
```

## 原理

> MASSCAN不建立完整的TCP连接，收到SYN/ACK之后，发送RST结束连接。选项--banners除外。

<figure><img src="../../.gitbook/assets/masscan.webp" alt=""><figcaption></figcaption></figure>

## 使用

```bash
# 扫描 B 类子网的 443 端口
masscan 192.168.0.0/16 -p 443
# 扫描指定范围内端口
masscan 192.168.0.0/16 -p22-25
```

## 参数介绍

```bash
-p 《ports,–ports 《ports》》 指定端口进行扫描
–banners 获取banner信息，支持少量的协议
–rate 《packets-per-second》 指定发包的速率
-c 《filename》, --conf 《filename》 读取配置文件进行扫描
–echo 将当前的配置重定向到一个配置文件中
-e 《ifname》 , --adapter 《ifname》 指定用来发包的网卡接口名称
–adapter-ip 《ip-address》 指定发包的IP地址
–adapter-port 《port》 指定发包的源端口
–adapter-mac 《mac-address》 指定发包的源MAC地址
–router-mac 《mac address》 指定网关的MAC地址
–exclude 《ip/range》 IP地址范围黑名单，防止masscan扫描
–excludefile 《filename》 指定IP地址范围黑名单文件
–includefile，-iL 《filename》 读取一个范围列表进行扫描
–ping 扫描应该包含ICMP回应请求
–append-output 以附加的形式输出到文件
–iflist 列出可用的网络接口，然后退出
–retries 发送重试的次数，以1秒为间隔
–nmap 打印与nmap兼容的相关信息
–http-user-agent 《user-agent》 设置user-agent字段的值
–show [open,close] 告诉要显示的端口状态，默认是显示开放端口
–noshow [open,close] 禁用端口状态显示
–pcap 《filename》 将接收到的数据包以libpcap格式存储
–regress 运行回归测试，测试扫描器是否正常运行
–ttl 《num》 指定传出数据包的TTL值，默认为255
–wait 《seconds》 指定发送完包之后的等待时间，默认为10秒
–offline 没有实际的发包，主要用来测试开销
-sL 不执行扫描，主要是生成一个随机地址列表
–readscan 《binary-files》 读取从-oB生成的二进制文件，可以转化为XML或者JSON格式.
–connection-timeout 《secs》 抓取banners时指定保持TCP连接的最大秒数，默认是30秒。
```

## 输出

* xml：只需使用参数-oX。或者，使用参数--output-format xml和--output-filename \<filename>。
* 二进制：这是masscan内置格式。它产生的文件要小得多，因此，当我扫描Internet时，磁盘不会填满。但是，它们需要解析。命令行选项--readscan将读取二进制扫描文件。--readscan与该-oX选项一起使用将生成结果文件的XML版本。
* grepable：这是Nmap -oG输出的实现，可以通过[命令行工具](https://cloud.tencent.com/product/cli?from=10680)轻松地对其进行解析。只需使用参数-oG。或者，使用参数--output-format grepable和 --output-filename \<filename>。
* json：这会将结果保存为JSON格式。只需使用参数-oJ。或者，使用参数--output-format json和 --output-filename \<filename>。
* list：这是一个简单的列表，每行一个主机和端口对。只需使用参数-oL。或者，使用参数 --output-format list和--output-filename \<filename>。格式为：
