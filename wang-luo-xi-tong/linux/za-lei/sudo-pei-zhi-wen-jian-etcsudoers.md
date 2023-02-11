# sudo配置文件/etc/sudoers

## SUDO 命令执行流程

当用户执行sudo时，系统会主动寻找/etc/sudoers文件，判断该用户是否有执行sudo的权限 --> 确认用户具有可执行sudo的权限后，让用户输入用户自己的密码确认 --> 若密码输入成功，则开始执行sudo后续的命令

## 配置

> 让普通用户support只能在某几台服务器上，执行root能执行的某些命令 首先需要配置一些Alias，这样在下面配置权限时，会方便一些，不用写大段大段的配置。Alias主要分成4种:
>
> ```shell
> 1. 配置Host_Alias：就是主机的列表
>    Host_Alias      HOST_FLAG = hostname1, hostname2, hostname3
> 2. 配置Cmnd_Alias：就是允许执行的命令的列表
>    Cmnd_Alias      COMMAND_FLAG = command1, command2, command3
> 3. 配置User_Alias：就是具有sudo权限的用户的列表
>    User_Alias USER_FLAG = user1, user2, user3
> 4. 配置Runas_Alias：就是用户以什么身份执行（例如root，或者oracle）的列表
>    Runas_Alias RUNAS_FLAG = operator1, operator2, operator3
> 5. 配置权限：
>    格式：
>        USER_FLAG HOST_FLAG=(RUNAS_FLAG) COMMAND_FLAG
>    不需要密码：
>    	   USER_FLAG HOST_FLAG=(RUNAS_FLAG) NOPASSWD: COMMAND_FLAG
> ```

## 使用

我们常在权限提升中可以使用这个方法进行权限提升：

```shell
echo "USER ALL=(ALL) NOPASSWD:ALL" >>/etc/sudoers
```
