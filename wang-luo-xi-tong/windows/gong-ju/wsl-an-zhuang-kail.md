---
description: Windows 11 使用 WSL 安装 kail
---

# WSL 安装 kail

## 前期

1. 在微软官方应用商店下载 kail 的安装包
2. 开启本地的一些环境 ： 控制面板 --> 程序 --> 开启或关闭 Windows 功能
   1. Windows  虚拟机监控程序平台
   2. 适用于 Linux  的 WIndows 子系统
   3. 虚拟机平台
3. 将自己本机的 WSL  版本升级到 2 版本

```bash
# 查看wsl版本
$ wsl -l -v
# 如果不是二版本需要从官网下载升级程序
# 下载完后升级就可以

# 管理员身份打开 Powershell
ps > wsl --set-default-version 2
```

修改后，基本就没问题了，然后就可以安装了

## 修改默认安装位置

> 一般来说都会默认按在 C 盘 ，所以我们可以使用下面的方法进行修改默认安装位置，同时我们应该也可以使用这个方法进行一个分支保存

<pre class="language-bash"><code class="lang-bash"><strong># 1. 查看所有安装的版本
</strong><strong>PS C:\Users\14894> wsl -l --all -v
</strong>  NAME          STATE           VERSION
* kali-linux    Running         2
# 2. 导出分发版为 tar文件到 D盘
<strong>PS C:\Users\14894> wsl --export kali-linux D:\wsl-kali-linux.tar
</strong><strong># 3. 注销当前分发版
</strong>PS C:\Users\14894> wsl --unregister kali-linux
# 4. 重新导入并安装WSL在D盘
PS C:\Users\14894> wsl --import kali-linux D:\kali-linux  D:\wsl-kali-linux.tar --version 2
# 5. 设置默认登陆用户为安装时用户名
PS C:\Users\14894> kali-linux config --default-user USERNAME
# 6. 删除 wsl-kali-linux.tar
PS C:\Users\14894> del D:\wsl-kali-linux.tar
</code></pre>

## 更新源和升级软件

> 注意 kali-archive-keyring\_2022.1\_all.deb 不是固定的，访问 https://http.kali.org/kali/pool/main/k/kali-archive-keyring/ 查看当前版本。

{% code title="更新源" %}
```bash
wget --no-check-certificate -O  kali-archive-keyring_2022.1_all.deb https://http.kali.org/kali/pool/main/k/kali-archive-keyring/kali-archive-keyring_2022.1_all.deb
sudo dpkg -i kali-archive-keyring_2022.1_all.deb
sudo apt-get update
sudo apt-get upgrade
```
{% endcode %}

> 安装方式：
>
> * Metapackages ：元包有助于一次快速轻松地安装许多工具。这可以很容易地从最小的安装过渡到功能齐全的桌面环境。
> * Kali Network Repositories 和 Kali Branches ：如果用户想要一个更静态的安装，了解Kali网络存储库和Kali分支是很有用的。&#x20;

{% code title="安装软件" %}
```bash
sudo apt install -y kali-linux-default
# 这需要很长时间
```
{% endcode %}

## 安装桌面程序

```bash
$ sudo apt install -y kali-win-kex
```

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

安装完成后输入`kex`，之后要求设置密码（密码长度至少为6）。设置完成即开启gui。

{% code title="kex 命令" lineNumbers="true" %}
```bash
kex <mode> <command> <parameters>
<mode>:
--esm	# 使用Windows本机RDP在专用窗口中启动KeX桌面 即远程桌面连接
--sl	# 将KeX整合到Windows桌面中 
--win	# 在专用窗口中启动KeX桌面
<command>
--status
--start
--stop
--passwd	#设置kex server密码

```
{% endcode %}

## 添加代理

> 这个安装的 IP 和本机 IP 不在同一个网段，所以添加代理很重要，这里我使用  proxychains

<pre class="language-bash"><code class="lang-bash"><strong># 安装
</strong><strong>sudo apt install proxychains
</strong><strong># 修改配置文件
</strong><strong>vim /etc/proxychains.conf
</strong><strong>    socks4 IP 9095 # 添加
</strong>proxychains curl -i www.google.com
</code></pre>

> IP  的值为： 172.24.32.1
>
>

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
