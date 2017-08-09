# Registry——镜像仓库



# Docker Hub

> Docker Hub 是Docker官方的镜像站，类似Github一般的存在，注册后我们也可以往上面上传镜像

[Docker Hub官网](https://hub.docker.com/)

```shell
# 默认情况下是从Docker Hub拉取镜像
# 从 Docker Hub 拉取一个镜像
docker pull nginx:alpine

# 从自建仓库拉取镜像（前提是这个仓库存在）
docker pull 192.168.12.12:5000/nginx:alpine
```



## Docker Registry

> registry 是存放和分发Docker镜像的一个服务

1. Docker hub 可以类比为 Github，是一个公共的镜像服务中心
2. 搭建 Registry 可以类比为搭建 GitLab 服务器，是一个私有的镜像服务中心
3. 自建 Registry 可以让镜像私有化，也可以提高镜像拉取速度

### 运行Registry

[Officical](https://hub.docker.com/_/registry/)

`docker run -d -p 5000:5000 --restart always --name registry registry`

#### 使用Registry

```shell
# 本地运行registry
docker run -d -p 5000:5000 --restart always --name registry registry
# 从官方拉取镜像
docker pull nginx:alpine
# 给镜像加标签
docker tag nginx:alpine localhost:5000/nginx:alpine
# 推送镜像到本地registry
docker push localhost:5000/nginx:alpine
# 删除本地镜像
docker rmi nginx:alpine localhost:5000/nginx:alpine
# 从本地registry 拉取镜像
docker pull localhost:5000/nginx:alpine
```

### Registry API

> Registry 有一些API可以方便的看到Registry上都有哪些镜像

[官方文档](https://docs.docker.com/registry/spec/api/#scope)

| API                    | 示例                                       | 说明     |
| ---------------------- | ---------------------------------------- | ------ |
| `/v2/`                 | `curl http://localhost:5000/v2/`         | 版本检查   |
| `/v2/_catalog`         | `curl http://localhost:5000/v2/_catalog` | 列出资源   |
| `/v2/<name>/tags/list` | `curl http://localhost:5000/v2/mysql/tags/list` | 列出镜像标签 |



### 关于HTTPS问题

> 使用registry时，如果没有配置https会导致其他机器无法拉取镜像

**错误提示：**

```shell
Error response from daemon: Get https://xx.xx.xx.xx:5000/v1/_ping: http: server gave HTTP response to HTTPS client
```

**解决方法：**

1. 方法1: 配置HTTPS，参考[官方文档](https://docs.docker.com/registry/deploying/#run-an-externally-accessible-registry)
2. 方法2: 在客户机配置该registry为非安全源

```shell
# 编辑或者创建 daemon.json 配置文件
vi /etc/docker/daemon.json
# 添加或者输入如下内容
{ "insecure-registries":["10.30.20.214:5000"] }
# 重启 docker
sudo service docker restart
# 至此便可以正常拉取镜像了
```



### 定制可迁移的registry

> 若需要在一个内网环境部署docker，那么定制一个可迁移的registry会变使部署变得方便

#### 1. 准备好一个registry容器

```shell
# 1. 运行registry容器
docker run --name some_registry -d -p 5000:5000 --restart always  registry
# 2. 从docker hub拉取镜像,如nginx:alpine
docker pull nginx:alpine
# 3. 标记该镜像
docker tag nginx:alpine localhost:5000/nginx:alpine
# 4. 推送该镜像到本地registry
docker push localhost:5000/nginx:alpine
# 重复3和4的步骤，将需要的镜像都推送到本地registry
```

#### 2. 导出旧registry的数据

```shell
# some_registry为旧registry容器名
docker run --rm --volumes-from some_registry -v $(pwd):/backup registry sh -c "cd /var/lib/ && tar cvf /backup/backup.tar registry"
```

#### 3. 运行新registry容器

```shell
# 在目标机器运行新registry容器（注意端口占用）
docker run --name new_registry -d -v /var/lib/registry  -p 5000:5000 --restart always registry
```

#### 4. 导入数据

```shell
# 导入数据（注意backup.tar要在当前路径下）
docker run --rm --volumes-from new_registry -v $(pwd):/backup registry sh -c "cd /var/lib/ && tar xvf /backup/backup.tar"
# 查看registry目录，检查是否导入数据成功
curl localhost:5000/v2/_catalog
```

