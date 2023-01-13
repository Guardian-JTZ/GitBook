# Exiftool

## 介绍

Exiftool 是一个免费的、跨平台的开源软件，用于读写和处理图像、音视频和 PDF 等文件的元数据。ExifTool可以作为Perl库（Image::ExifTool）使用，也有功能齐全的命令行版本。ExifTool支持很多类型的元数据，包括Exif、IPTC、XMP、JFIF、GeoTIFF、ICC配置文件、Photoshop IRB、FlashPix、AFCP和ID3，以及众多品牌的数码相机的私有格式的元数据。

## 什么是 Exif

Exif是可交换图像文件格式（Exchangeable image file format），是一种标准，定义了与数码相机捕获的图像（或其他媒体）有关的信息，用于存储重要的数据，比如相机的曝光、拍摄日期和时间，甚至GPS定位等。在早期，摄影师需要随身携带笔记本来记录重要信息，如日期、快门速度、光圈等，这非常麻烦而且容易出错。如今，每台数码相机都支持Exif，能够将拍摄时的很多参数通过这种格式（Exif）记录到照片中，这些照片（或其他类型的文件）中的额外数据就叫元数据（metadata），它由一系列参数组成，如快门速度、光圈、白平衡、相机品牌和型号、镜头、焦距等等。Exif信息可能会造成隐私泄露（相机型号、位置等），在社会工程学中，Exif也是获取目标信息的一种手段，所以建议在把照片上传到互联网之前先清理Exif数据。

## ExifTool支持的文件类型和标签

