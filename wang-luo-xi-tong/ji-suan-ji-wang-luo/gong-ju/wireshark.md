# WireShark

> [WireShark 官网](https://wiki.wireshark.org/Home)

## 收集方法

### Network Taps

一个物理机器，可以在电缆直接进行物理窃听，我们可以使用这个方法进行 `嗅探和捕获数据包`

常见的有两种方法：

1. 使用硬件窃听线路并在通过时拦截流量 ![img](https://img-blog.csdnimg.cn/img\_convert/7646f2373929409ed32f6a4c782f4362.gif)
2. 使用内联网络分路器，分路器可以在数据包通过时复制数据信息 ![img](https://img-blog.csdnimg.cn/img\_convert/c7133e4cfd31f7765342aae0b8ce0b8e.jpeg)

### MAC Floods

> 不断的向交换机施加压力以填满其 CAM 数据表，一旦 CAM 数据表填满，交换机就不在接受新的 MAC 地址，为了保证网络获取，交换机将先所有端口发送数据包

### ARP 中毒

> 通过 ARP 中毒可以将流量从主机重定向到正在监听的主机

## 过滤捕获

### 语法

> * Scope (范围) : host、net、port、portrange、addr
> * Direction (方向) : src、dst
> * protocol (协议) : ether、wlan、ip、ip6、arp、rarp、tcp、udp

### 比较运算符

| English | 描述 | Example                  |
| ------- | -- | ------------------------ |
| eq      | == | Equal                    |
| ne      | != | Not equal                |
| gt      | >  | 大于                       |
| lt      | <  | 小于                       |
| ge      | >= | Greater than or equal to |
| le      | <= | Less than or equal to    |

### 逻辑操作

> * and
> * or
> * eq

常见基本过滤

```shell
ip.src == ip	# 来源地址
ip.src == ip and ip.dst == ip 	# 来源地址和目标地址
tcp.port eq 123		# 端口
```

### Filter: contains

| **Filter**      | **contains**                          |
| --------------- | ------------------------------------- |
| **Type**        | 比较运算符                                 |
| **Description** | 在`数据包`中搜索一个值,区分大小写                    |
| **Example**     | Find all "Apache" servers.            |
| **Workflow**    | 找出所有数据包的 server 字段包含 apache 的HTTP 数据包 |
| **Usage**       | `http.server contains "Apache"`       |

### Filter: matches

| **Filter**      | **matches**                         |
| --------------- | ----------------------------------- |
| **Type**        | 比较运算符                               |
| **Description** | 按照`正则表达式`匹配，不区分大小                   |
| **Example**     | Find all .php and .html pages.      |
| **Workflow**    | 找出所有数据包的 host 字段匹配关键字 .php 或者 .html |
| **Usage**       | \`http.host matches ".(php          |

### Filter: in

| **Filter**      | **in**                      |
| --------------- | --------------------------- |
| **Type**        | 标志位寻找                       |
| **Description** | 搜索特定范围的值或字段                 |
| **Example**     | 查找所有的 80, 443 or 8080.      |
| **Workflow**    | 找出所有数据报端口为 80、443、8080      |
| **Usage**       | `tcp.port in {80,443,8080}` |

### Filter： upper

| **Filter**      | **upper**                                         |
| --------------- | ------------------------------------------------- |
| **Type**        | Function                                          |
| **Description** | 将字符串转换为大写                                         |
| **Example**     | Find all "APACHE" servers.                        |
| **Workflow**    | 查找所有 HTTP 数据报的 server HTTP 头部字段中包含 APACHE 关键字的数据包 |
| **Usage**       | `upper(http.server) contains "APACHE"`            |

### Filter: lower

| **Filter**      | **lower**                                         |
| --------------- | ------------------------------------------------- |
| **Type**        | Function                                          |
| **Description** | 将字符串转换为小写                                         |
| **Example**     | Find all "apache" servers.                        |
| **Workflow**    | 查找所有 HTTP 数据包中 server HTTP 头部字段中包含 apache 关键字的数据包 |
| **Usage**       | `lower(http.server) contains "apache"`            |

### Filter: string

| **Filter**      | **string**                                |
| --------------- | ----------------------------------------- |
| **Type**        | Function                                  |
| **Description** | 将非字符串转换为字符串                               |
| **Example**     | 找出所有奇数帧                                   |
| **Workflow**    | 将所有帧编号字段转换为字符串值，并列出以奇数结尾的帧                |
| **Usage**       | `string(frame.number) matches "[13579]$"` |

## 数据包分析

> wireshark 截取到的数据包由 `OSI 5 到 7 层`组成
