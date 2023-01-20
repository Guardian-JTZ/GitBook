# John

## 1. 破解密码保护的 Office 的文档

```shell
# 提取 哈希
python office2john.py 1.docx
# HashCat 破解 哈希
hashcat -m 9600 office_hash ./rockyou.txt
```

## 2. 破解受密码保护的 ZIP 文件

### 1. john

```shell
# 提取哈希
zip2john.py ./blueprints.zip
# 破解哈希
hashcat -a 0 -m 17200 pdf_hash_to_crack ./rockyou.txt
```

### 2. fcrackzip

> Fcrackzip是一款专门破解zip类型压缩文件密码的工具，工具小巧方便、破解速度快，能使用字典和指定字符集破解，适用于linux、mac osx 系统。

```bash
# 安装
sudo apt-get install fcrackzip
```

## 3. 破解受密码保护的 Keepass 文件

```shell
# 获取哈希
python keepass2john.py Master.kdbx 
# 破解 哈希
hashcat -a 0 -m 13400 keepass_hash ./rockyou.txt
```

## 4. 破解受保护的 PDF 文件

```shell
# 获取 哈希
python pdf2john.py inventory.pdf
# 破解 哈希
hashcat -a 0 -m 10500 pdf_hash ./rockyou.txt
```
