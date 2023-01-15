# Searchsploit

## 介绍

SearchSploit是一个用于搜索Exploit-DB漏洞数据的命令行工具，它可以允许你离线Exploit数据库，这样你就可以在本地执行离线搜索，这对于有时候不能联网的渗透工作非常的有用。

## 安装教程

```shell
$sudo apt update 
$sudo apt -y install exploitdb
```

## 参数介绍

```shell
==========
 Examples 
==========
  searchsploit afd windows local
  searchsploit -t oracle windows
  searchsploit -p 39446
  searchsploit linux kernel 3.2 --exclude="(PoC)|/dos/"
  searchsploit -s Apache Struts 2.0.0
  searchsploit linux reverse password
  searchsploit -j 55555 | json_pp

  For more examples, see the manual: https://www.exploit-db.com/searchsploit

=========
 Options 
=========
## Search Terms
   -c, --case     [Term]      区分大小写(默认不区分大小写)
   -e, --exact    [Term]      对exploit标题进行EXACT匹配 (默认为 AND) [Implies "-t"].
                                e.g. "WordPress 4.1" would not be detect "WordPress Core 4.1")
   -s, --strict               执行严格搜索，因此输入值必须存在，禁用对版本范围的模糊搜索
                                e.g. "1.1" would not be detected in "1.0 < 1.3")
   -t, --title    [Term]      仅仅搜索漏洞标题（默认是标题和文件的路径）
       --exclude="term"       从结果中删除值。通过使用“|”分隔多个值
                                e.g. --exclude="term1|term2|term3"

## Output
   -j, --json     [Term]      以JSON格式显示结果
   -o, --overflow [Term]      Exploit标题被允许溢出其列
   -p, --path     [EDB-ID]    显示漏洞利用的完整路径（如果可能，还将路径复制到剪贴板），后面跟漏洞ID号
   -v, --verbose              在输出中显示更多信息
   -w, --www      [Term]      显示Exploit-DB.com的URL而不是本地路径（在线搜索）
       --id                   显示EDB-ID
       --colour               搜索结果不高亮显示关键词

## Non-Searching
   -m, --mirror   [EDB-ID]    把一个exp拷贝到当前工作目录,参数后加目标id
   -x, --examine  [EDB-ID]    使用$ PAGER检查（副本）Exp

## Non-Searching
   -h, --help                 显示帮助
   -u, --update               检查并安装任何exploitdb软件包更新（deb或git）

## Automation
       --nmap     [file.xml]  使用服务版本检查Nmap XML输出中的所有结果（例如：nmap -sV -oX file.xml）
                                e.g.: nmap [host] -sV -oX file.xml
```
