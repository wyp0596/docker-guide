# Image——镜像

> Docker 镜像相当于系统ISO安装包一样的存在，容器是镜像的运行时，容器运行并不会影响镜像的内容。当然，镜像可以发布新版本。Docker 镜像采用分层存储技术，相同的存储层只存储一份，可以节约空间。

## 获取镜像

```shell
#从官方Docker Hub获取ubuntu:14.04
docker pull ubuntu:14.04
#运行
docker run -it --rm ubuntu:14.04 bash
cat /etc/os-release
exit
```

`docker run`: 就是运行容器的命令

`-it`：这是两个参数， `-i`交互式操作， `-t` 终端

`—rm`：这个参数是说容器退出后随之将其删除

`ubuntu:14.04`：这是指用 ubuntu:14.04 镜像为基础来启动容器

`bash`：放在镜像名后的是命令

`cat /etc/os-release`:这是 Linux 常用的查看当前系统版本的命令

`exit` :退出了这个容器



## 列出镜像

```shell
#列出当前镜像
docker images
#仓库名和标签都为<none>的是虚悬镜像,列出虚悬镜像
docker images -f dangling=true
#删除虚悬镜像
docker rmi $(docker images -q -f dangling=true)
#列出所有镜像,可能包括库名和标签都为<none>的中间层镜像
docker images -a
#列出部分镜像,比如ubuntu
docker images ubuntu
#过滤镜像
docker images -f since=mongo:3.2
docker images -f before=mongo:3.2
docker images -f label=com.example.version=0.1
#以特定格式列出镜像
docker images -q
docker images --format "{{.ID}}: {{.Repository}}"
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```



## commit

```shell
#运行一个镜像
docker run --name webserver -d -p 80:80 nginx
#进入容器命令行交互界面,并做修改
docker exec -it webserver bash
echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
exit
#查看修改
docker diff webserver
#将容器保存为镜像
docker commit \
    --author "test <test@gmail.com>" \
    --message "修改了默认网页" \
    webserver \
    nginx:v2
#查看镜像
docker images nginx
#查看镜像内的历史记录
docker history nginx:v2
#运行新的镜像
docker run --name web2 -d -p 81:80 nginx:v2
```

### 慎用 docker commit

1. 若清理不当会导致镜像臃肿
2. 操作为黑箱操作，生成的是黑箱镜像
3. 每次commit都在当前层操作，可能导致镜像臃肿
4. 一般在学习和被入侵后保存现场时用
5. 定制镜像用Dockerfile

### Dockerfile

> Dockerfile 是一个文本文件，其内包含了一条条的**指令(Instruction)**，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。



### 基本指令

```dockerfile
# FROM 指定基础镜像必须放在第一个,其中scratch为是一个空镜像
FROM nginx
# RUN 就像直接在命令行中输入的命令一样  
# shell 格式：RUN <命令>
# exec 格式：RUN ["可执行文件", "参数1", "参数2"]
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
# 使用 && 将各个所需命令串联起来。简化为 1 层,并进行了清理操作
RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```



### 构建镜像

```shell
# 方式1.在Dockerfile文件所在目录执行命令 格式如:docker build [选项] <上下文路径/URL/-> 
# 涉及了上下文的概念以及Docker的C/S设计
docker build -t nginx:v3 .
# 方式2.直接用 Git repo 进行构建
docker build https://github.com/twang2218/gitlab-ce-zh.git#:8.14
# 方式3.用给定的 tar 压缩包构建
docker build http://server/context.tar.gz
# 方式4.从标准输入中读取 Dockerfile 进行构建,它没有上下文,所以不能copy本地文件
docker build - < Dockerfile # 或者cat Dockerfile | docker build -
#方式5.从标准输入中读取上下文压缩包进行构建,并将压缩包内容作为上下文
docker build - < context.tar.gz
```



## Dockerfile指令



### COPY 复制文件

1. `COPY package.json /usr/src/app/`
2. 从上下文目录中复制,可以是通配符
3. `目标路径`可以是容器内的绝对路径，也可以是相对于工作目录的相对路径，不存在会自动生成
4. 源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。

### ADD 更高级的复制文件

1. `ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /`
2. 若源文件为压缩文件，将会自动解压缩这个压缩文件到`目标路径`去
3. 所有的文件复制均使用 `COPY` 指令，仅在需要自动解压缩的场合使用 `ADD`

### CMD 容器启动命令

1. `CMD echo $HOME` 相当于 `CMD [ "sh", "-c", "echo $HOME" ]`
2. 容器中的应用都应该以前台执行，容器内没有后台服务的概念
3. `CMD ["nginx", "-g", "daemon off;"]` 以前台形式运行nginx

