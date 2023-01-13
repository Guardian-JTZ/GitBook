# TLS/SSL

## 介绍

* TLS 又称 传输层安全性协议，是 SSL(安全套接字) 的修改版本
* SSL 包含 记录层和传输层， 记录层协议确定传输层数据的`封装格式`，传输层安全协议`使用 X.509 认证`，之后使用`非对称加密算法`演算来对通信方做身份认证，之后`交换对称密钥`作为会话密钥 (Session Key)。这个会谈密匙是用来将通信两方交换的资料做加密，保证两个应用间通信的保密性和可靠性，使客户与服务器应用之间的通信不被攻击者窃听。
*   TLS

    > TLS协议采用[主从式架构](https://zh.wikipedia.org/wiki/%E4%B8%BB%E5%BE%9E%E5%BC%8F%E6%9E%B6%E6%A7%8B)模型，用于在两个应用程序间透过网络创建起安全的连线，防止在交换资料时受到[窃听](https://zh.wikipedia.org/wiki/%E7%AB%8A%E8%81%BD)及篡改。
    >
    > TLS协议的优势是与高层的[应用层](https://zh.wikipedia.org/wiki/%E5%BA%94%E7%94%A8%E5%B1%82)协议（如[HTTP](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)、[FTP](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)、[Telnet](https://zh.wikipedia.org/wiki/Telnet)等）无耦合。应用层协议能透明地运行在TLS协议之上，由TLS协议进行创建加密通道需要的协商和认证。应用层协议传送的数据在通过TLS协议时都会被加密，从而保证通信的私密性。
    >
    > TLS协议是可选的，必须配置客户端和服务器才能使用。主要有两种方式实现这一目标：一个是使用统一的TLS协议[端口](https://zh.wikipedia.org/wiki/%E9%80%9A%E8%A8%8A%E5%9F%A0)（例如：用于[HTTPS](https://zh.wikipedia.org/wiki/HTTPS)的端口443）；另一个是客户端请求服务器连接到TLS时使用特定的协议机制（例如：电子邮件常用的[STARTTLS](https://zh.wikipedia.org/wiki/STARTTLS)）。一旦客户端和服务器都同意使用TLS协议，他们通过使用一个握手过程协商出一个有状态的连接以传输数据\[[1\]](https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E5%B1%A4%E5%AE%89%E5%85%A8%E6%80%A7%E5%8D%94%E5%AE%9A#cite\_note-1)。通过握手，客户端和服务器协商各种参数用于创建安全连接：
    >
    > * 当客户端连接到支持TLS协议的服务器要求创建安全连接并列出了受支持的[密码包](https://zh.wikipedia.org/wiki/%E5%AF%86%E7%A0%81%E5%A5%97%E4%BB%B6)（包括[加密算法](https://zh.wikipedia.org/wiki/%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95)、[散列算法](https://zh.wikipedia.org/wiki/%E6%95%A3%E5%88%97%E7%AE%97%E6%B3%95)等），握手开始。
    > * 服务器从该列表中决定密码包，并通知客户端。
    > * 服务器发回其[数字证书](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E8%AF%81%E4%B9%A6)，此证书通常包含服务器的名称、受信任的[证书颁发机构](https://zh.wikipedia.org/wiki/%E8%AF%81%E4%B9%A6%E9%A2%81%E5%8F%91%E6%9C%BA%E6%9E%84)（CA）和服务器的公钥。
    > * 客户端确认其颁发的证书的有效性。
    > * 为了生成会话密钥用于安全连接，客户端使用服务器的公钥加密随机生成的密钥，并将其发送到服务器，只有服务器才能使用自己的私钥解密。
    > * 利用随机数，双方生成用于加密和解密的对称密钥。这就是TLS协议的握手，握手完毕后的连接是安全的，直到连接（被）关闭。如果上述任何一个步骤失败，TLS握手过程就会失败，并且断开所有的连接。
