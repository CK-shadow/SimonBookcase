---
title: Docker 实战
date: 2023-12-16 18:47:40
tags: 云原生
categories: 
  - 云原生
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F/Docker%20%E5%AE%9E%E6%88%98/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F/Docker%20%E5%AE%9E%E6%88%98/preview.jpg
---

# Docker 实战

## 简介

### 为什么会有 Docker



之前在服务器配置一个应用的运行环境，要安装各种软件，Java/RabbitMQ/MySQL/JDBC 驱动包等。安装和配置这些东西有多麻烦就不说了，它还不能跨平台。假如我们是在 Windows 上安装的这些环境，到了 Linux 又得重新装。况且就算不跨操作系统，换另一台同样操作系统的服务器，要移植应用也是非常麻烦的

传统上认为，软件编码开发/测试结束后，所产出的成果即是程序或是能够编译执行的二进制字节码等( java 为例)。而为了让这些程序可以顺利执行，开发团队也得准备完整的部署文件，让运维团队得以部署应用程式，开发需要清楚的告诉运维部署团队，用的全部配置文件 + 所有软件环境。不过，即便如此，仍然常常发生部署失败的状况。Docker 的出现使得 Docker 得以打破过去「程序即应用」的观念。透过镜像(images)将作业系统核心除外，运作应用程式所需要的系统环境，由下而上打包，达到应用程式跨平台间的无缝接轨运作



Docker 是基于 Go 语言实现的云开源项目。Docker 的主要目标是 “Build，Ship and Run Any App,Anywhere”，也就是通过对应用组件的封装、分发、部署、运行等生命周期的管理，使用户的 APP（可以是一个 WEB 应用或数据库应用等等）及其运行环境能够做到“一次镜像，处处运行”

Linux 容器技术的出现就解决了这样一个问题，而 Docker 就是在它的基础上发展过来的。将应用打成镜像，通过镜像成为运行在 Docker 容器上面的实例，而  Docker 容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。只需要一次配置好环境，换到别的机子上就可以一键部署好，大大简化了操作



简单来说，Docker 解决了运行环境和配置不同的问题，方便做持续集成并有助于整体发布的容器虚拟化技术



------

### 容器和虚拟机



虚拟机（virtual machine）就是带环境安装的一种解决方案

它可以在一种操作系统里面运行另一种操作系统，比如在 Windows10 系统里面运行 Linux 系统 CentOS7。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变

缺点在于资源占用多、冗余步骤多、启动慢



由于虚拟机存在某些缺点，Linux 发展出了另一种虚拟化技术：Linux 容器 (Linux Containers，缩写为 LXC)

Linux 容器是与系统其他部分隔离开的一系列进程，从另一个镜像运行，并由该镜像提供支持进程所需的全部文件。容器提供的镜像包含了应用的所有依赖项，因而在从开发到测试再到生产的整个过程中，它都具有可移植性和一致性

Linux 容器不是模拟一个完整的操作系统而是对进程进行隔离。有了容器，就可以将软件运行所需的所有资源打包到一个隔离的容器中。容器与虚拟机不同，不需要捆绑一整套操作系统，只需要软件工作所需的库资源和设置。系统因此而变得高效轻量并保证部署在任何环境中的软件都能始终如一地运行



比较了 Docker 和传统虚拟化方式的不同之处：

传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；

容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源



------

### 基本组成



* 镜像 Image

  Docker 镜像（Image）就是一个只读的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。相当于容器的“源代码”，docker 镜像文件类似于 Java 的类模板，而 docker 容器实例类似于 java 中 new 出来的实例对象

* 容器 Container

  Docker 利用容器（Container）独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环境，容器是用镜像创建的运行实例。就像是 Java 中的类和实例对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器为镜像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台

* 仓库

  仓库（Repository）是集中存放镜像文件的场所。仓库分为公开仓库（Public）和私有仓库（Private）两种形式。最大的公开仓库是 Docker Hub



------

## Docker 常用命令

### 帮助启动类命令



```sh
# 启动 docker
systemctl starter docker

# 停止 Docker
systemctl stop docker

# 重启 docker
systemctl restart docker

# 查看 docker 状态
systemctl status docker

# 开机启动
systemctl enable docker

# 查看 docker 概要信息
docker info

# 查看 docker 总体帮助文档
docker --help

# 查看 docker 命令帮助文档
docker 具体命令 --help
```