### ENTRYPOINT 入口点

1. `ENTRYPOINT` 的目的和 `CMD` 一样，都是在指定容器**启动程序**及参数
2. 当指定了 `ENTRYPOINT` 后，`CMD`不再是直接的运行其命令，而是将 `CMD` 的内容作为参数传给 `ENTRYPOINT` 指令，实际执行时`<ENTRYPOINT> "<CMD>"`
3. 通过 `docker run` 的参数 `—entrypoint` 可以指定别的入口点
4. 可以让镜像变成像命令一样使用，`docker run`可以直接加上参数运行
5. 应用运行前的准备工作，启动容器就是启动主进程，但有些时候，启动主进程前，需要一些准备工作

### ENV 设置环境变量

> 通过环境变量，我们可以让一份 Dockerfile 制作更多的镜像，只需使用不同的环境变量即可。

```dockerfile
# 方式1
ENV VERSION 1.0
# 方式2
ENV VERSION=1.0 DEBUG=on \
    NAME="Happy Feet"
```

可以使用`$VERSION`这种形式来获取环境变量值



### ARG 构建参数

```dockerfile
# 方式1
ARG VERSION 1.0
# 方式2
ARG VERSION=1.0 DEBUG=on \
    NAME="Happy Feet"
```



1. 效果同`ENV` 但是只在构建时有效
2. `docker history` 可以看到所有值
3. `docker build` 中可以用 `--build-arg <参数名>=<值>` 来覆盖

### VOLUME 定义匿名卷

1. `VOLUME /data` 或者 `VOLUME ["/data","/test"]`
2. 挂在卷用以保存动态数据
3. 运行时可以覆盖这个挂载设置`docker run -d -v mydata:/data xxxx`

### EXPOSE 声明端口

1. `EXPOSE 8080`或者` EXPOSE ["80","8080"]`
2. 仅仅是声明运行时容器提供服务端口，并不会自动在宿主进行端口映射
3. `docker run -P` 时，会自动随机映射 `EXPOSE` 的端口

### WORKDIR 指定工作目录

1. `WORKDIR /app`
2. 指定工作目录（或者称为当前目录），改变环境状态并影响以后的层
3. 该目录需要已经存在，`WORKDIR` 并不会建立目录
4. 如果需要改变以后各层的工作目录的位置，那么应该使用 `WORKDIR` 指令

### USER 指定当前用户

1. `USER redis`
2. 指定当前用户，改变环境状态并影响以后的层
3. 用户必须是事先建立好的，否则无法切换

### HEALTHCHECK 健康检查

> Docker 1.12 后引入

HEALTHCHECK 支持下列选项：

- `--interval=<间隔>`：两次健康检查的间隔，默认为 30 秒；
- `--timeout=<时长>`：健康检查命令运行超时时间，如果超过这个时间，本次健康检查就被视为失败，默认 30 秒；
- `--retries=<次数>`：当连续失败指定次数后，则将容器状态视为 unhealthy，默认 3 次。

命令的返回值决定了该次健康检查的成功与否：0：成功；1：失败；

```dockerfile
# 如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令
HEALTHCHECK NONE
#每 5 秒检查一次，如果健康检查命令超过 3 秒没响应就视为失败，并且使用 curl -fs http://localhost/ || exit 1 作为健康检查命令。
HEALTHCHECK --interval=5s --timeout=3s \
  CMD curl -fs http://localhost/ || exit 1
```



### ONBUILD 为他人做嫁衣裳



1. `ONBUILD <其它指令>`
2. 只有当以当前镜像为基础镜像，去构建下一级镜像的时候才会被执行

## 其他方式



### 从rootfs压缩包导入

格式:`docker import [选项] <文件>|<URL>|- [<仓库名>[:<标签>]]`

```shell
#创建一个OpenVZ的ubuntu14.04的镜像
docker import \
    http://download.openvz.org/template/precreated/ubuntu-14.04-x86_64-minimal.tar.gz \
    openvz/ubuntu:14.04
```



### `docker save`和`docker load`

```shell
# 保存镜像
docker save alpine | gzip > alpine-latest.tar.gz

# 导入镜像
docker load -i alpine-latest.tar.gz

# 从一个机器将镜像迁移到另一个机器，并且带进度条的功能
docker save <镜像名> | bzip2 | pv | ssh <用户名>@<主机名> 'cat | docker load'
```



## 删除本地镜像

格式:`docker rmi [选项] <镜像1> [<镜像2> ...]`





## 参考

[Docker — 从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/)



