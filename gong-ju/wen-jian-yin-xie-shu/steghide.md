# Steghide

Steghide是一款开源的隐写术软件，它可以让你在一张图片或者音频文件中隐藏你的秘密信息

## 使用

```shell
┌──(jtz㉿JTZ)-[~/Desktop/Temp/Agent_Sudo]
└─$ steghide extract -sf cute-alien.jpg
Enter passphrase:
wrote extracted data to "message.txt".
```

### 爆破密码

```shell
#bruteStegHide.sh 
#!/bin/bash

for line in `cat $2`;do
    steghide extract -sf $1 -p $line > /dev/null 2>&1
    if [[ $? -eq 0 ]];then
        echo 'password is: '$line
        exit
    fi  
done
```

```shell
┌──(jtz㉿JTZ)-[~/Desktop/Temp/Agent_Sudo]
└─$ ./bruteStegHide.sh  cute-alien.jpg passwd.txt
```
