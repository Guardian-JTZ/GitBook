# Linux 权限： SUID, SGID, sticky

> 特殊权限，构成除用户、组、其他之外的第四个访问级别，特殊权限是对表中权限集的附加权限

## user + s (SUID)

> 具有 SUID 的文件始终`以拥有该文件的用户身份运行`，无论用户传递的命令是什么，如果文件所有者没有权限，那么这里使用大写的 S

我们以 /usr/bin/passwd 举例,默认情况下，该文件具有 SUID 权限集

```shell
[tcarrigan@server ~]$ ls -l /usr/bin/passwd 
-rwsr-xr-x. 1 root root 33544 Dec 13  2019 /usr/bin/passwd
```

## group + s (SGID)

> 这个特殊权限具有几个功能
>
> * 在文件上设置，它允许文件`作为用户改文件的组执行`
> * 在目录上设置，则在目录中创建的任何文件都将其`组所有权设置为目录所有者的组所有权`
>
> 如果所属组没有执行权限，则使用大写 S

```shell
[tcarrigan@server article_submissions]$ ls -l 
total 0
drwxrws---. 2 tcarrigan tcarrigan  69 Apr  7 11:31 my_articles
```

## other + t (sticky )

> 这个权限不影响单个文件，但是在目录级别限制文件删除，只有文件所有者和 root 可以删除该目录中文件，一个场景的例子是 /tmp 目录

```shell
[tcarrigan@server article_submissions]$ ls -ld /tmp/
drwxrwxrwt. 15 root root 4096 Sep 22 15:28 /tmp/
```

## 设置命令

* SUID = 4
* SGID = 2
* sticky = 1

```shell
# 语法, X 是特殊权限数字
[tcarrigan@server ~]$ chmod X### file | directory

# 举例
[tcarrigan@server article_submissions]$ chmod 2770 community_content/
[tcarrigan@server article_submissions]$ ls -ld community_content/
drwxrws---. 2 tcarrigan tcarrigan 113 Apr  7 11:32 community_content/
```

## 参考文章

* [Linux permissions: SUID, SGID, and sticky bit](https://www.redhat.com/sysadmin/suid-sgid-sticky-bit)
