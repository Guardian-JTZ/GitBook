# Kerbrute

> Kerberos 是一个身份验证服务，打开此端口我们可以使用 kerbrute 进行暴力破解用户、密码、密码喷洒
>
> `最新版本的 Kerbrute 可能不包含 UserEnum 标志`
>
> * GitHUb --> [Kerbrute](https://github.com/ropnop/kerbrute/releases)

## Kerbrute 使用场景

* 使用场景： 从域外对域用户进行用户枚举和口令破解
* 由于没有域用户的口令，所以无法通过LDAP协议枚举出所有域用户，而且使用LDAP协议进行暴力破解时会产生日志(4625 - An account failed to log on)
* 优点：
  * 使用Kerberos pre-auth bruteforcing的速度更快
  * 不会产生日志(4625 - An account failed to log on)

## 功能使用

### 1. 用户枚举

```shell
kerbrute_windows_amd64.exe userenum --dc 192.168.1.1 -d test.com user.txt
```

### 2. 口令验证

`在确定了用户存在以后，可以使用这个功能来验证口令是否正确，命令如下：`

```shell
kerbrute_windows_amd64.exe passwordspray -d test.com user.txt DomainUser123!
```
