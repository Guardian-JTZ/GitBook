# 字符串

```shell
#!/bin/bash

name="abcABC123ABCabc"

# 1. 计算字符串长度
echo ${#name}
echo `expr length $name`
echo `expr "$name" : '.*'

# 2. 使用正则表达式匹配
`echo `expr match "$name" 'abc[A-Z]*.2'` 
echo `expr "$name" : 'abc[A-Z]*.2'`   

# 3. 索引
# expr index $string $substring 在字符串$string中$substring第一次出现的数字位置
echo `expr index "$name" C12`

# 4. 提取子字符串
# ${string:position} 把$string中从第$postion个字符开始字符串提取出来。如果$string是"*"或"@"，则表示从位置参数中提取第$postion后面的字符串。
# ${string:position:length} 把$string中$postion个字符后面的长度为$length的字符串提取出来
echo ${name:7}           # 23ABCabc         
echo ${name:7:3}         # 23A
echo ${String:(-4)}                         # Cabc 
echo ${String: -4}                          # Cabc


# 5. 字符串移动
# ${string#substring}从$string左边开始，剥去最短匹配$substring子串
# ${string##substring}从$string左边开始，剥去最长匹配$substring子串
# ${string%substring} 从$string结尾开始，剥去最短匹配$substring子串
# ${string%%substring}从$string结尾开始，剥去最长匹配$substring子串
```
