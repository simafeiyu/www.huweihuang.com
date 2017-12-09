---
title: "[Docker] Docker常用命令原理图"
catalog: true
date: 2017-07-09 10:50:57
type: "categories"
subtitle:
header-img: "/img/article_header_img/article_header.png"
tags:
- Docker
catagories:
- Docker
---

## docker常用命令原理图概览：

<img src="http://res.cloudinary.com/dqxtn0ick/image/upload/v1510578333/article/docker/commands/dockerCommands.jpg" width="70%">

## （一）image layer（镜像层）

镜像可以看成是由多个镜像层叠加起来的一个文件系统，镜像层也可以简单理解为一个基本的镜像，而每个镜像层之间通过指针的形式进行叠加。

![1](/img/article/docker/docker常用命令原理图/1.png)

根据上图，镜像层的主要组成部分包括镜像层id，镜像层指针【指向父层】，元数据【layer metadata】包含了docker构建和运行的信息还有父层的层次信息。

只读层和读写层【top layer】的组成部分基本一致。同时读写层可以转换成只读层【docker commit操作实现】

## （二）image（镜像）---【只读层的集合】

1、镜像是一堆只读层的统一视角，除了最底层没有指向外，每一层都指向它的父层，统一文件系统（union file system）技术能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在，在用户的角度看来，只存在一个文件系统。而每一层都是不可写的，就是只读层。

![2.1](/img/article/docker/docker常用命令原理图/2.1.png)

## （三）container（容器）---【一层读写层+多层只读层】

1、容器和镜像的区别在于容器的最上面一层是读写层【top layer】，而这边并没有区分容器是否在运行。运行状态的容器【running container】即一个可读写的文件系统【静态容器】+隔离的进程空间和其中的进程。

 ![3.1](/img/article/docker/docker常用命令原理图/3.1.png)

隔离的进程空间中的进程可以对该读写层进行增删改，其运行状态容器的进程操作都作用在该读写层上。每个容器只能有一个进程隔离空间。

![3.2](/img/article/docker/docker常用命令原理图/3.2.png)

## （四）docker常用命令说明

### 1、标识说明

#### 1）image---（统一只读文件系统）

![4.1.1](/img/article/docker/docker常用命令原理图/4.1.1.png)

#### 2）静态容器【未运行的容器】---（统一可读写文件系统）

![4.1.2](/img/article/docker/docker常用命令原理图/4.1.2.png)

#### 3）动态容器【running container】---（进程空间（包括进程）+统一可读写文件系统）

![4.1.3](/img/article/docker/docker常用命令原理图/4.1.3.png)

### 2、命令说明

#### a）docker生命周期相关命令:

##### 1）docker create <image-id>

![4.2.1.1](/img/article/docker/docker常用命令原理图/4.2.1.1.png)

即为只读文件系统添加一层可读写层【top layer】，生成可读写文件系统，该命令状态下容器为静态容器，并没有运行。

##### 2）docker start（restart） <container-id>        【docker stop即为docker start的逆过程】

![4.2.1.2](/img/article/docker/docker常用命令原理图/4.2.1.2.png)

即为可读写文件系统添加一个进程空间【包括进程】，生成动态容器【running container】

##### 3）docker run <image-id>

![4.2.1.3](/img/article/docker/docker常用命令原理图/4.2.1.3.png)



docker run=docker create+docker start

类似流程如下 ：

![4.2.1.3.1](/img/article/docker/docker常用命令原理图/4.2.1.3.1.png)

##### 4）docker stop <container-id>

![4.2.1.4](/img/article/docker/docker常用命令原理图/4.2.1.4.png)

向运行的容器中发一个SIGTERM的信号，然后停止所有的进程。即为docker start的逆过程。

##### 5）docker kill <container-id>

![4.2.1.5](/img/article/docker/docker常用命令原理图/4.2.1.5.png)

docker kill向容器发送不友好的SIGKILL的信号，相当于快速强制关闭容器，与docker stop的区别在于docker stop是正常关闭，先发SIGTERM信号，清理进程，再发SIGKILL信号退出。

