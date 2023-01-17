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
# -l ：输出后面接的装置所有的分区内容。若仅有 fdisk -l 时， 则系统将会把整个系统内能够搜寻到的装置的分区均列出来
```

## mkfs

> #### 磁盘格式化

```bash
mkfs [-t 文件系统格式] 装置文件名
# -t ：可以接文件系统格式，例如 ext3, ext2, vfat 等(系统有支持才会生效)

# 查看支持那些文件系统
[root@www ~]# mkfs[tab][tab]
```

## fsck

> 用来检查和维护不一致的文件系统。
>
> 若系统掉电或磁盘发生问题，可利用fsck命令对文件系统进行检查

```bash
fsck [-t 文件系统] [options] 装置名称

# 显示支持的文件系统
[root@www ~]# fsck[tab][tab]
```

## 磁盘挂载与卸除

> Linux 的磁盘挂载使用 `mount` 命令，卸载使用 `umount` 命令。

```bash
# 磁盘挂载语法：
mount [-t 文件系统] [-L Label名] [-o 额外选项] [-n]  装置文件名  挂载点

# 磁盘卸载命令语法：
umount [-fn] 装置文件名或挂载点
    # -f ：强制卸除！可用在类似网络文件系统 (NFS) 无法读取到的情况下；
    # -n ：不升级 /etc/mtab 情况下卸除。
```

