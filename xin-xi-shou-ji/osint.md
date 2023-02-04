# OSINT

## Google

> 我们可以通过 Google 来查找目标的信息

## Wappalyzer

> 浏览器扩展工具，可以帮助识别网站使用的技术

## Watback Machine (回程机)

> 这个网站保存的可以理解为是一个网页的缓存
>
> * [Watback Machine](https://archive.org/web/)

## Github

> 我们可以从 Github 上来查找目标的存储库、源代码、密码等信息

## S3 Buckets

> S3 Buckets 是亚马逊 AWS 提供的一种存储服务，允许人们将文件甚至静态网站内容保存在可通过 HTTP 和 HTTPS 访问的云中。文件的所有者可以设置访问权限，使文件公开、私有甚至可写。有时，这些访问权限设置不正确，无意中允许访问不应向公众开放的文件。S3 存储桶的格式为 \*\*http(s)://{name}.\[s3.amazonaws.com]\*\*其中 {name} 由所有者决定，例如 tryhackme-assets.s3.amazonaws.com 可以通过多种方式发现 S3 存储桶，例如在网站的页面源、GitHub 存储库中查找 URL，甚至自动化该过程。一种常见的自动化方法是使用公司名称后跟常用术语，例如 **{name}** -assets、 **{name}** -www、 **{name}** -public、 **{name}** -private 等。
