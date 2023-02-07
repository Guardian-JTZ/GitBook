# Python 虚拟环境

> Virtaulenvwrapper是virtualenv的扩展包，用于更方便管理虚拟环境，它可以做： - 将所有虚拟环境整合在一个目录下 - 管理（新增，删除，复制）虚拟环境 - 快速切换虚拟环境

### 1. 安装

```
# on Windows
pip install virtualenvwrapper-win
# on macOS / Linux
pip install --user virtualenvwrapper
# then make Bash load virtualenvwrapper automatically
echo "source virtualenvwrapper.sh" >> ~/.bashrc
source ~/.bashrc
```

### 2. 创建虚拟环境

```
# on macOS/Linux:
mkvirtualenv --python=python3.6 venv
# on Windows
mkvirtualenv --python=python3 venv
```

### 3. 激活环境

```
workon #列出虚拟环境列表
workon [venv] #切换环境
```

### 4. 退出环境

```
deactivate
```

### 5. 删除环境

```
rmvirtualenv venv
```

### 6. 其他有用指令

```
pip freeze #查看当前安装库版本
#创建 requirements.txt 文件，其中包含了当前环境中所有包及 各自的版本的简单列表
#保持部署相同，一键安装所有包
pip install -r requirements.txt
pip freeze > requirements.txt 
lsvirtualenv    #列举所有的环境
cdvirtualenv    #导航到当前激活的虚拟环境的目录中，相当于pushd 目录
cdsitepackages   # 和上面的类似，直接进入到 site-packages 目录
lssitepackages     #显示 site-packages 目录中的内容
```
