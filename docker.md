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

## 1.概念

1. 需求：使数据持久化，容器数据不以容器的删除而丢失
2. 卷技术：将docker容器中产生的数据同步到本地这就是卷技术
3. 方法：目录挂载，将容器内的目录挂载到linux（主机）中
4. 容器的持久化和同步操作！容器间也是可以数据共享的

## 2.使用数据卷

```shell
-v										#使用-v命令挂载
docker run -it -v [主机目录]:[容器内目录]   #挂载容器目录
docker inspect [容器id]				   #查看其中的mount项看目录挂载是否成功
docker volume							#查看所有的volume卷情况
```

## 3.具名和匿名挂载

1. 匿名挂载：在挂载时只指定了容器内的路径未指定容器外路径

2. 具名挂载：-v [卷名]:[容器内路径]

```shell
# 匿名挂载
-v 容器内路径
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的volume的情况
➜  ~ docker volume ls    
DRIVER              VOLUME NAME
local               33ae588fae6d34f511a769948f0d3d123c9d45c442ac7728cb85599c2657e50       
# 这里发现，这种就是匿名挂载，我们在 -v只写了容器内的路径，没有写容器外的路径

# 具名挂载
➜  ~ docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
➜  ~ docker volume ls                  
DRIVER              VOLUME NAME
local               juming-nginx

# 通过 -v 卷名：容器内路径
# 查看一下这个卷
```

3. 所有的docker容器内的卷，没有指定挂载目录的情况下都是在/var/lib/docker/volumes下，如果指定了目录，docker volume ls 是查看不到的。
4. 一般情况下我们都是具名挂载

```shell
-v [容器内路径]				#匿名挂载
-v [卷名]:[容器内路径]		   #具名挂载
-v [主机目录]:[容器内目录]	  #指定路径挂载docker volume ls 是查看不到的
```

## 4.拓展

```shell
# 通过 -v 容器内路径： ro rw 改变读写权限
ro #readonly 只读
rw #readwrite 可读可写
docker run -d -P --name nginx05 -v juming:/etc/nginx:ro nginx
docker run -d -P --name nginx05 -v juming:/etc/nginx:rw nginx
# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作！
```

## 7.数据卷容器

数据卷容器指多个容器之间进行数据同步

```shell
docker run -it --name [新容器名] --volumes-from [父容器名] [镜像名]
```

注：容器之间的配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的！

# 7.Dockerfile

## 1.Dockerfile介绍

dockerfile是用来构建docker镜像的文件！

构建步骤：

1. 编写dockerfile文件
2. 使用docker build构建一个镜像
3. 使用docker run运行镜像
4. 使用docker pull发布镜像 

很多官方镜像都是基础包，很多功能是没有的，因此通常我们需要自己构建镜像

## 2.Dockerfilre构建过程

### 1.基础知识

1. 每个保留关键字(指令)必须使用大写字母
2. 执行是从上到下顺序执行
3. #表示注释
4. 每个指令都会创建提交一个新的镜像层并提交

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200704150706618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODIyMzQ1,size_16,color_FFFFFF,t_70)

### 2.Dockerfile指令

```shell
常用指令
FROM			#基础镜像，一切从这里开始构建，如：FROM centos
MAINTAINER		#镜像是书写的，姓名+邮箱
RUN				#镜像构建时需要运行的命令
ADD				#步骤，如tomcat镜像，这个tomcat压缩包！添加内容 添加同目录
WORKDIR			#镜像工作目录
VOLUME			#挂载目录
EXPOST			#保留端口配置（暴露端口）
CMD				#指定容器启动时运行的命令，只有最后一个命令生效
ENTRYPOTNT		#指定容器启动时运行的命令，可追加命令
ONBUILD			#当构建一个被继承 DockerFile 这个时候就会运行ONBUILD的指令，触发指令。
COPY			#类似ADD，将文件拷贝到镜像中
ENV				#构建时设置环境变量
docker history [镜像id]		#列出镜像变更历史
```

## 3.实战

### 1.创建一个自己的centos

```shell
# 1.编写Dockerfile文件
vim mydockerfile-centos
FROM centos
MAINTAINER cheng<1204598429@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-----end----"
CMD /bin/bash
```

```shell
# 2、通过这个文件构建镜像
# 命令 docker build -f [文件名] -t [镜像名]:[tag] .
docker build -f mydockerfile-centos -t mycentos:0.1 .
```

### 2.Tomcat镜像

1. 准备镜像文件

准备tomcat 和 jdk压缩包，上传到linux中，编写好README文件(帮助文档，可不写) 。

2. 编写dockerfile文件，官方命名` Dockerfile`,使用官方命名在在build时可不用指定-f [文件名]，它会自动寻找

```shell 
FROM centos
MAINTAINER cheng<1204598429@qq.com>
COPY README /usr/local/README                    #复制文件
ADD jdk-8u231-linux-x64.tar.gz /usr/local/       #复制镜像并解压
ADD apache-tomcat-9.0.35.tar.gz /usr/local/      #复制镜像并解压
RUN yum -y install vim
ENV MYPATH /usr/local                             #设置环境变量
WORKDIR $MYPATH                                   #设置工作目录
ENV JAVA_HOME /usr/local/jdk1.8.0_231             #设置环境变量
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.35 #设置环境变量
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib  #设置环境变量 分隔符是：
EXPOSE 8080                                       #设置暴露的端口
CMD /usr/local/apache-tomcat-9.0.35/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.35/logs/catalina.out                          # 设置默认命令

```

3. 构建镜像

```shell
# 因为dockerfile命名使用默认命名 因此不用使用-f 指定文件
$ docker build -t mytomcat:0.1 .
```

4. run镜像

```shell
$ docker run -d -p 8080:8080 --name tomcat01 -v /home/kuangshen/build/tomcat/test:/usr/local/apache-tomcat-9.0.35/webapps/test -v /home/kuangshen/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.35/logs mytomcat:0.1
```

5. 访问测试

6. 发布项目(由于做了卷挂载，我们直接在本地编写项目就可以发布了！)

## 4.发布镜像

### 1.发布镜像到dockerhub

1. 注册并登录dockerhub官网
2. 在服务器上提交自己的镜像

```shell
#登录
docker login -p [dockerhub官网账号密码] -u [dockerhub官网账号用户名]
#提交镜像
docker push [作者名]/[要提交的镜像名]:[版本号]
```

### 2.发布镜像到阿里云上

1. 登录阿里云
2. 找到容器镜像服务
3. 创建命名空间
4. 创建容器镜像（镜像仓库）
5. 上传镜像，参考阿里云官方文档很详细https://cr.console.aliyun.com/repository/

## 5.小结
https://img-blog.csdnimg.cn/20200704150831579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODIyMzQ1,size_16,color_FFFFFF,t_70
