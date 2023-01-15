# XFF\&Referer

X-Forward-For (XFF)： 是用来识别通过 `HTTP 代理或负载均衡方式`连接到 WEB 服务器的客户端`最原始的 IP 地址`的 HTTP 请求头字段。

Referer 是 HTTP header 的一部分，当浏览器向 WEB 服务器发送请求时，会带上 Referer ，告诉服务器该网页是从哪个页面链接过来的，服务器因此可以获得一些信息用于处理。

***

XFF 是 HTTP 的扩展头部字段，作用是`使服务器获取访问用户的 IP 真实地址`，由于很多用户通过代理服务器访问，服务器只能获取代理服务器的 IP 地址，XFF 作用在于`可以记录用户真实 IP，以及代理服务器 IP`

```html
X-Forwarded-For: 本机IP, 代理1IP, 代理2IP, 代理2IP
```

Referer 是 HTTP 的扩展头部，作用是`记录当前请求页面的页面来源地址`，服务器使用 Referer 确认访问来源，如果 Referer 内容不符合要求，服务器可以拦截或者重定向请求。

```html
 即：XFF构造来源IP，Referer构造来源浏览器！！！！
```
