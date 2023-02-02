---
description: 用于直观地绘制出 AD 关系并帮助规划可能被忽视的攻击路径
---

# BloodHound

> * 安装教程一搜一大堆这里就直接贴了官方教程 --> [链接](https://bloodhound.readthedocs.io/en/latest/index.html)
> * 主要是解决一些安装时可能出现的问题
> * neo4j 如果想安装中文版本 --> [链接](https://we-yun.com/blog/prod-56.html)

## JAVA 环境问题

```bash
# 打开 neo4j.bat 文件
@ECHO OFF
rem Copyright (c) "Neo4j,"
rem Neo4j Sweden AB [http://neo4j.com]
rem
rem This file is part of Neo4j.
rem
rem Neo4j is free software: you can redistribute it and/or modify
rem it under the terms of the GNU General Public License as published by
rem the Free Software Foundation, either version 3 of the License, or
rem (at your option) any later version.
rem
rem This program is distributed in the hope that it will be useful,
rem but WITHOUT ANY WARRANTY; without even the implied warranty of
rem MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
rem GNU General Public License for more details.
rem
rem You should have received a copy of the GNU General Public License
rem along with this program.  If not, see <http://www.gnu.org/licenses/>.
SET "JAVA_HOME=F:\OpenSourceApp\JDK\JDK11"  # 添加这一行内容即可
SETLOCAL

Powershell -NoProfile -NonInteractive -NoLogo -ExecutionPolicy Bypass -File "%~dp0neo4j.ps1" %*
EXIT /B %ERRORLEVEL%
```

## 导入数据显示 %NAN%

这是因为版本不一致的原因，你可以选择安装一个旧版本进行测试应该就没有什么问题了
