# 安装Docker

[Docker官网](https://www.docker.com/community-edition#/download)

## Linux

> 一般来说只支持64位的Linux，而且内核要够新（3.1以上），推荐`Ubuntu 16.04.2 LTS amd64` 本教程针对amd64架构

### 在线安装

#### 快速安装

* 使用[DaoCloud](https://www.daocloud.io)的[一键安装脚本](http://get.daocloud.io/#install-docker)  `curl -sSL https://get.daocloud.io/docker | sh`
* 使用官方安装脚本 `curl -sSL https://get.docker.com | sh`

#### Ubuntu

[官方教程](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-docker-ce)

```shell
# 更新源
sudo apt-get update
# 安装一些工具
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
# 添加 docker 官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
# 安装docker
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

#### Debian

[官方教程](https://docs.docker.com/engine/installation/linux/docker-ce/debian/#install-docker-ce)

```shell
# 更新源
sudo apt-get update
# 安装一些工具
sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common
# 添加 docker 官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
# 安装docker
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
```

#### CentOS

[官方教程](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#install-docker-ce)

```shell
# 安装一些工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
# 添加源
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 更新源信息
sudo yum makecache fast
# 安装docker
sudo yum install docker-ce
```

#### Fedora

[官方教程](https://docs.docker.com/engine/installation/linux/docker-ce/fedora/#install-docker-ce)

```shell
# 安装一些工具
sudo dnf -y install dnf-plugins-core
# 添加源
sudo dnf config-manager \
    --add-repo \
    https://download.docker.com/linux/fedora/docker-ce.repo
# 更新源信息
sudo dnf makecache fast
# 安装docker
sudo dnf install docker-ce
```

### 离线安装

参考开源项目 [docker-installer](https://github.com/wyp0596/docker-installer)

基本步骤：

1. 下载该项目： `git clone git@github.com:wyp0596/docker-installer.git` 
2. 选择操作系统对应的安装包
3. 使用root权限执行`sh install.sh`

## Mac

> 支持  OS X El Capitan 10.11 以上的 Mac OS

[官方安装指南](https://store.docker.com/editions/community/docker-ce-desktop-mac)

## Windows

> 支持 Win10 以上的 Windows

[官方安装指南](https://store.docker.com/editions/community/docker-ce-desktop-windows)

推荐使用 [Virtual Box](https://www.virtualbox.org) 虚拟机运行 `Linux` ，然后在虚拟机里面安装`Linux` 版的`Docker` 





