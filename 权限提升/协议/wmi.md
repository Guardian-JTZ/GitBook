# WMI

## 介绍

> WMI 是用来管理 Windows 系统的数据库工具， WMI 本身的组织架构就是一个数据库架构 --> [详细介绍](https://blog.csdn.net/Ping\_Pig/article/details/119446154?ops\_request\_misc=%257B%2522request%255Fid%2522%253A%2522166830609116800182162818%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D\&request\_id=166830609116800182162818\&biz\_id=0\&utm\_medium=distribute.pc\_search\_result.none-task-blog-2\~all\~top\_positive\~default-1-119446154-null-null.142^v63^pc\_rank\_34\_queryrelevant25,201^v3^control\_1,213^v2^t3\_control1\&utm\_term=WMI\&spm=1018.2226.3001.4187)
>
> * 使用 DCOM TCP ： 135 端口
> * WinRM 协议： 5985 端口

```shell
# WMI 通信的初始化总是在TCPport上135进行，在连接成功建立后，通信移动到一个随机端口。例如，Impacket 工具包中的程序wmiexec.py可以用于此目的
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```
