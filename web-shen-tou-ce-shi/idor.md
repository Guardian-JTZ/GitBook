---
description: IDOR 是一种访问控制漏洞
---

# IDOR

> 当 WEB 服务器收到用户提供的输入来获取 `文件、数据` 时，没有进行过滤，直接在服务器之间请求资源时可能会发生此漏洞。

## 位置

* AJAX 请求
* JS 文件中
* 未引用的参数

## 攻击

### 1. ID

> 在请求资源时，URL 中可能存在 ID 之类的参数，服务器根据 ID 来获取数据

```shell
http://online-service.thm/profile?user_id=1305  # 比如 user_id 参数
```

* ID 可能在 cookie 或者 URl 中并且会 `编码 或者 hash` 这时候我们需要解码

### 2. 不可预测的 ID

> 我们可以创建两个账号并在他们之间交互 ID 号，如果可以查看其他用户内容，说明存在 IDOR漏洞
