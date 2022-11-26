---
description: 创建自定义词表的工具
---

# CEWL

工作原理： 抓取一个网站上的单词并创建一个单词列表

```bash
# 语法
# -m 输出单词的长度
cewl -d <depth to spider> -m <minimum word length> -w <output wordlist> <url of website>

# -e 从网站提取电子邮件
cewl -d 5 -m 8 -e http://inlanefreight.com/blog -w wordlist.txt
```
