# WPScan

## 安装

```shell
gem install wpscan
```

## 参数介绍

> 我们可以从[WPVulnDB](https://wpvulndb.com/)获取 API

```shell
Usage: wpscan [options]
        --url URL                                 指定扫描的 URL 地址
    -h, --help                                    展示帮助信息
        --hh                                      展示完整帮助信息并退出
        --version                                 显示版本信息
    -v, --verbose                                 详细模式
        --[no-]banner                             是否显示横幅
                                                  Default: true
    -o, --output FILE                             保存到文件
    -f, --format FORMAT                           以提供的格式输出结果
                                                  支持选择: cli-no-colour, cli-no-color, json, cli
        --detection-mode MODE                     Default: mixed
                                                  支持选择: mixed, passive, aggressive
        --user-agent, --ua VALUE
        --random-user-agent, --rua                使用随机 user-agent
        --http-auth login:password
    -t, --max-threads VALUE                       指定使用线程
                                                  Default: 5
        --throttle MilliSeconds                   WEB 请求之间的毫秒数默认为1
        --request-timeout SECONDS                 请求超时时间
                                                  Default: 60
        --connect-timeout SECONDS                 连接超时时间
                                                  Default: 30
        --disable-tls-checks                      禁用 SSL/TLS 证书验证，并降级到 TLS1.0+（后者需要 cURL 7.66）
        --proxy protocol://IP:port                指定代理
        --proxy-auth login:password
        --cookie-string COOKIE                    指定 Cookie, 格式: cookie1=value1[; cookie2=value2]
        --cookie-jar FILE-PATH                    读写cookies的文件
                                                  Default: /tmp/wpscan/cookie_jar.txt
        --force                                   不检查目标是否正在运行 WordPress 或返回 403
        --[no-]update                             更新数据库
        --api-token TOKEN                         指定 API
        --wp-content-dir DIR                      指定包含的目录
        --wp-plugins-dir DIR                      指定插件目录， such as "wp-content/plugins"
    -e, --enumerate [OPTS]                        枚举过程
                                                  Available Choices:
                                                   vp   只枚举有漏洞的插件 
                                                   ap   枚举所有插件
                                                   p    枚举插件
                                                   vt   只枚举存在漏洞的主题 
                                                   at   枚举所有主题，时间较长 可以指定多个扫描选项，例："-e tt,p" 如果没有指定选项，默认选项为："vt,tt,u,vp"
                                                   t    枚举主题信息 
                                                   tt   列举缩略图相关的文件
                                                   cb   配置备份
                                                   dbe  数据库导出
                                                   u    枚举用户名，默认从1-10
                                                   m    Media IDs range. e.g m1-15
                                                        Note: Permalink setting must be set to "Plain" for those to be detected
                                                        Range separator to use: '-'
                                                        Value if no argument supplied: 1-100
                                                  Separator to use between the values: ','
                                                  Default: All Plugins, Config Backups
                                                  Value if no argument supplied: vp,vt,tt,cb,dbe,u,m
                                                  Incompatible choices (only one of each group/s can be used):
                                                   - vp, ap, p
                                                   - vt, at, t
        --exclude-content-based REGEXP_OR_STRING  当使用枚举选项时，可以使用该参数做一些过滤，基于正则或者字符串，可以不写正则分隔符，但要用单引号或双引号包裹 
                                                  标题和正文都被检查。 不需要正则表达式定界符。
        --plugins-detection MODE                  使用提供的模式枚举插件。
                                                  Default: passive
                                                  Available choices: mixed, passive, aggressive
        --plugins-version-detection MODE          使用提供的模式检查插件的版本。
                                                  Default: mixed
                                                  Available choices: mixed, passive, aggressive
        --exclude-usernames REGEXP_OR_STRING      排除与正则表达式/字符串匹配的用户名（不区分大小写）。 不需要正则表达式定界符。
    -P, --passwords FILE-PATH                     密码攻击期间使用的密码列表。
                                                  If no --username/s option supplied, user enumeration will be run.
    -U, --usernames LIST                          密码攻击期间使用的用户名列表。
                                                  Examples: 'a1', 'a1,a2,a3', '/tmp/a.txt'
        --multicall-max-passwords MAX_PWD         通过 XMLRPC 多路调用请求发送的最大密码数
                                                  Default: 500
        --password-attack ATTACK                  强制使用提供的攻击而不是自动确定攻击。
                                                  Available choices: wp-login, xmlrpc, xmlrpc-multicall
        --login-uri URI                           登录页面的 URI，如果不同于 /wp-login.php
        --stealthy                                Alias for --random-user-agent --detection-mode passive --plugins-version-detection passive
```

## 使用

1.  WPSCAN 枚举

    ```shell
    wpscan --url http://blog.inlanefreight.com --enumerate --api-token Kffr4fdJzy9qVcTk<SNIP>
    ```
2.  WPscan-XMLRPC

    ```shell
    wpscan --password-attack xmlrpc -t 20 -U admin, david -P passwords.txt --url http://blog.inlanefreight.com
    ```
