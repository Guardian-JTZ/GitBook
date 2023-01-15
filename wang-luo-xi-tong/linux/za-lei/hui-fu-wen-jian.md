# 恢复文件

当我的文件被删除时，文件系统的结构会删除有关该文件的元数据。这包括时间戳、文件名和指向原始文件在磁盘上的位置的指针。删除操作不会转到磁盘上的那个点并执行任何清理数据的操作，比如在其上写入所有空值。

## grep | strings

grep 用于从文件中找出给定模式的字符串，下面我们想要从虚拟磁盘中找到一个 32 位数字字母混合的数据

```shell
root@raspberrypi:/media/usbstick/lost+found# grep -aPo '[a-fA-F0-9]{32}' /dev/sdb
3d3e483143ff12ec505d026fa13e020b
```

如果我仅仅想要找到一个字符串，我们也可以使用 strings

```shell
root@raspberrypi:/media/usbstick/lost+found# strings /dev/sdb -n 32
3d3e483143ff12ec505d026fa13e020b
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?
```

## 拷贝盘

```shell
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $sshpass -p raspberry ssh pi@10.10.10.48 "sudo dd if=/dev/sdb | gzip -1 -" | dd of=usb.gz20480+0 records in
20480+0 records out
10485760 bytes (10 MB) copied, 0.0931846 s, 113 MB/s
记录了93+1 的读入
记录了93+1 的写出
48105字节（48 kB，47 KiB）已复制，4,10876 s，11,7 kB/s
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $file usb.gz 
usb.gz: gzip compressed data, last modified: Sun Jan 15 11:35:23 2023, max speed, from Unix, original size modulo 2^32 10485760
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $gunzip usb.gz 
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $file usb 
usb: Linux rev 1.0 ext4 filesystem data, UUID=635bcd7f-1d95-4229-bf13-3e722026db3c (extents) (huge files)
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $ls -hl usb 
-rw-r--r-- 1 jtz jtz 10M  1-р сар 15 19:35 usb
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $extundelete usb --restore-all
NOTICE: Extended attributes are not restored.
Loading filesystem metadata ... 2 groups loaded.
Loading journal descriptors ... 23 descriptors loaded.
Searching for recoverable inodes in directory / ... 
1 recoverable inodes found.
Looking through the directory structure for deleted files ... 
0 recoverable inodes still lost.
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $ls
32400-directory.txt  dig.txt    nmap-port-explore.txt  RECOVERED_FILES
80-directory.txt     mirai.txt  nmap-port.xml          usb
┌─[jtz@parrot]─[~/Desktop/Temp/Mirai]
└──╼ $tree ./RECOVERED_FILES
./RECOVERED_FILES
└── root.txt

0 directories, 1 file
```

## cat 查看

有的时候你可以使用 cat 查看信息，如果是英文会直接输出的
