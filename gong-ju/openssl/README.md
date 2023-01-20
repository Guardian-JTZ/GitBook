# OPENSSL

## 介绍

## 生成密钥对

```shell
openssl genrsa [-out filename] [-passout arg] [numbits]
# -out filename ：将生成的私钥保存至filename文件，若未指定输出文件，则为标准输出
# -numbits ：指定要生成的私钥的长度，默认为1024。该项必须为命令行的最后一项参数

# 生成私钥
$ openssl genrsa -aes128 -out alice_private.pem 1024
# 提取公钥
$ openssl rsa -in alice_private.pem -pubout > alice_public.pem
```

## 参考

https://linux.cn/article-13368-1.html

http://www.hangdaowangluo.com/archives/1739

https://www.finclip.com/news/f/10795.html
