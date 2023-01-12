---
description: JDk8/11/16
---

# JDK

> * 下载地址 --> [百度网盘](https://pan.baidu.com/s/1zScZjiE7KyElgVU3Bh2klQ?pwd=ypva)

## 如何同时存在多个版本

> 原理 --> [链接](https://blog.csdn.net/qq\_45182665/article/details/115571768?ops\_request\_misc=%257B%2522request%255Fid%2522%253A%2522167352271316800222825596%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D\&request\_id=167352271316800222825596\&biz\_id=0\&utm\_medium=distribute.pc\_search\_result.none-task-blog-2\~all\~sobaiduend\~default-1-115571768-null-null.142^v70^control,201^v4^add\_ask\&utm\_term=python%20--version%E6%B2%A1%E5%8F%8D%E5%BA%94\&spm=1018.2226.3001.4187)

> 不需要修改环境变量，只需要输入如下命令就可以执行：
>
> ```bash
> PS C:\Users\14894> java8 -version
> java version "1.8.0_341"
> Java(TM) SE Runtime Environment (build 1.8.0_341-b10)
> Java HotSpot(TM) 64-Bit Server VM (build 25.341-b10, mixed mode)
> PS C:\Users\14894> java11 -version
> java version "11.0.16.1" 2022-08-18 LTS
> Java(TM) SE Runtime Environment 18.9 (build 11.0.16.1+1-LTS-1)
> Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.16.1+1-LTS-1, mixed mode)
> PS C:\Users\14894> java16 -version
> java version "16.0.2" 2021-07-20
> Java(TM) SE Runtime Environment (build 16.0.2+7-67)
> Java HotSpot(TM) 64-Bit Server VM (build 16.0.2+7-67, mixed mode, sharing)
> ```

你可以尝试输入以下命令：

```bash
# 你就会发现这其中多个一个 javapath 环境下的 java.exe 程序，这一项在你所安装的其他 java.exe 之前
C:\Users\14894>  where java
```

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption><p>这是从网上找的图片，因为我的已经删掉了</p></figcaption></figure>

删掉后你就可以上面的命令了

