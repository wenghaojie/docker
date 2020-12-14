# 1.学前准备

（1）linux

（2）spingboot（非必要）

## 2.docker学习

* Docker概述

*  Docker安装

* Docker命令

*  Docker镜像

* 容器数据卷 

*  Dockerfile

* Docker网络原理

* IDEA整合Docker

* Docker compose

* Docker swarm

* Li/CD

* Jenkins

# 3.Docker概述

## 1.Docker出现

1. 问题：我的程序在我的电脑上可以运行

2. docker的思想来源于集装箱

3. 隔离：docker的核心思想

4. docker容器技术也是一种虚拟化技术

5. docker是基于go语言开发的

6. docker比虚拟机有更少的抽象层

7. docker利用宿主机的内核，vm需要guestos

## 2.组成

镜像（imange）：好比模板。用来创建容器服务

容器（contamer）：独立运行一个或一组应用

仓库（repository）：存放镜像，分为私有仓库和公有仓库（dockerhub）

## 3.docker工作原理

docker是一个client-server结构的系统，守护进程运行在主机上，通过socker从客户端访问。Docker-server接收docker-client指令，从而执行这个指令

# 3.Docker安装

## 1.环境

centos7

## 2.环境查看

系统内核：

```shell
uname -r 
```

系统版本：

 ```shell
cat /etc/os-release
 ```

## 3.安装

1.卸载旧版本的docker

```shell 
yum remove docker
```

2.安装需要的安装包

```shell
yum install -y yum-utils
```

3.设置镜像仓库（配置软件源，建议阿里云）

```shell
 yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

4.更新yum软件包索引

```shell
yum makecache fast
```

5.安装docker（ce社区版，ee企业版）

```shell
yum install docker-ce docker-ce-cli containerd.io
```

6.启动docker

```shell
systemetl start docker
```

7.验证docker是否安装成功

```shell
docker version
```

8.运行hello-world验证docker

```shell
docker run hello-world
```

\#hello-world实现流程

![image-20201214110609206](C:\Users\一切安好\AppData\Roaming\Typora\typora-user-images\image-20201214110609206.png)

9.查看下载的hello-world镜像

```shell
docker images
```

## 4.卸载docker

a.卸载依赖

```shell
yum remove docker-ce docker-ce-cli containerd.io
```

b.删除资源（rm -rf /var/lib/docker）

```shell
rm -rf /var/lib/docker
```

#/var/lib/docker是docker默认工作路径

## 5.阿里云镜像加速

A.登录阿里云找到容器服务

B.找到镜像加速地址

C.配置使用

![image-20201214135600203](C:\Users\一切安好\AppData\Roaming\Typora\typora-user-images\image-20201214135600203.png)

# 4.Docker常用命令

## 1.帮助命令

```shell
docker version      #显示docker版本信息

docker info        	#显示docker系统信息，包括镜像容器数量

docker 命令 --help   #帮助命令，查看命令的用法
```

#帮助文档地址：docker官网下的reference中

## 2.镜像命令

```shell
docker images                    #查看本机上的镜像

可选项：-a 列出所有镜像  -q 只显示镜像的id

docker search (镜像名)             #搜索镜像

可选项：例 --filter=start=3000 搜索出来镜像只显示start大于3000的镜像

docker pull (镜像名[:tar])         #下载镜像

docker rmi -f (id或镜像名)   	    #删除指定镜像

docker rmi -f id id ....          #删除多个镜像

docker rmi -f $(docker images -a) #删除全部镜像
```

## 5.容器命令

注：有了镜像才可以创建容器

新建容器并启动

```shell
docker run [可选参数] [镜像id或镜像名]
#参数说明
--name="新的镜像名"		 #创建容器名。用以区分容器
-d						#容器启动后后台运行
-it						#容器启动后已交互方式运行，进入容器查看内容
-p [主机端口：容器端口]	   #映射容器端口
-P						#映射随机端口
```

查看所有运行的容器

```shell
docker ps				#查看所有正在运行的容器
#参数说明
-a						#查看正在运行的容器和历史运行过的容器
-n=[数字]				   #查看最近创建的n个容器
-q						#只显示容器编号
```

其他

```shell
exit					#停止容器并退出
ctrl+p+q				#容器不停止退出
```

删除容器

```shell
docker rm [容器id]		       #删除指定容器，不能删除正在运行的容器，可跟多个容器id，删除多个容器
docker rm -f $(docker ps -aq)   #删除所有容器
docker ps -aq |xargs docker rm  #删除所有容器
```

启动和停止容器

```shell
docker start [容器id]		#启动容器
docker restart [容器id]	#重启容器
docker stop [容器id]		#停止容器
docker kill [容器id]		#强制停止当前容器
```

常用命令

```shell
docker logs								 #查看日志
#参数
-tf									 	 #显示日志
--tail [n]							 	 #显示n条日志
docker top [容器id]	   					#查看容器的进程信息
docker inspect [容器id]  					#查看镜像的元数据
docker exec -it [容器id] /bin/bash		#进入容器并开启一个新的终端
docker attach [容器id]					#进入容器正在执行的终端
docker cp [容器id]:[容器文件目录] [主机目录]  #从容器中拷贝文件到主机
docker stats 							 #查看cpu状态
```

注：

1. 使用docker run -d [镜像名或id] 启动镜像后使用docker ps 命令发现并无此镜像，这个镜像已经停止运行

   #docker容器使用后台运行进必须有一个前台进程，docker发现没有应用就会自动停止运行

2. ```shell
   docker run -it --rm [容器名或id]      #表示启动容器后删除容器，即用完即删，主要用于测试
   ```

3. portainer docker图形化界面管理工具

# 5.Docker镜像

## 1.镜像是什么

镜像是一种轻量、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行软件所需的所有内容，包括代码、运行库、环境变量和配置文件等，通常有以下获取方式：

1.从远程库下载

2.朋友拷贝

3.自己制作  （Dockerfile）                                                                                         

##  2.Docker镜像加载原理

1. unionfs(联合文件系统)：一种分层的、轻量级的高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。uniosfs文件系统是docker镜像的基础，镜像可以通过分层来继承，基于基础镜像，可以制作各种具体的镜像。

2. docker镜像加载原理。docker镜像实际上是由一层一层的文件系统组成的，这种层级文件就是unionfs，包括bootfs和rootfs
3. 镜像的下载都是分层下载的

## 3.commit镜像

```shell
docker commit		#提交镜像，使其成为一个新的镜像
docker commit -m="新镜像描述信息" -a="作者名" [镜像id] [目标镜像名]:[tag(版本)]	#完整命令
```

# 6.容器数据卷

