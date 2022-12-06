---
description: SendEmail 是一个轻量级、完全基于命令行的 SMTP 电子邮件代理
---

# sendEmail

## 介绍

### 1. 安装

SendEmail --> [Github](https://github.com/mogaal/sendemail)

```bash
sudo apt-get install sendemail
```

## 使用

### 1. 帮助文档

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>sendemail --help
</strong><strong>Required：
</strong>    -f 来自（发件人）电子邮件地址的地址
    * 至少需要一个收件人通过 -t、-cc 或 -bcc
    * 消息正文需要通过 -m、STDIN 或 -o message-file=FILE

  Common：
    -t ADDRESS [ADDR ...] 到电子邮件地址
    -u SUBJECT 邮件主题
    -m MESSAGE 消息体
    -s SERVER[:PORT] smtp 邮件中继，默认为 localhost:25

  Optional：
    -a FILE [FILE ...] 文件附件
    -cc ADDRESS [ADDR ...] 抄送电子邮件地址
    -bcc ADDRESS [ADDR ...] bcc 电子邮件地址
    -xu USERNAME 用于 SMTP 身份验证的用户名
    -xp PASSWORD 用于SMTP认证的密码

  Paranormal：
    -b BINDADDR[:PORT] 本地主机绑定地址
    -l LOGFILE 记录到指定文件
    -v 冗长，多次使用以获得更好的效果
    -q 保持安静（即没有 STDOUT 输出）
    -o NAME=VALUE 高级选项，详情请尝试：--help misc
        -o 消息内容类型=&#x3C;自动|文本|html|其他>
        -o 消息文件=FILE -o 消息格式=raw
        -o message-header=HEADER -o message-charset=字符集
        -o 回复=ADDRESS -o timeout=SECONDS
        -o 用户名=用户名 -o 密码=密码
        -o tls=&#x3C;自动|是|否> -o fqdn=FQDN
</code></pre>

### 2. 使用举例

```bash
#发送者使用任意的邮箱方式发送邮件
robin@SZDB:~> sendEmail -f robinson@oracle.com \
> -t robinson.cheng@trade.com \
> -u "Test Mail" \
> -m "This is a test mail from Robinson"
Dec 18 14:10:23 szdb sendEmail[18494]: Email was sent successfully!
```
