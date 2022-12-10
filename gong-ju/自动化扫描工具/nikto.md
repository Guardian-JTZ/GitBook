# nikto

> [Nikto 官方文档](https://github.com/sullo/nikto/wiki)

## 使用

```shell
# 基本扫描，默认是 80 端口
nikto -h IP [-p 80,90,99...]

# 针对多个主机使用文件名指出，主机格式必须为每行一个主机，主机末尾带端口号
# 格式： IP:[80,90,99....]

# 使用代理
nikto -h IP -useproxy http://localhost:8080/

# 互动功能：
    SPACE - 报告当前扫描状态
    v - 打开/关闭详细模式
    d - 打开/关闭调试模式
    e - 打开/关闭错误报告
    p - 打开/关闭进度报告
    r - 打开/关闭 3xx/重定向显示
    c - 打开/关闭 cookie 显示
    o - 打开/关闭 200/OK 显示
    a - 打开/关闭身份验证显示
    q - 退出（优雅地）
    N - 下一个主持人/帖子
    P - 暂停
```
