---
description: 关于 Kerberos 认证的介绍
---

# Kerberos--介绍

## 介绍

Kerberos 是一种网络认证协议，其设计目标是通过密钥系统为客 户机 / 服务器应用程序提供强大的认证服务。该认证过程的实现不 依赖于主机操作系统的认证，无需基于主机地址的信任，不要求 网络上所有主机的物理安全，并假定网络上传送的数据包可以被 任意地读取、修改和插入数据。在以上情况下， Kerberos 作为一 种可信任的第三方认证服务，是通过传统的密码技术(如:共享 密钥)执行认证服务的

## 参与角色

1. 客户端
2. 服务端
3. KDC （Key Distribution Center）= DC
   1. 作用： 管理票据、认证票据、分发票据
   2. 组成：
      1. Authentication Service (身份验证服务器): 为客户端生成 TGT 的服务
      2. Ticket Granting Service (票证授予服务器): 为客户端生成某个服务的票证
4. 基础概念：
   1. 票据： 是网络对象互相访问的凭证
   2. TGT （Ticket Granting Ticket） ： 入场券，通过TGT 来获取凭据，是一种临时凭证的存在
5. Account Database : 存储所有的客户端的白名单，只有存在于白名单的客户端才能申请到 TGT

<figure><img src="../../../.gitbook/assets/Kerberos1.png" alt=""><figcaption></figcaption></figure>

## 域认证

### 1. 粗略流程

1. client 向 Kerberos 服务请求，希望可以获取访问 Server 的权限， Kerberos 收到消息后，先判断 client 是否在白名单中。这一阶段发生在 AS 中，通过 Account Database 存储的白名单黑名单来辨别 client ，成功后，向 client 返回 TGT
2. client 收到 TGT 后，继续向 Kerberos 请求，希望获取访问 server 的权限， Kerberos 得到消息后，这时候通过 client 消息中的TGT，判断出了client拥有了这个权限，给了client 访问 server 的权限票证 TGS
3. client 得到 TGS 后就可以成功访问 server ，只是针对这个 server

<figure><img src="../../../.gitbook/assets/Kerberos2.png" alt=""><figcaption></figcaption></figure>

### 2. 详细流程

<figure><img src="../../../.gitbook/assets/Kerberos3.png" alt=""><figcaption></figcaption></figure>

#### 1. Client & AS

1. Client 向 AS 发出请求
   1. User Name/ID
   2. Service Name/ID
   3. IP address
   4. 当前时间戳
2. KDC当中的AS（Authentication Server）接收请求（AS是KDC中专门用来认证客户端身份的认证服务器）后去kerberos认证数据库中根据用户名查找是否存在该用户，此时只会查找是否有相同用户名的用户，并不会判断身份的可靠性
   1. 不存在用户名，认证失败，服务结束
   2. 存在用户名，AS 认证中心认为用户存在，便返回响应给客户端
      1. 第一部分内容是`使用客户端密钥加密`的一段内容，其中包括用于客户端和TGS间通信的 TGS\_Session\_key(CT\_SK)、客户端即将访问的TGS的 Name 、TGT的有效时间、一个当前时间戳 。该部分内容使用客户端密钥加密，所以客户端在拿到该部分内容时可以通过自己的密钥解密。如果是一个假的客户端，那么他是不会拥有真正客户端的密钥的，因为该密钥也从没在网络中进行传输过。这也同时认证了客户端的身份，如果是假客户端会由于解密失败从而终端认证流程。
      2. 第二部分内容称为TGT，他叫做票据授予票据，客户端需要使用TGT去KDC中的TGS（票据授予中心）获取访问网络服务所需的 Ticket（服务授予票据），TGT中包含的内容有User Name/ID、TGS Name/ID、IP、当前时间戳、TGT 的有效时间、一把用于客户端和TGS间进行通信的 TGS\_Session\_key(CT\_SK)。整个TGT`使用TGS密钥加密`，客户端是解密不了的，由于密钥从没有在网络中传输过，所以也不存在密钥被劫持破解的情况