* ExifTool支持的文件类型（File Type）：[参考官网Supported File Types](https://exiftool.org/#supported)
* ExifTool支持的标签（Tag）：[参考官网ExifTool Tag Names](https://exiftool.org/TagNames/index.html)

## 命令格式

```shell
读取：exiftool [OPTIONS] [-TAG...] [--TAG...] FILE...
写入：exiftool [OPTIONS] -TAG[+-<]=[VALUE]... FILE...
复制：exiftool [OPTIONS] -tagsFromFile SRCFILE [-SRCTAG[>DSTTAG]...] FILE...
其他：exiftool [ -ver | -list[w|f|r|wf|g[NUM]|d|x] ]
```

## 参数信息

```shell
标签选项
  -TAG or --TAG                    提取或排除指定的标签
  -TAG[+-^]=[VALUE]                向标签写入新值
  -TAG[+-]<=DATFILE                从数据文件读取标签写入文件
  -TAG[+-]<SRCTAG                  复制标签值（见-tagsFromFile）

  -tagsFromFile SRCFILE            复制某文件的标签值
  -x TAG      (-exclude)           排出指定标签
  
输入输出文本格式
  -args       (-argFormat)         将元数据格式化为ExifTool参数
  -b          (-binary)            以二进制输出元数据
  -c FMT      (-coordFormat)       设置GPS坐标格式
  -charset [[TYPE=]CHARSET]        指定字符编码
  -csv[[+]=CSVFILE]                以CSV格式导出/导入标签
  -d FMT      (-dateFormat)        设置日期/时间的格式
  -D          (-decimal)           以十进制显示标签ID号
  -E,-ex,-ec  (-escape(HTML|XML|C))为HTML，XML或C的转义标记值
  -f          (-forcePrint)        强制打印所有指定的标签
  -g[NUM...]  (-groupHeadings)     按标签组输出
  -G[NUM...]  (-groupNames)        打印每个标签的组名
  -h          (-htmlFormat)        输出为HTML格式
  -H          (-hex)               以十六进制显示标签ID号
  -htmlDump[OFFSET]                生成HTML格式的二进制转储
  -j[[+]=JSONFILE] (-json)         以JSON格式导出/导入标签
  -l          (-long)              使用长2行输出格式（标签、值各一行）
  -L          (-latin)             使用Windows Latin1编码
  -lang [LANG]                     设定当前语言
  -listItem INDEX                  从列表中提取特定项目
  -n          (--printConv)        不转换，直接打印
  -p FMTFILE  (-printFormat)       以指定格式输出
  -php                             将标签导出为PHP数组
  -s[NUM]     (-short)             简短输出
  -S          (-veryShort)         非常简短输出
  -sep STR    (-separator)         设置列表项的分隔符字符串
  -sort                            按字母顺序对输出进行排序
  -struct                          启用结构化信息的输出
  -t          (-tab)               以制表符分隔的列表格式输出
  -T          (-table)             以表格格式输出
  -v[NUM]     (-verbose)           打印详细消息
  -w[+|!] EXT (-textOut)           写入（或覆盖！）输出的文本文件
  -W[+|!] FMT (-tagOut)            为每个标签写入输出文本文件
  -Wext EXT   (-tagOutExt)         用-W指定要写入的文件类型
  -X          (-xmlFormat)         使用RDF/XML输出格式

执行选项
  -a          (-duplicates)        允许提取重复的标签
  -e          (--composite)        不生成复合标签
  -ee         (-extractEmbedded)   从嵌入式文件中提取信息
  -ext[+] EXT (-extension)         只处理具有指定扩展名的文件
  -F[OFFSET]  (-fixBase)           修复制造商Makernotes偏移
  -fast[NUM]                       提取元数据时提高速度
  -fileOrder[NUM] [-]TAG           设置文件处理顺序
  -i DIR      (-ignore)            忽略指定的目录名称
  -if[NUM] EXPR                    按条件处理文件
  -m          (-ignoreMinorErrors) 忽略小错误和警告
  -o OUTFILE  (-out)               设置输出文件或目录名称
  -overwrite_original              重命名tmp文件覆盖原始文件
  -overwrite_original_in_place     通过复制tmp文件覆盖原始文件
  -P          (-preserve)          保留文件修改日期/时间
  -password PASSWD                 处理受保护文件的密码
  -progress[:[TITLE]]              显示文件进度计数
  -q          (-quiet)             -q不显示正常消息，-q-q不显示警告
  -r[.]       (-recurse)           递归处理子目录
  -scanForXMP                      扫描所有文件以获取XMP
  -u          (-unknown)           提取未知标签
  -U          (-unknown2)          提取未知的二进制标签
  -wm MODE    (-writeMode)         设置用于写入/创建标签的模式
  -z          (-zip)               读/写压缩信息

其他选项
  -@ ARGFILE                       从文件中读取命令行参数
  -k          (-pause)             结束前先暂停
  -list[w|f|wf|g[NUM]|d|x]         列出各种exiftool功能
  -ver                             打印版本号
  --                               结束选项

特殊功能
  -geotag TRKFILE                  从指定的GPS日志对图像进行地理标记
  -globalTimeShift SHIFT           移位所有格式化的日期/时间值
  -use MODULE                      从插件模块添加功能

实用工具
  -delete_original[!]              删除"_original"备份
  -restore_original                从"_original"备份还原

高级选项
  -api OPT[[^]=[VAL]]              设置ExifTool API选项
  -common_args                     定义通用参数
  -config CFGFILE                  指定配置文件名
  -echo[NUM] TEXT                  将文本回显到stdout或stderr
  -efile[NUM][!] ERRFILE           保存错误的文件名
  -execute[NUM]                    一行执行多个命令
  -srcfile FMT                     处理其他文件来源
  -stay_open FLAG                  继续阅读-@ argfile，即使在EOF之后
  -userParam PARAM[[^]=[VAL]]      设置用户参数 (API UserParam opt)
```

## 使用

> 几个常用的参数   `-r`：递归处理子目录   `-overwrite_original`：不备份\_original文件，直接覆盖   `-restore_original`：恢复备份   `-delete_original`：删除备份

1.  读取文件所有元数据

    ```shell
    exiftool a.jpg
    ```
2.  写入标签artist、值rmnof到文件a.jpg(如果artist已存在将更新其值)

    ```shell
    exiftool -artist=rmnof a.jpg
    exiftool -artist=rmnof a.jpg b.jpg c.jpg  (同时写入多个文件)
    exiftool -artist=rmnof D:/images    (写入目录中所有文件)
    exiftool -artist=rmnof D:/images -r   (递归处理子目录)
    exiftool -artist="rmnof" -copyright="rmnof.com" a.jpg (同时写入多个标签)
    ```
3.  将创建时间、光圈、快门速度和ISO四项以列表形式保存为out.txt

    ```shell
    exiftool -T -createdate -aperture -shutterspeed -iso DIR > out.txt
    ```
4.  打印某照片的尺寸和曝光时间

    ```shell
    exiftool -s -ImageSize -ExposureTime a.jpg
    ```
5.  递归扫描某目录所有照片，将共有的元数据写入相同文件名的txt文件中

    ```shell
    exiftool -r -w .txt -common DIR
    ```
6.  生成image.raw的缩略图thumbnail.jpg

    ```shell
    exiftool -b -ThumbnailImage image.raw > thumbnail.jpg
    ```
7.  从a.jpg提取完整的xmp数据记录

    ```shell
    exiftool -xmp -b a.jpg> out.xmp
    ```
8.  递归删除某目录下所有文件的全部元数据

    ```shell
    exiftool -all= -r DIR
    ```

## 基于ExifTool引擎的实用程序

> 详细查看官方文档: [ExifTool实用程序](https://exiftool.org/#related)

1. WIndows
   * [Metadata++](https://www.logipole.com/metadata++-en.htm)：图形界面，可以查看、编辑、提取和复制元数据。
   * [GeoSetter](http://www.geosetter.de/en/)：图形界面，用于显示和更改图片的GPS数据。
   * [AdvancedRenamer](http://www.advancedrenamer.com/)：图形界面，通过元数据信息重命名文件。
   * [AutoJpegTrunk](http://www.avtonomer.net/content/view/332/42/)：图形界面，清理图片所有元数据的工具。
   * [ExifTool GUI for Windows](https://exiftool.org/gui/)：图形界面，可以查看元数据，和一些编辑功能
2. Android
   * [ExifTool for Android](https://play.google.com/store/apps/details?id=com.exiftool.free)：查看、修改或删除图片、视频等文件的元数据。
3. 在线工具
   * [Jeffrey’s Image Metadata Viewer](http://exif.regex.info/exif.cgi)：在线元数据查看器。
   * [The eXif.er](http://www.thexifer.net/)：在线Exif元数据编辑器。
   * [Get-IPTC-Photo-Metadata](https://getiptcpmd.nitsvc.net/getiptcpmd.html)：在线IPTC元数据查看器。
4. [ExifTool 命令示例](https://exiftool.org/examples.html)
