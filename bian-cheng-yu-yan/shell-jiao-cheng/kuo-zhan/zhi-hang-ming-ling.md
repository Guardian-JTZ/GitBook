# 执行命令

## bash -c

```shell
#!/bin/bash

bash -c "echo 'HELLO WORLD'"
```

## exec

> * 在同一个 SHELL 内执行
> * 特点： 使用exec调用一个新脚本以后, 父脚本中exec行之后的内容就不会再执行了（相当于父shell被子shell删除）

```shell
#!/bin/bash

bash -c "echo 'HELLO WORLD'"
```

## source

> * 使用同一个 SHELL
> * 子shell不会将父shell吞噬，执行完这个子shell以后还可以继续执行父shell下面的指令

```shell
#!/bin/bash

source shell.sh
```

## ()

> () 中的命令将会在 子 SHELL 中执行

```shell
#!/bin/bash

$(cd ..;ls -l)
```
