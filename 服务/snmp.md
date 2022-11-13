# SNMP

## 介绍

SNMP 被创建用于监控网络设备，同时该协议还可以用于远程处理配置任务和更改设置，支持 SNMP 的硬件包括路由器、交换机、服务器、物联网设备和许多其他的也可以使用该协议进行查询和控制的设备。

为了让 SNMP 客户端和服务器交换各自的值，可用的 SNMP 对象必须具有双方都知道的唯一地址。这种寻址机制是使用 SNMP 成功传输数据和网络监控的绝对先决条件。

### 1. MIB

为了让SNMP 可以访问不同的客户端-服务器进行工作，创建了一种存储信息的格式 -- MIB ， 在 MIB 中所有可查询的 SNMP 对象都以标准化的树形层次结构列出，包含 OID、唯一地址/名称、相应对象的类型、访问权限和描述的信息

### 2. OID

OID 表示分层命名空间中的节点，一系列数字唯一的标识每一个节点，从而确定节点在树中的位置

## 工具

### 1. snmpwalk

> 查询 OID 和信息

```shell
snmpwalk -v2c -c public 10.129.14.128
```

### 2. Onesixtyone

> 暴力破解字符串的名称

```shell
sudo apt install onesixtyone
onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt 10.129.14.128

# 一旦知道一个社区字符串 就可以使用下方工具进行暴力破解 OID 以及相关信息
sudo apt install braa
braa public@10.129.14.128:.1.3.6.*
```
