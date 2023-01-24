# Cisco Packet Tracer

## 入门

> Packet Tracer 可以创建四种不同类型的文件，这些文件类型用于不同的目的，这四种类型文件为： .pka、.pkr、.pksz、.pkz

### .pka 文件

这个文件是 Packet Trabcer 活动文件，这个文件类型包含两个网络： 初始网络和 answer 网络，启动活动时会打开初始网络， answer 网络在后台运行可用于在学习者完成活动时评分反馈

### .pkt 文件

这个类型文件是在 Packet Trancer 中构建并保存模拟网络时创建的， .pkt 文件还可以在其中嵌入图形背景图像

### .pksz 文件

这个文件类型是一个 Packet Trancer Tutored Activities （PTTA） 文件，这个文件捆绑一个 .pka 文件、media assets 、一个用于提示系统的脚本文件

### .pkz 文件

这是旧版本的 .pkt 文件，已经弃用

## CLI

### User EXEC Mode

```shell
Router>
 |    |
 |    |---> User EXEC Mode
 |--------> 主机名称
```

### Privileged EXEC Mode

```shell
Router>enable  # 进入特权模式
Router#
```

### 帮助

```shell
# 查看可用命令
Router>?
Router#?

# 查看命令有哪些
Router>e?
enable exit
```

### 全局配置模式

```shell
Router#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
```

### 设置密码

```shell
Router(config)#enable password ?
  7      Specifies a HIDDEN password will follow
  LINE   The UNENCRYPTED (cleartext) 'enable' password
  level  Set exec level password
Router(config)#enable password CCNA

Router>enable
Password: 
Router#
```

### 配置文件

* Running-config : 设备上当前活动的配置文件
* startup-config : 设备重启时加载的配置文件

```shell
Router#show running-config 
Router#show startup-config 
startup-config is not present  # 表明配置文件还没有保存

# 保存配置文件 -- 三个命令
Router#write
Building configuration...
[OK]
Router#write memory 
Building configuration...
[OK]
Router#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### 密码加密

> 我们可以将我们的密码进行加密存储在文件中

```shell
Router(config)#service password-encryption 
```

### 取消或删除命令

```shell
Router(config)#no service password-encryption #b取消密码加密命令
```
