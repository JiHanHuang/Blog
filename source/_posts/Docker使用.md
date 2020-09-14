---
title: Docker使用
date: 2020-09-14 11:27:39
tags:
---


苦于python环境的迁移，开始尝试使用docker来实现不同linux平台的环境搬迁。(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *
*[Docker官方](https://www.docker.com)*  
*[参考教程1](https://www.runoob.com/docker/centos-docker-install.html)  [参考教程2](https://yeasy.gitbooks.io/docker_practice/install/mirror.html)*  
*[Docker Hub](https://hub.docker.com/)*

<!-- more -->

### 创建基本镜像
我这儿的基础镜像是：`centos7+python3.7`  官方已经不支持centos7以下的版本了。   
但是由于我想用这个基本镜像做开发，因此还根据我自己添加了其他的一些工具。  
基本步骤如下[参考](https://docs.docker.com/install/linux/docker-ce/centos/)：  
**脚本安装(懒人)：**
```sh
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```
**安装包安装(离线)：**
1. 下载安装包，这儿有三类安装包，保险都下：https://download.docker.com/linux/centos/7/x86_64/stable/Packages/  
2. 安装：
   ```sh
   $ sudo yum install /path/to/package.rpm
   ```
3. 启动：
   ```sh
   $ sudo systemctl start docker
   ```
4. 验证：
   ```sh
    $ sudo docker run hello-world
   ```
**社区版安装：**
1. 卸载旧版本docker
   ```sh
   $ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
   ```
2. 由于linux是社区版本(开源)，需要安装docker仓库，以便更新.
   ```sh
   $ sudo yum install -y yum-utils \
        device-mapper-persistent-data \
        lvm2
    ```
3. 设置仓库：
   ```sh
   $ sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo
   ```
4. 官网还有一些配置操作，我这儿做开发，基本用不到。跳过，直接安装。(如果网速慢，自行配置yum源)
   ```sh
   $ sudo yum install docker-ce docker-ce-cli containerd.io
   ```
5. 查看需要安装的版本：
   ```sh
   $ yum list docker-ce --showduplicates | sort -r
    docker-ce.x86_64            3:19.03.5-3.el7                    docker-ce-stable 
    ...
   ```
6. 安装docker：
   ```sh
   $ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
   $ sudo yum install docker-ce-19.03.5 docker-ce-cli-19.03.5 containerd.io
   ```
7. 启动docker：
   ```sh
   $ sudo systemctl start docker
   ```
8. 检查安装是否成功：
   ```sh
   $ sudo docker run hello-world
   ```
**创建镜像**
1. 最保险的方式，就是创建`系统+平台`这种镜像，但是缺点是镜像占用空间大。
2. 直接创建`平台`镜像，占用空间小，但是有可能在不同版本的系统上运行不畅。

那么我们两种都试试。
老规矩，给docker hub加速，[参考](https://yeasy.gitbooks.io/docker_practice/install/mirror.html)：  
Ubuntu 16.04+、Debian 8+、CentOS 7  
对应`/etc/docker/daemon.json`文件中添加：
```json
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://hub-mirror.c.163.com"
  ]
}
```
重启服务：
```sh
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```
1. 系统+镜像  
   1. 上docker hub，注册（docker2jihan）
   2. 找个系统，我喜欢centos7，找个7.5纯净版的。[官方镜像](https://hub.docker.com/_/centos?tab=tags)
   ```sh
   $ docker pull centos:centos7.5.1804
   $ docker images
        REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
        centos              centos7.5.1804      cf49811e3cdb        9 months ago        200MB
   ```
   3. 进入到相应该镜像：
   ```sh
   docker run -t -i centos:centos7.5.1804 /bin/bash
    <-i 参数后面跟镜像名:标签>
    ```
    4. 安装各种你所需要的东西，我这儿安装了常用命令，python等。宿主机到docker拷贝`docker cp .vimrc <容器ID>:/root/`，反正也一样。
    5. 制作镜像：
    ```sh
    docker commit -m="init and conda" -a="jihan" <容器ID> <name>:<tag>
    ```
    6. 上传镜像或者导出镜像：  
   上传：  
   创建tag：`docker tag IMAGEID new_repository newTAG`  
   登录验证：`docker login`  
   上传docker hub：`docker push <hub-user>/<repo-name>:<tag>`  
   导出：  
   `docker save  <repo-name>:<tag> -o <name>.tar`  
   导入：  
   `docker import <name>.tar - <repo-name>:<tag`
