# Container——容器

> Docker 容器是镜像的运行时，容器运行并不会影响镜像的内容，一个镜像可以运行多个容器。



## 启动

### 新建并启动

```shell
# -t 分配伪终端并绑定到容器标准输出 -i 让容器标准输入打开
# docker run 创建容器
sudo docker run -t -i ubuntu:14.04 /bin/bash
pwd
ls
```

1. 若本地不存在镜像会自动下载
2. 利用镜像创建并启动容器
3. 分配一个文件系统,并在只读镜像层外挂载一层读写层
4. 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
5. 从地址池配置一个 ip 地址给容器
6. 执行用户指定的应用程序
7. 执行完毕后容器被终止

### 启动已终止的容器

`docker start <容器标识>`



## 守护态运行

通常通过参数`-d`实现

要获取容器的输出信息，可以通过 `docker logs` 命令



## 终止

1. `docker stop <容器标识>`来终止守护态运行中的容器

2. `exit`或`Ctrl+d`来退出终端容器

3. 终止状态的容器可以用 `docker ps -a` 命令看到

4. 处于终止状态的容器，可以通过 `docker start <容器标识>` 命令来重新启动

5. `docker restart <容器标识>` 命令会将一个运行态的容器终止，然后再重新启动它

   ​

## 进入容器

进入守护态运行中的容器

### `attach`命令

`docker attach  <容器标识>`

当多个窗口同时 `attach` 到同一个容器的时候，所有窗口都会同步显示

### `nsenter`命令

[方法步骤](https://yeasy.gitbooks.io/docker_practice/content/container/enter.html)

## 导出和导入

### 导出容器

`docker export 7691a814370e > ubuntu.tar`

### 导入容器

`cat ubuntu.tar | sudo docker import - test/ubuntu:v1.0`

## 删除

1. 删除终止状态容器`docker rm  <容器标识>`
2. 强制删除运行中的容器`docker rm -f <容器标识>`
3. 清理所有处于终止状态的容器`docker rm $(docker ps -a -q)`







## 参考

[Docker — 从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/)



