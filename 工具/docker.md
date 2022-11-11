# Docker

\[TOC]

## 1. 使用 Docker 安装软件

1. [Docker 官方镜像仓库查找](https://hub.docker.com/)
2.  实例：

    > 拿 redis 举例：
    >
    > ```shell
    > docker run -d -p 6379:6379 --name redis redis:latest
    > # docker run ： docker 运行一个软件
    > # -d : 后台运行
    > # -p 6379:6379 : 映射容器内端口到宿主机
    > # --name ： 起一个名字
    > # redis:latest ： 使用的软件源
    > ```

## 2. 构建自己的 Docker 镜像

1.  编写 DockerFile

    > 作用: 用来描述需要什么东西
    >
    > ```shell
    > FROM node:11
    > MAINTAINER easydoc.net
    >
    > # 复制代码
    > ADD . /app
    >
    > # 设置容器启动后的默认运行目录
    > WORKDIR /app
    >
    > # 运行命令，安装依赖
    > # RUN 命令可以有多个，但是可以用 && 连接多个命令来减少层级。
    > # 例如 RUN npm install && cd /app && mkdir logs
    > RUN npm install --registry=https://registry.npm.taobao.org
    >
    > # CMD 指令只能一个，是容器启动后执行的命令，算是程序的入口。
    > # 如果还需要运行其他命令可以用 && 连接，也可以写成一个shell脚本去执行。
    > # 例如 CMD cd /app && ./start.sh
    > CMD node app.js
    > ```
2.  Build 为镜像（安装包）和运行

    > ```shell
    > # 编译 
    > 	# docker build ： 按照 DockerFile build 项目
    >     # -t 设置镜像名字和版本号
    >     # . 当前目录
    > docker build -t test:v1 .
    >
    > # 运行 
    > docker run -p 8080:8080 --name test-hello test:v1
    > ```
3.  更多相关命令

    ```shell
    docker ps  # 查看当前运行中的容器
    docker images # 查看镜像列表
    docker rm container-id # 删除指定 id 的容器
    docker stop/start container-id # 停止/启动指定 id 的容器
    docker rmi image-id # 删除指定 id 的镜像
    docker volume ls # 查看 volume 列表
    docker network ls # 查看网络列表
    ```

## 3. 挂载目录

> 解决问题：
>
> * 使用 Docker 运行后，我们改了项目代码不会立刻生效，需要重新`build`和`run`，很是麻烦。
> * 容器里面产生的数据，例如 log 文件，数据库备份文件，容器删除后就丢失了。

1. 几种挂载方式
   1. `bind mount` 直接把宿主机目录映射到容器内，适合挂代码目录和配置文件。可挂到多个容器上
   2. `volume` 由容器创建和管理，创建在宿主机，所以删除容器不会丢失，官方推荐，更高效，Linux 文件系统，适合存储数据库数据。可挂到多个容器上
   3.  `tmpfs mount` 适合存储临时文件，存宿主机内存中。不可多容器共享。

       ![](https://typora-1311404666.cos.ap-beijing.myqcloud.com/img/20220830091643.png)
2.  挂载演示：

    > ```
    > bind mount` 方式用绝对路径 `-v D:/code:/app
    > volume` 方式，只需要一个名字 `-v db-data:/app
    > ```
    >
    > 示例： `docker run -p 8080:8080 --name test-hello -v D:/code:/app -d test:v1`

## 4. 多容器通信

1.  创建虚拟网络

    > 要想多容器之间互通，从 Web 容器访问 Redis 容器，我们只需要把他们放到同个网络中就可以了。
2.  实例：

    > 1. 创建一个名为 test-net 的网络 `docker network create test-net`
    > 2. 运行 Redis 在 test-net 网络中，别名为 redis `docker run -d --name redis --network test-net --network-alias redis redis:latest`
    > 3. 修改代码中访问 redis 的地址为网络别名 ![img](https://typora-1311404666.cos.ap-beijing.myqcloud.com/img/20220830092454.png)
    > 4. 运行 WEB 项目，使用同一个网络 `docker run -p 8080:8080 --name test -v D:/test:/app --network test-net -d test:v1`
    > 5. 查看数据 `http://localhost:8080/redis`

## 5. Docker-Compose

> 使用 dokcer-compose 可以将多个服务集合到一起，一键运行

1.  编写脚本： 编写一个 `docker-compose.yml` 描述依赖了那些服务

    ```yaml
    version: "3.7" # 版本号

    services: # 依赖那些服务
      app:
        build: ./ # 当前目录
        ports: 
          - 80:8080 
        volumes:
          - ./:/app # 挂载目录
        environment:
          - TZ=Asia/Shanghai  # 时区
      redis:
        image: redis:5.0.13 # redis 镜像
        volumes:
          - redis:/data # 挂载目录
        environment:
          - TZ=Asia/Shanghai # 时区

    volumes:
      redis:
    ```
2.  运行

    ```shell
    # 在docker-compose.yml 文件所在目录，执行：
    docker-compose up


    # 其他命令
    docker-compose up -d  # 在后台运行只需要加一个 -d 参数
    docker-compose ps # 查看运行状态
    docker-compose stop # 停止运行
    docker-compose restart # 重启
    docker-compose restart service-name # 重启单个服务
    docker-compose exec service-name sh # 进入容器命令行
    docker-compose logs [service-name] # 查看容器运行log
    ```

## 6. 发布和部署

> 我们也可以把自己 build 出来的镜像上传到 docker 提供的镜像库中，方便传播。 当然你也可以搭建自己的私有镜像库，或者使用国内各种大厂提供的镜像托管服务，例如：阿里云、腾讯云

1.  上传镜像

    > 1. 注册一个账号
    > 2. 创建一个镜像库
    > 3. 命令行登陆账号 `docker login -u username`
    > 4. 新建一个tag，名字必须跟你注册账号一样 `docker tag test:v1 username/test:v1`
    > 5. 推上去 `docker push username/test:v1`
    > 6. 部署 `docker run -dp 8080:8080 username/test:v1`

## 7. 备份和迁移数据

1.  迁移数据介绍

    > 容器中的数据，如果没有用挂载目录，删除容器后就会丢失数据。 前面我们已经讲解了如何 挂载目录 如果你是用`bind mount`直接把宿主机的目录挂进去容器，那迁移数据很方便，直接复制目录就好了 如果你是用`volume`方式挂载的，由于数据是由容器创建和管理的，需要用特殊的方式把数据弄出来。
2.  备份和导入 Volume 的流程

    > 备份：
    >
    > * 运行一个 ubuntu 的容器，挂载需要备份的 volume 到容器，并且挂载宿主机目录到容器里的备份目录。
    > * 运行 tar 命令把数据压缩为一个文件
    > * 把备份文件复制到需要导入的机器
    >
    > 导入：
    >
    > * 运行 ubuntu 容器，挂载容器的 volume，并且挂载宿主机备份文件所在目录到容器里
    > * 运行 tar 命令解压备份文件到指定目录
3.  备份 MongoDB

    > * 运行一个 mongodb，创建一个名叫`mongo-data`的 volume 指向容器的 /data 目录 `docker run -p 27018:27017 --name mongo -v mongo-data:/data -d mongo:4.4`
    > * 运行一个 Ubuntu 的容器，挂载`mongo`容器的所有 volume，映射宿主机的 backup 目录到容器里面的 /backup 目录，然后运行 tar 命令把数据压缩打包 `docker run --rm --volumes-from mongo -v d:/backup:/backup ubuntu tar cvf /backup/backup.tar /data/`
    >
    > 最后你就可以拿着这个 backup.tar 文件去其他地方导入了。
4.  恢复 Volume 数据演示

    > * 运行一个 ubuntu 容器，挂载 mongo 容器的所有 volumes，然后读取 /backup 目录中的备份文件，解压到 /data/ 目录 `docker run --rm --volumes-from mongo -v d:/backup:/backup ubuntu bash -c "cd /data/ && tar xvf /backup/backup.tar --strip 1"`
    > * 注意，volumes-from 指定的是容器名字 strip 1 表示解压时去掉前面1层目录，因为压缩时包含了绝对路径
