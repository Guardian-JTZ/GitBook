# ICMP

## 介绍

1. ICMP 协议是一个 `网络层`协议，基于 IP 协议工作 `ICMP 只能配合 IPV4 使用，如果是 IPV6 的话需要使用 ICMPV6`
2. 功能：
   * 确认 IP 包是否成功到达目标地址 (只关心对应主机是否存活，并不关心对应的主机端口的开放情况)
   * 通知在发送过程中 IP 包被丢弃的原因

## traceroute

* traceroute 也是基于 ICMP 协议实现的
* 功能： 到达目标主机之前经历多少路由器

## 参考

* [参考链接](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E6%8E%A7%E5%88%B6%E6%B6%88%E6%81%AF%E5%8D%8F%E8%AE%AE)
