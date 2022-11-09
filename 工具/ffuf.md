# FFUF

\[TOC]

> * 目录模糊测试
> * 对文件和扩展名进行模糊测试
> * 识别隐藏的虚拟主机
> * PHP 参数的模糊测试
> * 参数值的模糊测试

```shell
fuff -h
HTTP OPTIONS:
  -H                  请求 Header 信息， "Name:Value" 使用
  -X                  使用的 HTTP 方法
  -b                  Cookie 数据 "NAME1=VALUE1; NAME2=VALUE2" 
  -d                  POST 数据
  -http2              使用 HTTP2 协议 (default: false)
  -ignore-body        不获取响应的内容 (default: false)
  -r                  跟随重定向 (default: false)
  -recursion          递归扫描，仅支持 FUZZ 关键字，并且 -u 必须以它结尾(default: false)
  -recursion-depth    最大递归深度(default: 0)
  -recursion-strategy 递归策略: 默认基于重定向, "greedy" 递归所有匹配(default: default)
  -replay-proxy       使用次带来重放匹配的请求
  -sni                目标 TLS SNI 不支持 FUZZ 关键字
  -timeout            HTTP 请求超时(default: 10)
  -u                  目标地址
  -x                  代理  http://127.0.0.1:8080 or socks5://127.0.0.1:8080

常规选项:
  -V                  显示版本信息 (default: false)
  -ac                 自动校准过滤选项 (default: false)
  -acc                自定义自动校准字符串。可以多次使用。暗示 -ac
  -c                  着色输出 (default: false)
  -config             从文件加载配置
  -json               JSON 输出，打印以换行符分隔的 JSON 记录 (default: false)
  -maxtime            整个进程的最大运行时间 (default: 0)
  -maxtime-job        每个作业的最大运行时间 (default: 0)
  -noninteractive     禁用交互式控制台功能 (default: false)
  -p                  请求之间的“延迟”秒数，或随机延迟范围。例如“0.1”或“0.1-2.0”
  -rate               每秒请求的速率 (default: 0)
  -s                  不打印附加信息（静默模式） (default: false)
  -sa                 停止所有错误情况。暗示 -sf 和 -se (default: false)
  -se                 停止虚假错误 (default: false)
  -sf                 当 > 95% 的响应返回 403 Forbidden 时停止 (default: false)
  -t                  并发线程数  (default: 40)
  -v                  详细输出，打印完整的 URL 和重定向位置（如果有）和结果 (default: false)

匹配选项：
  -mc                  匹配 HTTP 状态代码，或“全部”匹配所有内容。 （默认：200,204,301,302,307,401,403,405,500）
  -ml                  匹配响应的行数
  -mr                  匹配正则表达式
  -ms                  匹配 HTTP 响应大小
  -mt                  匹配第一个响应字节的毫秒数，大于或小于。例如：>100 或 <100
  -mw                  匹配响应中的单词数量

过滤器选项：
  -fc                  从响应中过滤 HTTP 状态代码。逗号分隔的代码和范围列表
  -fl                  按响应的行数过滤。行数和范围的逗号分隔列表
  -f                   过滤正则表达式
  -fs                  过滤 HTTP 响应大小。逗号分隔的大小和范围列表
  -ft                  按毫秒数过滤到第一个响应字节，大于或小于。例如：>100 或 <100
  -fw                  按响应的字数过滤。逗号分隔的字数和范围列表

输入选项：
  -D                  DirSearch 词表兼容模式。与 -e 标志一起使用。 （默认：假）
  -e                  逗号分隔的扩展列表。扩展 FUZZ 关键字。
  -ic                 忽略单词表注释（默认值：false）
  -input-cmd          命令产生输入。使用此输入法时需要 --input-num。覆盖 -w。
  -input-num          要测试的输入数。与 --input-cmd 结合使用。 （默认值：100）
  -input-shell        用于运行命令的 Shell
  -mode               多词表操作模式。可用模式：集束炸弹、干草叉、狙击手（默认：集束炸弹）
  -request            包含原始 http 请求的文件
  -request-proto      与原始请求一起使用的协议（默认值：https）
  -w Wordlist         文件路径和（可选）关键字，用冒号分隔。例如。 '/path/to/wordlist:KEYWORD'

输出选项：
  -debug-log           将所有内部日志记录写入指定文件。
  -o                   将输出写入文件
  -od                  存储匹配结果的目录路径。
  -of                  输出文件格式。可用格式：json、ejson、html、md、csv、ecsv（或所有格式的“全部”）（默认值：json）
  - 或者如果我们没有结果，则不要创建输出文件（默认值：false）
```

## 1. 目录模糊测试

```shell
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

## 2. 页面模糊测试

```shell
# 猜解页面的后缀
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
# 猜解页面名称
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php
```

## 3. 递归模糊测试

```shell
# -recursion : 启用递归扫描
# -recursion-depth : 指定深度
# -e : 指定扩展名
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```

## 4. 子域模糊测试

```shell
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.hackthebox.eu/
```

## 5. 虚拟主机模糊测试

```shell
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'
```

## 6. 过滤结果

```shell
# -fs :过滤响应大小为 900 K 的数据
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900
```

## 7. 参数模糊测试

```shell
# 测试 GET 参数
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs 900

# 测试 POST 参数
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs 900

# 值模糊测试
ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs 900
```