#### 2. Client & TGS

> 客户端收到来最 AS 的响应后，客户端使用自己的密钥来对`第一部分`内容解密。解密后根据响应中的时间戳与自己请求时的时间之间差值比较如果大于五分钟认为该 AS 是伪造的，认证失败。如果时间戳合理，客户端便准备向 TGS 发起请求，这次请求的目的是为： **`获取能够访问目标网络服务的服务授予票据Ticket`**

1. Client ：
   1. Client 使用 TGS\_Session\_key(CT\_SK) 将自己的客户端消息加密，发送给客户端，加密的内容： User Name/ID、Timestamp
   2. 将自己要访问的 Server 服务以明文的形式发送
   3. 将第一次通信中收到的 TGT 原封不动的发送
2. TGS：
   1. TGS 首先根据客户端传输的 Server 服务查看当前 Kerberos 系统是否存在被用户访问的该服务，如果不存在认证失败
   2. TGS 使用自己的密钥对 TGT 中内容进行解密，根据解密消息判断：
      1. 时间戳： 根据时间戳判断此次消息是否可靠有效
   3. 使用 TGT 中解密出来的 TGS\_session\_Key 对客户端发送的加密内容进行解密，取出的用户信息与 TGT 中用户信息进行比对，如果全部相同则认为客户端身份正确
   4. TGS 将当前用户信息存储的 TGS Cash 中
   5. 此时 TGS 将返回响应给客户端：
      1. 第一部分 : 使用 TGS Serssion Key 加密的内容，其中包括 Service Name/ID、时间戳、ST 有效时间。
      2. 第二部分: 用于客户端访问网络服务的使用Server密码加密的ST（Servre Ticket), 其中包括 User Name/ID、Service name/ID、时间戳、IP 地址、ST 有效时间、用于客户端和服务端之间通信的 CS\_SK（Session Key）

#### 3. 第三次通信

> 客户端收到来自 TGS 的消息后，使用缓存的 Service\_Session\_key 对收到的第一部分内容解密，检查时间戳

1. 客户端：
   1. 使用解密的内容中的 Service\_Session\_key 加密信息，信息包括： User Name/ID 、时间戳
   2. 将收到的 Servre Ticket 原封不动的发送给 Service
2. 服务端：
   1. 时间自己的密钥解密 Servre Ticket ，核对时间戳后将消息中的 Service\_Session\_key 取出，并解密客户端发送的消息，获取经过 TGS 认证后的客户端消息，此时他将这部分信息和客户端第二部分内容带来的自己的信息进行比对，最终确认该客户端就是经过了KDC认证的具有真实身份的客户端
   2. 将客户信息保存在 Service Cash 中
   3. 返回一段使用 Service\_Session\_key 加密的响应给客户端，客户端收到请求后，使用缓存在本地的 Service\_Session\_key 解密确认服务daunt的身份
3. 客户端收到响应后：
   1. 将 Servre Ticket 保存到 User Cash 中

## 参考文章&视频

1. youtube Kerberos --> [链接](https://www.youtube.com/watch?v=5N242XcKAsM)
2. 详解kerberos认证原理 --> [链接](https://blog.csdn.net/rlnLo2pNEfx9c/article/details/122871829?ops\_request\_misc=%257B%2522request%255Fid%2522%253A%2522167098486216800215021109%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D\&request\_id=167098486216800215021109\&biz\_id=0\&utm\_medium=distribute.pc\_search\_result.none-task-blog-2\~all\~top\_click\~default-2-122871829-null-null.142^v68^pc\_rank\_34\_queryrelevant25,201^v4^add\_ask,213^v2^t3\_control1\&utm\_term=kerberos\&spm=1018.2226.3001.4187)
3. #### Kerberos原理--经典对话 中文版 --> [链接](https://github.com/SnowMeteors/KerberosPrinciple-Chinese)
