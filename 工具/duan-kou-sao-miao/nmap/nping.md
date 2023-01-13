# nping

## 介绍&安装

1. Nping允许用户发送多种协议（TCP、UDP、ICMP和ARP协议）的数据包。可以调整协议头中的字段，例如可以设置TCP和UDP的源端口和目的端口。
2. 主要功能
   1. 发送ICMP echo请求
   2. 对网络进行压力测试
   3. ARP毒化攻击
   4. DoS攻击
   5. 支持多种探测模式
   6. 可以探测多个主机的多个端口

## 参数

```shell
# 命令格式
nping [Probe mode][Options] {target specification}
```

```shell
PROBE MODES:探测模式
  --tcp-connect                    : 非特权TCP连接探测模式。
  --tcp                            : TCP 探测模式
  --udp                            : UDP 探测模式
  --icmp                           : ICMP 探测模式
  --arp                            : ARP/RARP 探测模式
  --tr, --traceroute               : 追踪路由模式 (只能和 TCP/UDP/ICMP 模式一起使用).
TCP CONNECT MODE: TCP 连接模式
   -p, --dest-port <port spec>     : 设置目标端口
   -g, --source-port <portnumber>  : 设置源端口
TCP PROBE MODE: TCP 探测模式
   -g, --source-port <portnumber>  : 设置源端口
   -p, --dest-port <port spec>     : 设置目标端口
   --seq <seqnumber>               : Set sequence number.
   --flags <flag list>             : Set TCP flags (ACK,PSH,RST,SYN,FIN...)
   --ack <acknumber>               : Set ACK number.
   --win <size>                    : Set window size.
   --badsum                        : 使用随机无效校验和
UDP PROBE MODE: UDP 探测模式
   -g, --source-port <portnumber>  : 设置源端口
   -p, --dest-port <port spec>     : 设置目标端口
   --badsum                        : 使用随机无效校验和.
ICMP PROBE MODE: ICMP 探测模式
  --icmp-type <type>               : ICMP 类型.
  --icmp-code <code>               : ICMP code.
  --icmp-id <id>                   : 设置标识符
  --icmp-seq <n>                   : 设置序列号
  --icmp-redirect-addr <addr>      : 设置重定向地址
  --icmp-param-pointer <pnt>       : Set parameter problem pointer.
  --icmp-advert-lifetime <time>    : Set router advertisement lifetime.
  --icmp-advert-entry <IP,pref>    : Add router advertisement entry.
  --icmp-orig-time  <timestamp>    : Set originate timestamp.
  --icmp-recv-time  <timestamp>    : Set receive timestamp.
  --icmp-trans-time <timestamp>    : Set transmit timestamp.
ARP/RARP PROBE MODE:
  --arp-type <type>                : Type: ARP, ARP-reply, RARP, RARP-reply.
  --arp-sender-mac <mac>           : Set sender MAC address.
  --arp-sender-ip  <addr>          : Set sender IP address.
  --arp-target-mac <mac>           : Set target MAC address.
  --arp-target-ip  <addr>          : Set target IP address.
IPv4 OPTIONS:
  -S, --source-ip                  : Set source IP address.
  --dest-ip <addr>                 : Set destination IP address (used as an 
                                     alternative to {target specification} ). 
  --tos <tos>                      : Set type of service field (8bits).
  --id  <id>                       : Set identification field (16 bits).
  --df                             : Set Don't Fragment flag.
  --mf                             : Set More Fragments flag.
  --ttl <hops>                     : Set time to live [0-255].
  --badsum-ip                      : Use a random invalid checksum. 
  --ip-options <S|R [route]|L [route]|T|U ...> : Set IP options
  --ip-options <hex string>                    : Set IP options
  --mtu <size>                     : Set MTU. Packets get fragmented if MTU is
                                     small enough.
IPv6 OPTIONS:
  -6, --IPv6                       : Use IP version 6.
  --dest-ip                        : Set destination IP address (used as an
                                     alternative to {target specification}).
  --hop-limit                      : Set hop limit (same as IPv4 TTL).
  --traffic-class <class> :        : Set traffic class.
  --flow <label>                   : Set flow label.
ETHERNET OPTIONS:
  --dest-mac <mac>                 : Set destination mac address. (Disables
                                     ARP resolution)
  --source-mac <mac>               : Set source MAC address.
  --ether-type <type>              : Set EtherType value.
PAYLOAD OPTIONS:
  --data <hex string>              : Include a custom payload.
  --data-string <text>             : Include a custom ASCII text.
  --data-length <len>              : Include len random bytes as payload.
ECHO CLIENT/SERVER:
  --echo-client <passphrase>       : Run Nping in client mode.
  --echo-server <passphrase>       : Run Nping in server mode.
  --echo-port <port>               : Use custom <port> to listen or connect.
  --no-crypto                      : Disable encryption and authentication.
  --once                           : Stop the server after one connection.
  --safe-payloads                  : Erase application data in echoed packets.
TIMING AND PERFORMANCE:
  Options which take <time> are in seconds, or append 'ms' (milliseconds),
  's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m, 0.25h).
  --delay <time>                   : Adjust delay between probes.
  --rate  <rate>                   : Send num packets per second.
MISC:
  -h, --help                       : Display help information.
  -V, --version                    : Display current version number. 
  -c, --count <n>                  : Stop after <n> rounds.
  -e, --interface <name>           : Use supplied network interface.
  -H, --hide-sent                  : Do not display sent packets.
  -N, --no-capture                 : Do not try to capture replies.
  --privileged                     : Assume user is fully privileged.
  --unprivileged                   : Assume user lacks raw socket privileges.
  --send-eth                       : Send packets at the raw Ethernet layer.
  --send-ip                        : Send packets using raw IP sockets.
  --bpf-filter <filter spec>       : Specify custom BPF filter.
OUTPUT:
  -v                               : Increment verbosity level by one.
  -v[level]                        : Set verbosity level. E.g: -v4
  -d                               : Increment debugging level by one.
  -d[level]                        : Set debugging level. E.g: -d3
  -q                               : Decrease verbosity level by one.
  -q[N]                            : Decrease verbosity level N times
  --quiet                          : Set verbosity and debug level to minimum.
  --debug                          : Set verbosity and debug to the max level.
EXAMPLES:
  nping scanme.nmap.org
  nping --tcp -p 80 --flags rst --ttl 2 192.168.1.1
  nping --icmp --icmp-type time --delay 500ms 192.168.254.254
  nping --echo-server "public" -e wlan0 -vvv 
  nping --echo-client "public" echo.nmap.org --tcp -p1-1024 --flags ack
```
