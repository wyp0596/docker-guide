# Docker搭建开发环境

> 利用 Docker 可以非常方便快速地搭建开发环境



### MySQL

> [官方Repository](https://hub.docker.com/_/mysql/)

### 运行

`docker run --name some-mysql -p 3306:3306 -v ~/my_data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=admin -d mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci`

说明：

1. `some-mysql` 是自定义的容器名称
2. `-p 3306:3306` 是端口映射配置
3. `~/my_data/mysql` 代表存储mysql的配置以及数据的目录

### 连接测试

`docker run -it --link some-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'`

## Redis

> [官方Repository](https://hub.docker.com/_/redis/)

### 运行

`docker run --name some-redis -d -p 6379:6379 redis:alpine redis-server`

说明：

1. `some-redis` 是自定义的容器名称
2. `-p 6379:6379` 是端口映射配置

### 连接测试

`docker run -it --link some-redis:redis --rm redis:alpine redis-cli -h redis -p 6379`



## Mongo

> [官方Repository](https://hub.docker.com/_/mongo/)

### 运行

`docker run --name some-mongo -v ~/my_data/mongo:/data/db -d -p 27017:27017 mongo`

说明：

1. `some-mongo` 是自定义的容器名称
2. `-p 27017:27017` 是端口映射配置
3. `~/my_data/mongo` 代表存储mongo的配置以及数据的目录

### 连接测试

`docker run -it --link some-mongo:mongo --rm mongo sh -c 'exec mongo "$MONGO_PORT_27017_TCP_ADDR:$MONGO_PORT_27017_TCP_PORT/test"'`



## Nginx

> [官方Repository](https://hub.docker.com/_/nginx/)

### 运行

`docker run --name some-nginx -v ~/my_data/nginx:/usr/share/nginx/html:ro -d -p 8080:80 nginx:alpine`

说明：

1. `some-nginx` 是自定义的容器名称
2. `-p 8080:80` 是端口映射配置
3. `~/my_data/nginx` 代表存储nginx静态文件的目录

## openjdk

> [官方Repository](https://hub.docker.com/_/openjdk/)



## Node

> [官方Repository](https://hub.docker.com/_/node/)



