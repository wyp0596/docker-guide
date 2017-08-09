# Docker的清理

> Docker 会占用一定的磁盘空间，若不需要可以进行清理

## 容器

```shell
# 强制删除并清理容器，包括数据卷
docker rm -vf container-name

# 清理退出的容器
docker rm $(docker ps -q -f status=exited)

# 杀死所有正在运行的容器
docker kill $(docker ps -a -q)

# 删除所有已经停止的容器
docker rm $(docker ps -a -q)
```





## 镜像

```shell


# 清理虚悬镜像
docker rmi $(docker images -q -f "dangling=true")

# 删除所有虚悬镜像
docker rmi $(docker images -q -f dangling=true)

# 删除所有镜像
docker rmi $(docker images -q)
```



## 数据卷



```shell
# 列出虚悬数据卷
docker volume ls -qf dangling=true
# 删除虚悬数据卷
docker volume rm $(docker volume ls -qf dangling=true)
```





