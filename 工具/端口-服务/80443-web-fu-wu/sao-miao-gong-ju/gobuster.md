# gobuster

> * 网站中的 URI（目录和文件）。
> * DNS 子域（支持通配符）。
> * 目标 Web 服务器上的虚拟主机名。
> * 打开 Amazon S3 存储桶

## 1. 入门

```shell
# 获取帮助
gobuster [dir|dns|fuzz|s3|vhost] -h

可用命令:
  dir         使用目录/文件枚举模式
  dns         使用 DNS 子域枚举方式
  fuzz        使用模糊模式
  s3          使用 aws 桶枚举模式
  vhost       用 VHOST 枚举模式

Flags:
  --delay duration    线程之间等待时间
  --no-error          不显示错误
  -z, --no-progress       不显示进度
  -o, --output string     将结果输出到文件中
  -p, --pattern string    包含替换模式的文件
  -q, --quiet             不打印横幅和其他噪音
  -t, --threads int       并发线程数
  -v, --verbose           详细输出
  -w, --wordlist string   字典路径
```

## 2. 基本使用

```shell
# DNS 模式
gobuster dns -d google.com -t 50 -w common-names.txt
# 显示 IP
gobuster dns -d google.com -w ~/wordlists/subdomains.txt -i


# dir 模式
gobuster dir -u https://mysite.com/path/to/folder -c 'session=123456' -t 50 -w common-files.txt -x .php,.html

# VHOST  模式
gobuster vhost -u https://mysite.com -w common-vhosts.txt

# FUZZ 模式
gobuster fuzz -u https://example.com?FUZZ=test -w parameter-names.txt

# s3 模式
gobuster s3 -w bucket-names.txt
```
