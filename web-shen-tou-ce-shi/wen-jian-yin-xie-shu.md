# 文件隐写术

## Exiftool

查看图像的元数据

## Hexdump

查看 十六进制文件，这里也可以使用 WinHex 文件

## BinWalk

> `Binwalk`是一款快速、易用，用于分析，逆向工程和提取固件映像的工具。简单易用，完全自动化脚本，并通过自定义签名，提取规则和插件模块，还重要一点的是可以轻松地扩展。
>
> 该工具可以发现文件中嵌入的其他文件

```shell
┌──(jtz㉿JTZ)-[~/Desktop/Temp/T]		# 查看是否存在嵌入的其他文件
└─$ binwalk meme.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, big-endian, offset of first image directory: 8
74407         0x122A7         RAR archive data, version 5.x
74478         0x122EE         PNG image, 147 x 37, 8-bit/color RGBA, non-interlaced
74629         0x12385         Zlib compressed data, default compression

┌──(jtz㉿JTZ)-[~/Desktop/Temp/T]		# 提取到 _meme.jpg.extracted 文件中
└─$ binwalk meme.jpg -e

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, big-endian, offset of first image directory: 8
74407         0x122A7         RAR archive data, version 5.x
74478         0x122EE         PNG image, 147 x 37, 8-bit/color RGBA, non-interlaced
74629         0x12385         Zlib compressed data, default compression


┌──(jtz㉿JTZ)-[~/Desktop/Temp/T]	
└─$ ls
meme.jpg  _meme.jpg.extracted
```

## Strings

> 使用 strings 可以查看文件中可以访问的字符串

## Steghide

> 在图片或者音频文件中隐藏密码信息

## LSB 隐藏痕迹

LSB也就是最低有效位 (Least Significant Bit)。原理就是图片中的像数一般是由三种颜色组成，即三原色，由这三种原色可以组成其他各种颜色，例如在PNG图片的储存中，每个颜色会有8bit，LSB隐写就是修改了像数中的最低的1bit，在人眼看来是看不出来区别的，也把信息隐藏起来了。譬如我们想把’A’隐藏进来的话，如下图，就可以把A转成16进制的0x61再转成二进制的01100001，再修改为红色通道的最低位为这些二进制串。

![图片\[4\]-隐写术总结-FancyPig's blog](https://img-blog.csdnimg.cn/img\_convert/3a8a3340bf72d85eacee322554ed7a39.webp?x-oss-process=image/format,png)

![图片\[5\]-隐写术总结-FancyPig's blog](https://img-blog.csdnimg.cn/img\_convert/c74bcb1ff7d59537759ba466595aecbd.webp?x-oss-process=image/format,png)

如果是要寻找这种LSB隐藏痕迹的话，有一个工具是个神器，可以来辅助我们进行分析。Stegsolve这个软件
