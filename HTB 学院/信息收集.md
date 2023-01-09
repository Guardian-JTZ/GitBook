# 信息收集

![Snipaste\_2022-10-28\_21-25-57](https://typora-1311404666.cos.ap-beijing.myqcloud.com/img/20221029090246.png)

在信息收集过程中，我们需要从以下方面进行考虑：

| 区域    | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 域和子域  | 通常，我们会得到一个域，或者可能是属于某个组织的域和子域的列表。许多组织没有准确的资产清单，并且可能忘记了对外公开的域和子域。这是侦察阶段的重要组成部分。我们可能会遇到映射回范围内 IP 地址的各种子域，从而增加我们参与的整体攻击面。隐藏和被遗忘的子域可能具有旧/易受攻击的应用程序版本或具有附加功能的开发版本（例如 Python 调试控制台）。Bug 赏金计划通常会将范围设置为\*.inlanefreight.com，这意味着inlanefreight.com在此示例中，所有子域都在范围内（即acme.inlanefreight.com，admin.inlanefreight.com，以此类推）。我们还可能发现子域的子域。例如，假设我们发现了一些类似的东西admin.inlanefreight.com。然后，我们可以对这个子域运行进一步的子域枚举，也许会发现dev.admin.inlanefreight.com一个非常诱人的目标。有很多方法可以找到子域（被动和主动） |
| IP 范围 | 找到我们的目标拥有的额外 IP 范围可能会导致发现其他域和子域，并打开我们可能的攻击面更大                                                                                                                                                                                                                                                                                                                                                                                                           |
| 基础设施  | 我们希望尽可能多地了解我们的目标。我们需要知道我们的目标正在使用什么技术堆栈。他们的应用程序都是 ASP.NET 吗？他们使用 Django、PHP、Flask 等吗？正在使用哪些类型的 API/Web 服务？他们是否使用 WordPress、Joomla、Drupal 或 DotNetNuke 等内容管理系统 (CMS)，这些系统有我们可能遇到的漏洞和错误配置类型？我们还关心正在使用的 Web 服务器，例如 IIS、Nginx、Apache 和版本号。如果我们的目标是运行过时的框架或 Web 服务器，我们希望深入挖掘相关的 Web 应用程序。我们还对正在使用的后端数据库的类型（MSSQL、MySQL、PostgreSQL、SQLite、Oracle 等）感兴趣，因为这将使我们了解我们可能能够执行的攻击类型。                                                                                 |
| 虚拟主机  | 最后，我们要枚举虚拟主机 (vhosts)，它类似于子域，但表明一个组织在同一 Web 服务器上托管多个应用程序。                                                                                                                                                                                                                                                                                                                                                                                               |

## 被动信息收集

### 1. WHOIS

> 使用 WHOIS 命令或者使用在线网站 [domaintools](https://whois.domaintools.com/)

### 2. 域名解析

1. nslookup
2. DIG
3. 被动子域枚举：VirusTotal 、[certificates](https://search.censys.io/certificates) 、[crt](https://crt.sh)、[TheHarvester](https://github.com/laramies/theHarvester)
4. 被动基础设施识别 ： [Netcraft](https://www.netcraft.com/)、[互联网档案馆](https://en.wikipedia.org/wiki/Internet\_Archive)

## 主动信息收集

### 1. 基础设施是被

1. CURL
2. [Whatweb](https://www.morningstarsecurity.com/research/whatweb)
3. [Wappalyzer](https://www.wappalyzer.com/)
4. [WafW00f](https://github.com/EnableSecurity/wafw00f)
5. [Aquatone](https://github.com/michenriksen/aquatone)

### 2. 存活子域枚举

1. https://hackertarget.com/zone-transfer/
