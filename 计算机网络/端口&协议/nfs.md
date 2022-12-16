# NFS

## 介绍

> NFS 具有和 SMB 相同的用途，目的是通过网络访问文件系统，但是两者使用不同的协议
>
> 配置文件： /etc/exports
>
> [官方使用手册](https://manpages.ubuntu.com/manpages/trusty/man5/exports.5.html)

```shell
# 设置
rw  # 读写权限, 危险
ro  # 只读权限
sync  # 同步数据传输。（有点慢）
async  # 异步数据传输。（快一点）
secure  # 不会使用 1024 以上的端口
insecure   # 将使用 1024 以上的端口  危险
no_subtree_check  # 此选项禁用子目录树的检查
nohide  # 	如果另一个文件系统安装在导出目录下，则该目录将通过其自己的导出条目导出 危险
root_squash  # 将root UID/GID 0 的文件的所有权限分配给anonymous 的UID/GID  危险
```

```shell
# 导出文件，下面命令将 /mnt/nfs 共享到子网 10.129.14.0/24
echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
systemctl restart nfs-kernel-server 
exportfs

# 显示可用 NFS 共享
showmount -e 10.129.14.128

# 挂载 NFS 共享
mkdir target-NFS
mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
cd target-NFS
tree .

# 卸载
cd ..
umount ./target-NFS
```
