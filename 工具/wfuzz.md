# WFUZZ

\[TOC]

[WFUZZ 使用文档](https://wfuzz.readthedocs.io/en/latest/user/getting.html)

> 功能：
>
> * 路径遍历
> * 可预测的认证
> * 可预测的资源定位
> * 注入
> * 认证漏洞
> * 不安全的直接对象引用

## 一、入门

```shell
# 最简单的一个命令执行
wfuzz -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ

# 输出结果
# ID ： 执行顺序排列的编号
# Response ： HTTP 的响应
# Lines ： HTTP 响应行数
# Word : HTTP 响应的字数
# Chars : HTTP 响应的字符数
# Request ： 有效负载
==================================================================
ID      Response   Lines      Word         Chars          Request
==================================================================
```

### 1. 获取帮助

```shell
wfuzz -h # 获取帮助

选项:
	-h                        : 获取帮助
	--version                 : 获取版本
	-e <type>                 : 展示可用模块encoders/payloads/iterators/printers/scripts
	
	-c                        : 输出颜色
	-v                        : 详细信息
	--interact                : 可以与程序进行交互
	
	-p addr                   : 使用 ip:port:type  格式的代理
	-t N                      : 指定并发连接数
	-s N                      : 指定请求时间延迟
	-R depth                  : 递归路径级别
	-D depth                  : 最大链接深度级别
	-L, --follow              : 跟随 HTTP 重定向
	-u url                    : 指定请求 URL
	-z payload                : 为每一个 FUZZ 关键字指定一个有效负载
	-w wordlist               : 指定字典
	-V alltype                : 所有参数暴力破解
	-X method                 : 指定请求方法
	-b cookie                 : 指定 COOKIE
	-d postdata               : 指定 POSt 参数
	-H header                 : 指定请求头
	--basic/ntlm/digest auth  : 格式为 user:pass 
	
	--hc/hl/hw/hh N[,N]+      : 隐藏具有指定数据的 code/lines/words/chars HTTP 响应
	--sc/sl/sw/sh N[,N]+      : 显示具有指定数据的 code/lines/words/chars HTTP 响应
	--ss/hs regex             : 显示/隐藏 带有指定正则表达式的响应
```

### 2. 有效载荷

> WFUZZ 将对关键字 FUZZ 的任何引用替换为给定有效负载值

```shell
# 列出有效负载值
wfuzz -e payloads

# 列出有关有效负载的详细信息  可以使用 --slice 进行过滤
wfuzz -z help [--slice "NAME"]

# 指定多个有效负载  FUZnZ : n 是指有效负载编号
wfuzz -w wordlist/general/common.txt -w wordlist/general/common.txt -w wordlist/general/extensions_common.txt --hc 404 http://testphp.vulnweb.com/FUZZ/FUZ2ZFUZ3Z
```

## 2. 基本用法

### 1. 模糊路径和文件

```shell
# 查找公共目录
wfuzz -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ

# 查找文件
wfuzz -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ.php

# 数据模糊测试
wfuzz -z range,0-10 --hl 97 http://testphp.vulnweb.com/listproducts.php?cat=FUZZ

# 模糊 POST 请求
wfuzz -z file,wordlist/others/common_pass.txt -d "uname=FUZZ&pass=FUZZ"  --hc 302 http://testphp.vulnweb.com/userinfo.php

# 对各种 COOKIE 重复
wfuzz -z file,wordlist/general/common.txt -b cookie=value1 -b cookie2=value2 http://testphp.vulnweb.com/FUZZ

# 模糊 COOKIE
wfuzz -z file,wordlist/general/common.txt -b cookie=FUZZ http://testphp.vulnweb.com/

# 重复 HTTP头
wfuzz -z file,wordlist/general/common.txt -H "myheader: headervalue" -H "myheader2: headervalue2" http://testphp.vulnweb.com/FUZZ

# 使用不同请求方法
wfuzz -z list,GET-HEAD-POST-TRACE-OPTIONS -X FUZZ http://testphp.vulnweb.com/

# 使用代理
wfuzz -z file,wordlist/general/common.txt -p localhost:2222:SOCKS5 -p localhost:9090 http://testphp.vulnweb.com/FUZZ

# 身份验证
wfuzz -z list,nonvalid-httpwatch --basic FUZZ:FUZZ https://www.httpwatch.com/httpgallery/authentication/authenticatedimage/default.aspx

# 将结果写入 JSON 文件
wfuzz -f /tmp/outfile,json -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ

# 以 JSON 格式输出
wfuzz -o json -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ
```

## 3. 高级用法

### 1. 迭代器

```shell
# 压缩
wfuzz -z list,a-b-c -z list,1-2-3 -m zip http://google.com/FUZZ/FUZ2Z

00001:  C=404      9 L        32 W          276 Ch        "a - 1"
00002:  C=404      9 L        32 W          276 Ch        "c - 3"
00003:  C=404      9 L        32 W          276 Ch        "b - 2"

# 链
wfuzz -z list,a-b-c -z list,1-2-3 -m chain http://google.com/FUZZ

00001:  C=404      9 L        32 W          280 Ch        "b"
00002:  C=404      9 L        32 W          280 Ch        "a"
00003:  C=404      9 L        32 W          280 Ch        "c"
00004:  C=404      9 L        32 W          280 Ch        "1"
00006:  C=404      9 L        32 W          280 Ch        "3"
00005:  C=404      9 L        32 W          280 Ch        "2"

# product
wfuzz -z list,a-b-c -z list,1-2-3 http://mysite.com/FUZZ/FUZ2Z

00001:  C=404      9 L        32 W          276 Ch        "a - 2"
00002:  C=404      9 L        32 W          276 Ch        "a - 1"
00005:  C=404      9 L        32 W          276 Ch        "b - 2"
00004:  C=404      9 L        32 W          276 Ch        "a - 3"
00008:  C=404      9 L        32 W          276 Ch        "c - 2"
00003:  C=404      9 L        32 W          276 Ch        "b - 1"
00007:  C=404      9 L        32 W          276 Ch        "c - 1"
00006:  C=404      9 L        32 W          276 Ch        "b - 3"
00009:  C=404      9 L        32 W          276 Ch        "c - 3"
```

### 2. 编码器

```shell
# 指定一个编码器
 wfuzz -z file,wordlist/general/common.txt,md5 http://testphp.vulnweb.com/FUZZ
==================================================================
ID      Response   Lines      Word         Chars          Request
==================================================================

00002:  C=404      7 L        12 W          168 Ch        "b4b147bc522828731f1a016bfa72c073"
00003:  C=404      7 L        12 W          168 Ch        "96a3be3cf272e017046d1b2674a52bd3"
00004:  C=404      7 L        12 W          168 Ch        "a2ef406e2c2351e0b9e80029c909242d"

# 指定多个编码器 使用 - @ 连接
wfuzz -z list,1-2-3,md5-sha1@none http://webscantest.com/FUZZ
==================================================================
ID      Response   Lines      Word         Chars          Request
==================================================================

00000:  C=200     38 L       121 W         1486 Ch        "da4b9237bacccdf19c0760cab7aec4a8359010b0"
00001:  C=200     38 L       121 W         1486 Ch        "c4ca4238a0b923820dcc509a6f75849b"
00002:  C=200     38 L       121 W         1486 Ch        "3"
```
