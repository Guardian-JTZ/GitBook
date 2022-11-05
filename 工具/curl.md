# CURL

| 标志               | 描述                            |
| ---------------- | ----------------------------- |
| -A               | 指定客户端用户代理头                    |
| -b               | 向服务器发送 COOKIE                 |
| -c               | 将服务器返回 COOKIE 写入文件            |
| -d               | 发送 POST 请求数据                  |
| --data-urlencode | 和 -d 效果相同，区别在于 会将数据进行 URL 编码  |
| -e               | 设置 HTTP 的标头`Referer`          |
| -F               | 向服务器上传二进制文件                   |
| -G               | 构造 URL 的查询字符串                 |
| -H               | 添加 HTTP 请求的标头                 |
| -i               | 打印出服务器回应的 HTTP 标头             |
| -I               | 只打印 HTTP 标头                   |
| -K               | 指定跳过 SSL 检测                   |
| -L               | 会让 HTTP 请求跟随服务器的重定            |
| --limit-rate     | 限制 HTTP 请求和回应的带宽              |
| -o               | 将服务器的回应保存成文件                  |
| -O               | 将服务器回应保存成文件，并将 URL 的最后部分当作文件名 |
| -s               | 不输出错误和进度信息                    |
| -S               | 指定只输出错误信息                     |
| -u               | 设置服务器认证的用户名和密码                |
| -v               | 输出通信的整个过程                     |
| -x               | 指定 HTTP 请求的代理                 |
| -X               | 指定 HTTP 请求的方法                 |

```shell
# 获取网页源码
curl www.sina.com

# 保存网页
curl -o 1.html www.sina.com

# 自动跳转
curl -L www.sina.com

# 显示 http response 的头信息，连同网页代码一起
curl -i www.sina.com

# 只显示http response的头信息
curl -I www.sina.com

# 显示显示一次http通信的整个过程，包括端口连接和http request头信息
curl -v www.sina.com
curl --trace output.txt www.sina.com

# 发送表单信息
curl example.com/form.cgi?data=xxx
curl -X POST [--data-urlencode] --data "data=xxx" example.com/form.cgi # --data-urlencode 自动编码

# 其他 HTTP 方法
curl -X POST www.example.com

# 文件上传
curl --form upload=@localfilename --form press=OK [URL]

# Referer字段
curl --referer http://www.example.com http://www.example.com

# User Agent字段
curl --user-agent "[User Agent]" [URL]

# Cookie  -c 保存服务器返回的 COOKIE 到文件  -b 使用这个文件中的 COOKIE 信息
curl --cookie "name=xxx" www.example.com
curl -c cookies http://example.com
curl -b cookies http://example.com

# 增加头信息
curl --header "Content-Type:application/json" http://example.com

# HTTP 认证
curl --user name:password example.com
```
