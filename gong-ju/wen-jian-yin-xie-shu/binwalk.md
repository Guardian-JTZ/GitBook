# binwalk

## 介绍

`Binwalk`是一款快速、易用，用于分析，逆向工程和提取固件映像的工具。简单易用，完全自动化脚本，并通过自定义签名，提取规则和插件模块，还重要一点的是可以轻松地扩展。 在CTF的MISC类题型和IOT安全的固件解包分析中广泛应用，可以大大提高效率。该工具对linux支持较好，对于windows功能支持较差，有条件的童鞋可以在linux上使用练习该神器。

## 参数介绍

```shell
$ binwalk -h  	# 帮助信息
$ binwalk firmware.bin		# 扫描文件
$ binwalk -e firmware.bin	# 文件提取
$ binwalk --signature firmware.bin	# 签名分析
$ binwalk -E firmware.bin	# 熵分析
$ binwalk -R "\x00\x01\x02\x03\x04" firmware.bin	# 搜索字符串
```

## 参考

* [BinWalk安装和命令参数详解](https://cloud.tencent.com/developer/article/1515285)
