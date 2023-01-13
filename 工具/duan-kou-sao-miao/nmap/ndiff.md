# ndiff

## 介绍&安装

Ndiff 是一种有助于比较 Nmap 扫描的工具。它需要两个 Nmap XML 输出文件并打印它们之间的差异：主机启动和关闭、端口打开或关闭等等。它可以生成人类可读文本或机器可读 XML 格式的输出。

```shell
sudo apt install ndiff
```

## 参数介绍

```shell
$ndiff -h
  -h, --help     帮助信息
  -v, --verbose  版本信息
  --text         以文本格式显示输出 (default)
  --xml          以 XML 格式显示输出
```
