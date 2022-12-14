# feroxbuster

官网链接 --> [Github](https://github.com/epi052/feroxbuster)

## 介绍&安装

**Feroxbuster**是一种旨在执行强制浏览的工具。强制浏览是一种攻击，其目的是识别和访问 Web 应用程序未引用但攻击者仍可获得的资源。Feroxbuster 工具使用暴力策略结合单词列表​​来搜索目标目录中未链接的内容。这些资源可能存储有关 Web 应用程序和操作系统的敏感信息，例如源代码、凭据、内部网络地址等。Feroxbuster 工具是开源的，可以免费使用。它在 GitHub 上可用，最近也添加到 Linux 操作系统的 apt 管理器中。这种攻击也称为可预测资源位置、文件枚举、目录枚举和资源枚举。

```bash
sudo apt update && sudo apt install -y feroxbuster
```

## 使用

参考文章 --> [链接](https://www.ddosi.org/feroxbuster/)

```bash
用法:
    feroxbuster [FLAGS] [OPTIONS] --url <URL>...

FLAGS:
    -f, --add-slash        追加/到每个请求
    -D, --dont-filter      不要自动过滤通配符响应
    -e, --extract-links    Extract links from response body (html, javascript, etc...); make new requests based on
                           findings (default: false)
    -h, --help             Prints help information
    -k, --insecure         禁用TLS证书验证
        --json             Emit JSON logs to --output and --debug-log instead of normal text
    -n, --no-recursion     不递归扫描
    -q, --quiet            只输出url;不输出状态码，响应大小，运行配置等…
    -r, --redirects        遵循重定向
        --stdin            从stdin读取url
    -V, --version          输出版本信息
    -v, --verbosity        增加详细程度(使用-vv或更大的效果。[注意]4 -v可能太多)

参数:
        --debug-log <FILE>                        Output file to write log entries (use w/ --json for JSON entries)
    -d, --depth <RECURSION_DEPTH>                 最大递归深度，深度为0表示无限递归(默认值:4)
    -x, --extensions <FILE_EXTENSION>...          …要搜索的文件扩展名(例如:-x php -x pdf js)
    -N, --filter-lines <LINES>...                 Filter out messages of a particular line count (ex: -N 20 -N 31,30)
    -X, --filter-regex <REGEX>...
            通过在响应体上匹配正则表达式过滤掉消息(例如:-X '^ignore me$')

        --filter-similar-to <UNWANTED_PAGE>...
            过滤掉与给定页面相似的页面(例如——Filter -similar-to http://site.xyz/soft404)

    -S, --filter-size <SIZE>...                   过滤掉特定大小的消息(例如:-S 5120 -S 4927,1970)
    -C, --filter-status <STATUS_CODE>...          过滤掉状态码(拒绝列表)(例如:-C 200 -C 401)
    -W, --filter-words <WORDS>...                 过滤掉特定单词计数的消息(例如:- w312 - w91,82)
    -H, --headers <HEADER>...                     指定HTTP报头(例如:-H报头:val 'stuff: things')
    -o, --output <FILE>                           要写入结果的输出文件(json项使用w/——json
    -p, --proxy <PROXY>
            用于请求的代理(例如:http(s)://host:port, socks5(h)://host:port)
    -Q, --query <QUERY>...                        指定URL查询参数(例如:-Q token=stuff -Q secret=key)

- r, replay-codes < REPLAY_CODE >…
    -R, --replay-codes <REPLAY_CODE>...
            发现时通过重播代理发送的状态码(默认值:——Status - Codes值)

    -P, --replay-proxy <REPLAY_PROXY>
            仅通过重播代理发送未过滤的请求，而不是所有请求

        --resume-from <STATE_FILE>
            要恢复部分完成扫描的状态文件(例如——resume-from ferox-1606586780.state)

    -L, --scan-limit <SCAN_LIMIT>                 限制总并发扫描数(默认:0，即没有限制)
    -s, --status-codes <STATUS_CODE>...
           要包含的状态码(允许列表)(默认值:200 204 301 302 307 308 401 403 405)

    -t, --threads <THREADS>                       并发线程数(默认为50)
        --time-limit <TIME_SPEC>                  所有扫描的总运行时间(例如:——time- Limit 10m)
    -T, --timeout <SECONDS>                       请求超时前的秒数(默认为7秒)
    -u, --url <URL>...                            目标URL(必需的，除非使用——stdin)
    -a, --user-agent <USER_AGENT>                 用户代理(默认:feroxbuster/VERSION)
    -w, --wordlist <FILE>                         密码字典的路径

NOTE:
    Options that take multiple values are very flexible.  Consider the following ways of specifying
    extensions:
        feroxbuster -u http://127.1 -x pdf -x js,html -x php txt json,docx

    The command above adds .pdf, .js, .html, .php, .txt, .json, and .docx to each url

    All of the methods above (multiple flags, space separated, comma separated, etc...) are valid
    and interchangeable.  The same goes for urls, headers, status codes, queries, and size filters.

EXAMPLES:
    Multiple headers:
        feroxbuster -u http://127.1 -H Accept:application/json "Authorization: Bearer {token}"

    IPv6, non-recursive scan with INFO-level logging enabled:
        feroxbuster -u http://[::1] --no-recursion -vv

    Read urls from STDIN; pipe only resulting urls out to another tool
        cat targets | feroxbuster --stdin --silent -s 200 301 302 --redirects -x js | fff -s 200 -o
js-files

    Proxy traffic through Burp
        feroxbuster -u http://127.1 --burp

    Proxy traffic through a SOCKS proxy
        feroxbuster -u http://127.1 --proxy socks5://127.0.0.1:9050

    Pass auth token via query parameter
        feroxbuster -u http://127.1 --query token=0123456789ABCDEF

    Find links in javascript/html and make additional requests based on results
        feroxbuster -u http://127.1 --extract-links

    Ludicrous speed... go!
        feroxbuster -u http://127.1 -threads 200

    Limit to a total of 60 active requests at any given time (threads * scan limit)
        feroxbuster -u http://127.1 --threads 30 --scan-limit 2

    Send all 200/302 responses to a proxy (only proxy requests/responses you care about)
        feroxbuster -u http://127.1 --replay-proxy http://localhost:8080 --replay-codes 200 302
--insecure

    Abort or reduce scan speed to individual directory scans when too many errors have occurred
        feroxbuster -u http://127.1 --auto-bail
        feroxbuster -u http://127.1 --auto-tune

    Examples and demonstrations of all features
        https://epi052.github.io/feroxbuster-docs/docs/examples/
```
