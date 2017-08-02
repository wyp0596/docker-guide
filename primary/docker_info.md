## docker 是什么？

> Docker是一个开放源代码软件项目，让应用程序布署在软件容器下的工作可以自动化进行，借此在Linux操作系统上，提供一个额外的软件抽象层，以及操作系统层虚拟化的自动管理机制。Docker利用Linux核心中的资源分离机制，例如cgroups，以及Linux核心命名空间（name space），来建立独立的软件容器（containers）。这可以在单一Linux实体下运作，避免启动一个虚拟机器造成的额外负担。
>
> ——摘自维基百科

### 虚拟机:

![vm](/img/docker/VM.png)

### Docker:

![docker](/img/docker/Docker.png)

> ——图片来自www.docker.com



## docker 跟原有的工具有何区别？

> 实现更轻量级的虚拟化，方便快速部署

1. docker技术使软件交付标准化
2. 像集装箱一样生成镜像，使得开发环境与生产环境一致
3. docker的出现使得[一次构建，到处部署]成为了可能
4. 统一的管理服务
5. 持续交付应用
6. 快速(秒级)启动

- 传统的部署模式是：安装(包管理工具或者源码包编译)->配置->运行；
- Docker的部署模式是：复制->运行。

## docker 会对服务器端开发/部署带来什么变化？

1. 对开发的影响不大
2. 对于部署来说，是场革命。极大的减少部署的时间成本和人力成本

## 诱人之处

- 资源独立/隔离
- 环境一致性
- 轻量化
- Build Once, Run Everywhere 

## Linux系统要求

1. 64位操作系统
2. Linux内核大于等于3.10

推荐操作系统(全部为64位Linux操作系统):

1. Ubuntu 16.10
2. Ubuntu 16.04
3. Ubuntu 14.04
4. RHEL 7
5. CentOS 7
6. Fedora 24
7. Fedora 25
8. Debian 7.7
9. Debian 8.0



## 文章

[docker八个应用场景](http://dockone.io/article/126)