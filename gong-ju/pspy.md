---
description: 非特权 Linux 进程监听
---

# 📱 Pspy

> pspy 是一个命令行工具，旨在无需 root 权限即可窥探进程。它允许您在执行时查看其他用户运行的命令、cron 作业等
>
> 下载链接 --> [pspy](https://github.com/DominicBreuker/pspy)

帮助选项

```bash
-p：允许将命令打印到标准输出（默认启用）
-f：启用将文件系统事件打印到标准输出（默认情况下禁用）
-r：使用 Inotify 监视的目录列表。pspy 将递归地监视所有子目录（默认情况下，监视 /usr、/tmp、/etc、/home、/var 和 /opt）。
-d：使用 Inotify 监视的目录列表。pspy 将只监视这些目录，而不是子目录（默认为空）。
-i：procfs 扫描之间的间隔（以毫秒为单位）。pspy 定期扫描新进程而不考虑 Inotify 事件，以防未收到某些事件。
-c：以不同颜色打印命令。文件系统事件不再有颜色，命令根据进程 UID 具有不同的颜色。
--debug：打印以其他方式隐藏的详细错误消息
```
