# 磁盘信息

## 磁盘

在 Linux 系统中所有的设备都会以文件的形式存储，设备一般保存在 /dev 目录下面以 sdan、sdbn、hda,hdb 命名，现在的设备一般都是以 sd 命名，以前的老硬盘是 ha 命名

## 分区

Linux只能创建4个主分区，如果需要创建更多的分区那么必须创建逻辑分区，其中逻辑分区需要占用一个主分区

## 文件系统

Linux中的文件系统也就是分区类型，在Windows中有NTEF,FAT32等，linux中常见的有Ext2、Ext3、Ext4、Linux swap、proc、sysfs、tmpfs等，可以通过mount命名查看当前已挂载的文件系统

## df

> 检查文件系统的磁盘空间占用情况。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

```bash
df [options] file
```

## dh

> 对文件和目录磁盘使用的空间的查看

```bash
du [options] file
```

## fdisk

> fdisk 是 Linux 的磁盘分区表操作工具

```bash
fdisk [options] <disk>         更改分区表
fdisk [options] -l [<disk>...] 列出分区表
```
