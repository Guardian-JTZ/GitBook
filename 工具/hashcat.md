---
description: 破解密码
---

# Hashcat

## Hashid

> 检测各种哈希值
>
> * 哈希类型与其对应的编号---> [网页跳转](https://hashcat.net/wiki/doku.php?id=example\_hashes)

```shell
# hashes.txt 是一个存储哈希值的文本文件
hashid hashes.txt 
```

## Hashcat

### 生成基于规则的词表

规则词表的语法列表 -->[ 跳转链接](https://hashcat.net/wiki/doku.php?id=rule\_based\_attack)

```
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```



<pre class="language-shell"><code class="lang-shell"><strong># Hashcat 语法
</strong>hashcat -a &#x3C;type> -m &#x3C;hash type> &#x3C;hash file> &#x3C;wordlist>

# 字典攻击
hashcat -a 0 -m 1400 sha256_hash_example ./rockyou.txt

# 组合攻击
hashcat -a 1 -m 0 combination_md5 wordlist1 wordlist2

# 掩码攻击 注解①
hashcat -a 3 -m 0 md5_mask_example_hash -1 01 'ILFREIGHT?l?l?l?l?l20?1?d'

# 使用单词列表的混合攻击，这条命令的作用是是在每一个单词的末尾加一个数字和特殊字符
hashcat -a 6 -m 0 ./rockyou.txt '?d?s'

# 使用带有掩码的单词列表的混合攻击
hashcat -a 7 -m 0 hybrid_hash_prefix -1 01 '20?1?d' ./rockyou.txt

# 破解哈希
hashcat -m 100 SHA1_hashes ./rockyou.txt</code></pre>

| 占位符 | 意义                                                        |
| --- | --------------------------------------------------------- |
| ?l  | 小写 ASCII 字母 (az)                                          |
| ?u  | 大写 ASCII 字母 (AZ)                                          |
| ?d  | 数字 (0-9)                                                  |
| ?h  | 0123456789abcdef                                          |
| ?H  | 0123456789ABCDEF                                          |
| ?s  | 特殊字符 («空格»!"#$%\&amp;'()\*+,-./:;\&lt;=\&gt; ?@ \[]^\_\`{ |
| ?a  | ?l?u?d?s                                                  |
| ?b  | 0x00 - 0xff                                               |