##### 6）docker pause <container-id>    【docker unpause为逆过程】---比较少使用

![4.2.1.6](/img/article/docker/docker常用命令原理图/4.2.1.6.png)

暂停容器中的所有进程，使用cgroup的freezer顺序暂停容器里的所有进程，docker unpause为逆过程即恢复所有进程。比较少使用。

##### 7）docker commit <container-id>

![4.2.1.7](/img/article/docker/docker常用命令原理图/4.2.1.7.png)

![4.2.1.7.2](/img/article/docker/docker常用命令原理图/4.2.1.7.2.png)

把容器的可读写层转化成只读层，即从容器状态【可读写文件系统】变为镜像状态【只读文件系统】，可理解为【固化】。

##### 8）docker build

![4.2.1.8.1](/img/article/docker/docker常用命令原理图/4.2.1.8.1.png)

![4.2.1.8.2](/img/article/docker/docker常用命令原理图/4.2.1.8.2.png)

**docker build=docker run【运行容器】+【进程修改数据】+docker commit【固化数据】，不断循环直至生成所需镜像。**

循环一次便会形成新的层（镜像）【原镜像层+已固化的可读写层】

docker build 一般作用在dockerfile文件上。

#### b）docker查询类命令：【查询对象：①image，②container，③image/container中的数据，④系统信息[容器数，镜像数及其他]】

##### 1.1）docker images

![4.2.2.1.1](/img/article/docker/docker常用命令原理图/4.2.2.1.1.png)

docker images 列出当前镜像【以顶层镜像id来表示整个完整镜像】，每个顶层镜像下面隐藏多个镜像层。

##### 1.2）docker images -a

![4.2.2.1.2](/img/article/docker/docker常用命令原理图/4.2.2.1.2.png)

docker images -a列出所有镜像层【排序以每个顶层镜像id为首后接该镜像下的所有镜像层】，依次列出每个镜像的所有镜像层。

##### 1.3）docker history <image-id>

![4.2.2.1.3](/img/article/docker/docker常用命令原理图/4.2.2.1.3.png)

docker history 列出该镜像id下的所有历史镜像。

##### 2.1）docker ps

![4.2.2.2.1](/img/article/docker/docker常用命令原理图/4.2.2.2.1.png)

列出所有运行的容器【running container】

##### 2.2）docker ps -a 

![4.2.2.2.2](/img/article/docker/docker常用命令原理图/4.2.2.2.2.png)

列出所有容器，包括静态容器【未运行的容器】和动态容器【running container】

##### 3.1）docker inspect <container-id> or <image-id>

![4.2.2.3.1](/img/article/docker/docker常用命令原理图/4.2.2.3.1.png)

提取出容器或镜像最顶层的元数据。

##### 3.2）docker info

显示 Docker 系统信息，包括镜像和容器数。

#### c）docker操作类命令：

##### 1）docker rm <container-id>

![4.2.3.1](/img/article/docker/docker常用命令原理图/4.2.3.1.png)

docker rm会移除镜像，该命令只能对静态容器【非运行状态】进行操作。

通过docker rm -f <container-id>的-f （force）参数可以强制删除运行状态的容器【running container】。

##### 2）docker rmi <image-id>

![4.2.3.2](/img/article/docker/docker常用命令原理图/4.2.3.2.png)

##### 3）docker exec <running-container-id>

![4.2.3.3](/img/article/docker/docker常用命令原理图/4.2.3.3.png)

docker exec会在运行状态的容器中执行一个新的进程。

##### 4）docker export <container-id>

![4.2.3.4](/img/article/docker/docker常用命令原理图/4.2.3.4.png)

docker export命令创建一个tar文件，并且移除了元数据和不必要的层，将多个层整合成了一个层，只保存了当前统一视角看到的内容。

 

附：本文在[http://dockone.io/article/783](http://dockone.io/article/783)的基础上做整理和修改。