# Rsync

> 一种高效的本地和远程复制文件的工具，可以用于在给定机器上本地复制文件以及与远程主机之间复制文件。当目标主机已存在文件版本时，仅发送两个文件之间的差异处进行传输。并且可以配置为使用 SSH 进行安全文件传输，
>
> 使用端口 ： `873`

```shell
# 扫描 Rsync
sudo nmap -sV -p 873 127.0.0.1

# 探索可访问共享
nc -nv 127.0.0.1 873
# 枚举开放共享
rsync -av --list-only rsync://127.0.0.1/dev

# 通过 SSH 使用 Rsync 传输文件
rsync OPTION SourceDirectory_or_filePath user@serverIP_or_name:Target
# 传输特定文件
rsync ~/Dir1/source.pdf test@192.168.56.100:~/Desktop/test
# 传输目录内容 * 表示只转移文件，不转移子目录
rsync ~/SourceDirectory/* username@192.168.56.100:~/Destination
# 传输目录中全部内容
rsync -a ~/Desktop/Dir1/ test@192.168.56.100:~/Desktop/test
# 显示传输进度
rsync -aP ~/SourceDirectory/ username@192.168.56.100:~/Destination
```