------

### 镜像命令



```sh
# 列出所有镜像
docker images

# 搜索镜像库中的镜像
docker search 镜像名称

# 拉取镜像，可添加 tag 指定版本号，不添加默认 latest
docker pull 镜像名称:[tag]

# 查看镜像/容器/数据卷所占的空间
docker system df

# 删除镜像
docker rmi -f 镜像id
```



------

### 容器命令



```sh
# 新建/启动容器
docker run [OPTIONS] IMAGE [COMMAND][ARGS...] 

# 列出当前正在运行的容器
docker ps

# 退出容器，容器会停止
exit
# 退出容器，容器不会停止
ctrl + p + q

# 启动已经停止的容器
docker start 容器id或容器名

# 重启容器
docker restart 容器id或容器名

# 停止容器
docker stop 容器id或容器名

# 强制停止容器
docker kill 容器id或容器名

# 删除已经停止的容器
docker rm 容器id或容器名
```



------

## Docker 镜像

### 简介



镜像是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境 (包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是 image 镜像文件



------

### 镜像分层



UnionFS（联合文件系统）：Union 文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下 (unite several directories into a single virtual filesystem)。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录



docker 的镜像实际上由一层一层的文件系统组成，这种层级的文件系统 UnionFS

bootfs(boot file system) 主要包含 bootloader 和 kernel, bootloader 主要是引导加载 kernel, Linux 刚启动时会加载 bootfs 文件系统，在 Docker 镜像的最底层是引导文件系统 bootfs。这一层与我们典型的 Linux/Unix 系统是一样的，包含 boot 加载器和内核。当 boot 加载完成之后整个内核就都在内存中了，此时内存的使用权已由 bootfs 转交给内核，此时系统也会卸载 bootfs

rootfs (root file system) ，在 bootfs 之上。包含的就是典型 Linux 系统中的 /dev, /proc, /bin, /etc 等标准目录和文件。rootfs 就是各种不同的操作系统发行版，比如 Ubuntu，Centos 等等

对于一个精简的 OS，rootfs 可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用 Host 的 kernel，自己只需要提供 rootfs 就行了。由此可见对于不同的 linux 发行版, bootfs 基本是一致的, rootfs 会有差别, 因此不同的发行版可以公用 bootfs



镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了复用

比如说有多个镜像都从相同的 base 镜像构建而来，那么 Docker Host 只需在磁盘上保存一份 base 镜像，同时内存中也只需加载一份 base 镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享

当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作“容器层”，“容器层”之下的都叫“镜像层”
所有对容器的改动 - 无论添加、删除、还是修改文件都只会发生在容器层中。只有容器层是可写的，容器层下面的所有镜像层都是只读的



------

### Docker 镜像发布到私有库



将 Docker 镜像发布到私有库有 docker 命令和 dockerfile 两种方式，这里先将使用 docker 命令，以阿里云镜像私有库为例



1. 下载镜像并允许 Docker Registry

   ```sh
   docker pull registry
   docker run -d -p 5000:5000  -v /simon/myregistry/:/tmp/registry --privileged=true registry
   ```

2. 将镜像修改为符合规范的 tag

   ```sh
   docker tag 镜像:Tag Host:Port/Repository:Tag
   ```

3. 推送到私有库

   ```sh
   docker push host:port/镜像:Tag
   ```



------

## Docker 容器数据卷

### 简介



Docker 将运用与运行的环境打包形成容器运行， Docker 容器产生的数据，如果不通过 docker commit 生成新的镜像，使得数据做为镜像的一部分保存下来， 那么当容器删除后，数据自然也就没有了。 为了能保存数据在 Docker 中我们使用卷

卷就是目录或文件，存在于一个或多个容器中，由 Docker 挂载到容器，但卷不属于联合文件系统（Union FileSystem），因此能够绕过联合文件系统提供一些用于持续存储或共享数据的特性


卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此 Docker 不会在容器删除时删除其挂载的数据卷

Docker 容器卷的工作就是将 docker 容器数据通过映射进行备份 + 持久化到本地的主机目录



------

### 使用方式



1. 简单使用

   ```
   docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名
   ```

2. 可以使用容器 2 继承容器 1 的数据卷策略

   ```
   docker run -it -volumes-from 容器1 --nama 容器2 镜像名
   ```




------

## Dockerfile

### 简介



Dockerfile 是用来构建 Docker 镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本



Docker 执行 Dockerfile 的大致流程为：

1. docker 从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似 docker commit 的操作提交一个新的镜像层
4. docker 再基于刚提交的镜像运行一个容器
5. 执行 dockerfile 中的下一条指令直至所有指令都执行完成



Dockerfile 注意事项：

1. 每条指令都必须为大写字母且后面要跟随至少一个参数
2. 指令按照从上到小顺序执行
3. #表示注释
4. 每条指令都会创建一个新的镜像层并对镜像进行提交



------

### Dockerfile 常用保留字指令



* FROM

  基础镜像，指定一条已经存在的镜像作为基础模板，第一条指令必须为 FROM

* MAINTAINER

  镜像维护者的姓名和邮箱地址

* RUN

  容器构建时需要运行的指令，后面可跟随 shell 和 exec 两种格式的脚本，RUN 是在 docker build 时运行的

* EXPOSE

  当前容器对外暴露出的端口

* WORKDIR

  指定创建容器后，从终端进来的初始路径

* USER

  以何种用户执行，不指定的话默认为 root

* ENV

  用来设置环境变量

* ADD

  将宿主机目录下的文件拷贝进镜像且会自动处理 url 和解压 tar 压缩包

* COPY

  将宿主机目录下的文件拷贝进镜像

* VOLUME

  用来指定容器数据卷

* CMD

  指定容器启动后需要干的事，Dockerfile 中可以有多条 CMD 指令，但只有最后一条会生效

* ENTRYPOINT

  与 CMD 类似，但 ENTRYPOINT 不会被 docker run 后面的指令覆盖



------

### 案例



比如我们想要构建一个 centos7 镜像，要求包含 vim、ifconfig 和 jdk8



1. 创建一个 Dockerfile 文件，具体内容如下

   ```dockerfile
   FROM centos
   MAINTAINER simon<simon@163.com>
    
   ENV MYPATH /usr/local
   WORKDIR $MYPATH
    
   #安装vim编辑器
   RUN yum -y install vim
   #安装ifconfig命令查看网络IP
   RUN yum -y install net-tools
   #安装java8及lib库
   RUN yum -y install glibc.i686
   RUN mkdir /usr/local/java
   #ADD 是相对路径jar,把jdk-8u171-linux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置
   ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/
   #配置java环境变量
   ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
   ENV JRE_HOME $JAVA_HOME/jre
   ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
   ENV PATH $JAVA_HOME/bin:$PATH
    
   EXPOSE 80
    
   CMD echo $MYPATH
   CMD echo "success--------------ok"
   CMD /bin/bash
   ```

2. 执行 build 命令

   ```sh
   # docker build -t 镜像名字:TAG .
   docker build -t centosjava8:1.5 .
   ```

3. 运行构建好的镜像

   ```
   docker run -it centosjava8:1.5
   ```



------

### 虚悬镜像



虚悬镜像指的是仓库名和标签都是 none 的镜像，俗称 dangling image

比如通过以下 Dockerfile 就可构建一个虚悬镜像

```dockerfile
from ubuntu
CMD echo 'action is success'
```



可以通过以下命令操作虚悬镜像

```
# 查询所有虚悬镜像
docker image ls -f dangling=true
# 删除所有虚悬镜像
docker image prune
```



------

## 容器编排

### 简介



Compose 是 Docker 公司推出的一个工具软件，可以管理多个 Docker 容器组成一个应用。你需要定义一个 YAML 格式的配置文件 docker-compose.yml，协调多个容器之间的调用关系。然后，只要一个命令，就能同时启动/关闭这些容器



 docker 建议我们每一个容器中只运行一个服务, 因为 docker 容器本身占用资源极少, 所以最好是将每个服务单独的分割开来但是这样我们又面临了一个问题。如果我需要同时部署好多个服务, 难道要每个服务单独写 Dockerfile 然后在构建镜像, 构建容器, 这样累都累死了, 所以 docker 官方给我们提供了 docker-compose 多服务部署的工具

 例如要实现一个 Web 微服务项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库 mysql 服务容器，redis 服务器，注册中心 eureka，甚至还包括负载均衡容器等等。。。。。。

 Compose 允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）

 可以很容易地用一个配置文件定义一个多容器的应用，然后使用一条指令安装这个应用的所有依赖，完成构建。Docker-Compose 解决了容器与容器之间如何管理编排的问题



------

### 下载安装



Docker Compose 与 Docker 的版本需要对应起来，以下是 Docker 官方提供的对应关系

https://docs.docker.com/compose/compose-file/compose-file-v3/

以下是在 Linux 上安装 Docker Compose 的方式

https://docs.docker.com/compose/install/linux/#install-the-plugin-manually



------

### 常用命令



```docker
# 查看帮助
docker-compose -h

# 启动所有docker-compose服务
docker-compose up                           

# 启动所有docker-compose服务并后台运行
docker-compose up -d                        

# 停止并删除容器、网络、卷、镜像
docker-compose down                         

# 进入容器实例内部  docker-compose exec docker-compose.yml文件中写的服务id 
docker-compose exec  yml里面的服务id /bin/bash

# 展示当前docker-compose编排过的运行的所有容器
docker-compose ps                      

# 展示当前docker-compose编排过的容器进程
docker-compose top   

 # 查看容器输出日志
docker-compose logs  yml里面的服务id    

# 检查配置
docker-compose config     

# 检查配置，有问题才有输出
docker-compose config -q  

# 重启服务
docker-compose restart   

# 启动服务
docker-compose start     

# 停止服务
docker-compose stop      
```



------

### 简单示例



此案例展示一个 SpringBoot 项目通过 Docker compose 启动，依赖的数据库有 Redis 和 MySQL



1. 新建一个 SpringBoot 项目，需要连接 Redis 和 MySQL，打成 jar 包并上传到 Linux 服务器上

2. 在当前目录下添加一个 Dockerfile 文件，内容如下

   ```dockerfile
   # 基础镜像使用java
   FROM java:8
   
   # 作者
   MAINTAINER Simon
   
   # VOLUME 指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp
   VOLUME /tmp
   
   # 将jar包添加到容器中并更名为zzyy_docker.jar
   ADD docker_test-0.0.1-SNAPSHOT.jar simon_docker.jar
   
   # 运行jar包
   RUN bash -c 'touch /simon_docker.jar'
   ENTRYPOINT ["java","-jar","/simon_docker.jar"]
   
   #暴露8082端口作为微服务
   EXPOSE 8082
   ```

3. 执行命令构建镜像

   ```sh
   docker build -t simon_docker:1.0
   ```

4. 在当前目录下添加 docker-compose.yml 文件

   ```yml
   # docker compose 版本
   version: "3"
   # 需要启动的容器
   services:
     # 项目镜像
     microService:
       # 镜像名称及 tag
       image: simon:1.0
       # 容器名称
       container_name: ms01
       # 容器端口和对外映射的端口
       ports:
         - "8082:8082"
       # 容器数据卷
       volumes:
         - /app/microService:/data
   	# 使用自定义网络
       networks: 
         - simon_test 
       # 需要依赖的容器
       depends_on: 
         - redis
         - mysql
     
     # 需要启动的 Redis
     redis:
   	# redis 镜像版本
       image: redis:6.0.8
   	# 容器端口和对外映射的端口
       ports:
         - "6379:6379"
   	# 容器数据卷
       volumes:
         - /app/redis/redis.conf:/etc/redis/redis.conf
         - /app/redis/data:/data
   	# 使用自定义网络
       networks: 
         - simon_test
   	# 启动命令
       command: redis-server /etc/redis/redis.conf
   
    
     # 需要启动的 MySQL
     mysql:
   	# MySQL 镜像版本
       image: mysql:5.7
       # 设置启动参数配置
       environment:
         MYSQL_ROOT_PASSWORD: '123456'
         MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
         MYSQL_DATABASE: 'db_test'
         MYSQL_USER: 'simon'
         MYSQL_PASSWORD: 'simon'
   	# 容器端口和对外映射的端口
       ports:
          - "3306:3306"
       # 容器数据卷
       volumes:
          - /app/mysql/db:/var/lib/mysql
          - /app/mysql/conf/my.cnf:/etc/my.cnf
          - /app/mysql/init:/docker-entrypoint-initdb.d
       # 使用自定义网络
       networks:
         - simon_test
   	# 启动命令
       command: --default-authentication-plugin=mysql_native_password #解决外部无法访问
   
   # 设置自定义网络名
   networks: 
      simon_test: 
   ```

5. 启动项目

   ```
   docker-compose up
   # 或
   docker-compose up -d
   ```

6. 添加 MySQL 表结构即可
