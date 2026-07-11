---
title: DevOps 教程
date: 2026-01-09 14:09:17
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/preview.jpg
tags:
  - 云原生与运维
  - DevOps
categories: 云原生与运维
---



# 简介

## 什么是 DevOps



DevOps，即 Development 和 Operations 的组合词，是一组过程、方法与系统的统称。它主要用于促进开发（应用程序 / 软件工程）、技术运营和质量保障（QA）部门之间的沟通、协作与整合

DevOps 是一种重视“软件开发人员（Dev）”和“IT 运维技术人员（Ops）”之间沟通合作的文化，旨在促进原本隔离的角色（如开发、IT 运维、质量工程和安全）之间的协调和协作。通过自动化“软件交付”和“架构更改”的过程，DevOps 可以更快、更频繁、更可靠地构建、测试和发布软件。它采用自动化的开发过程或工作流，旨在优化软件的快速交付

简而言之，DevOps 可以定义为开发和 IT 运营的一致性，以及更好的沟通和协作。它有助于提高组织提供应用程序和服务的速度，使组织能够更好地为客户服务，并在市场中更有竞争力

然而，实施 DevOps 也面临一些挑战，如投资过多工具、过于依赖现有工具集、对测试关注不足以及将安全视为事后工作等。因此，在引入 DevOps 时，需要综合考虑各种因素，以确保其成功实施
                

------

## DevOps 管理生命软件周期



DevOps 的方式可以让公司能够更快地应对更新和市场发展变化，开发可以快速交付，部署也更加稳定

核心就在于简化 Dev和 Ops 团队之间的流程，使整体软件开发过程更快速。整体的软件开发流程包括：

* PLAN：开发团队根据客户的目标制定开发计划
* CODE：根据 PLAN 开始编码过程，需要将不同版本的代码存储在一个库中
* BUILD：编码完成后，需要将代码构建并且运行
* TEST：成功构建项目后，需要测试代码是否存在 BUG 或错误
* DEPLOY：代码经过手动测试和自动化测试后，认定代码已经准备好部署并且交给运维团队
* OPERATE：运维团队将代码部署到生产环境中
* MONITOR：项目部署上线后，需要持续的监控产品
* INTEGRATE：然后将监控阶段收到的反馈发送回 PLAN 阶段，整体反复的流程就是 DevOps 的核心，即持续集成、持续部署

<br>

为了保证整体流程可以高效的完成，各个阶段都有比较常见的工具，如下图：

![image-20240306234821844](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240306234821844.png)



------

# Code 阶段工具

## 安装 Git



傻瓜式安装：https://git-scm.com/



------

## 安装 GitLab



1. 搜索和拉取镜像

   ```SH
   docker search gitlab
   docker pull gitlab/gitlab-ce:latest
   ```

2. 启动容器

   ```SH
   docker run \ -itd \ -p port:80 \ -p port:22 \ -v /home/gitlab/etc:/etc/gitlab \ -v /home/gitlab/log:/var/log/gitlab \ -v /home/gitlab/opt:/var/opt/gitlab \ --restart always \ --privileged=true \ --name gitlab \ gitlab/gitlab-ce
   ```

3. 进入容器

   ```SH
   docker exec -it gitlab /bin/bash 
   ```

4. 修改配置文件

   ```SH
   # 进入容器
   docker exec -it gitlab /bin/bash 
   
   # 进入文件
   vi /etc/gitlab/gitlab.rb
   ```

   ```SH
   # 修改的第一个内容，gitlab访问地址，填写域名或IP地址，如果端口不写的话默认为80端口
   external_url 'http://xxx.xxx.xxx.xx'
   
   # 修改 ssh主机ip
   gitlab_rails['gitlab_ssh_host'] = 'http://xxx.xxx.xxx.xx'
   
   # 修改ssh连接端口
   gitlab_rails['gitlab_shell_ssh_port'] = xxx
   ```

5. 让配置生效

   ```SH
   gitlab-ctl reconfigure
   ```

6. 修改http配置

   由于基于 Docker 端口映射，需要修改 gitlab 的映射端口与 Docker 服务启动时的重定向端口一致：

   ```SH
   docker exec -it gitlab /bin/bash
   vi /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
   ```

   ![image-20240307235503433](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240307235503433.png)

   ![image-20240307235512123](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240307235512123.png)

7. 重启配置

   ```SH
   # 让配置生效
   gitlab-ctl reconfigure
   
   # 重启容器
   gitlab-ctl restart
   
   # 退出容器
   exit
   ```

8. 输入网址启动 https:xxx.xxx.xxx.xx:xxxx 初次访问使用 root 登录，并需要修改密码 

   ![image-20240307235640547](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240307235640547.png)

   ```SH
   # 进入容器
   docker exec -it gitlab /bin/bash
   
   # 进入控制台
   gitlab-rails console -e production
   
   # 修改密码
   user = User.where(id:1).first
   user.password='your password'
   user.save
   exit
   ```




------

# Build 阶段工具

## 安装 JDK



Build 阶段需要使用 Maven 进行打包，而安装 Maven 则需要先安装 JDK

在 Linux 系统上安装 JDK 有多种方法，这里介绍使用 .tar.gz 格式的安装包进行安装的通用步骤：

1. 下载 JDK 安装包。访问 Oracle 官方网站，根据需要选择合适的 JDK 版本（例如，Java 8）并下载对应的 .tar.gz 格式安装包

2. 上传安装包并解压。使用 FTP 或其他文件传输工具将下载的安装包上传到 Linux 服务器。然后使用 tar 命令解压安装包

   ```SH
   tar -zxvf jdk-8u161-linux-x64.tar.gz -C /usr/local/java/
   ```

   这里假设将 JDK 安装到 /usr/local/java 目录

3. 配置环境变量。编辑 /etc/profile 文件，添加或修改 JAVA_HOME 和 PATH 环境变量

   ```SH
   export JAVA_HOME=/usr/local/java/jdk1.8.0_161
   export PATH=$JAVA_HOME/bin:$PATH
   ```

4. 使环境变量生效。运行 source /etc/profile 命令，使刚刚设置的 JDK 环境变量立即生效

5. 验证安装。运行 java -version 命令，检查是否安装成功。如果看到 JDK 的版本信息，说明安装成功



------

## 安装 Maven



1. 前往 Maven 的官方网站（https://maven.apache.org/）下载 Maven 二进制文件

2. 创建一个目录来存放 Maven 文件，将下载的 Maven 二进制文件上传到刚刚创建的目录中

3. 解压 Maven 二进制文件。进入 /usr/local/maven 目录，然后使用 tar -zxvf apache-maven-x.x.x-bin.tar.gz 命令（其中 x.x.x 是 Maven 的版本号）进行解压

4. 配置环境变量。编辑 /etc/profile 文件，添加 Maven 的安装路径到环境变量中

   ```SH
   export M2_HOME=/usr/local/maven/apache-maven-x.x.x
   export PATH=$PATH:$M2_HOME/bin
   ```

5. 保存并关闭文件，然后在终端运行 source /etc/profile 命令，使新的环境变量设置生效

6. 验证 Maven 是否安装成功。在终端中输入 mvn -v 命令，如果显示 Maven 的版本信息和其他相关配置，说明 Maven 已经成功安装并配置好了

7. 打开 Maven 的安装目录，进入 conf 文件夹，找到 settings.xml 文件并打开它，在 settings.xml 文件中，找到 mirrors 标签，在 mirrors 标签下添加阿里云镜像仓库的配置。配置内容大致如下：

   ```xml
   <mirror>  
     <id>aliyunmaven</id>  
     <mirrorOf>*</mirrorOf>  
     <name>aliyun public repository</name>  
     <url>https://maven.aliyun.com/repository/public</url>  
   </mirror>
   ```

8. 保存并关闭 settings.xml 文件。现在 Maven 在下载依赖时，将会从阿里云镜像仓库中获取



------

# Operate 阶段工具

## 安装 Docker



1. 安装 Docker 之前，需要安装一些必要的依赖包

   ```SH
   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. 设置 Docker 镜像源，这有助于加速 Docker 镜像的下载

   ```SH
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. 安装 Docker：

   ```SH
   sudo yum install docker-ce docker-ce-cli containerd.io
   ```

4. 启动 Docker 并设置为开机自启：

   ```SH
   sudo systemctl start docker  
   sudo systemctl enable docker
   ```

5. 为了验证 Docker 是否成功安装并运行，您可以执行以下命令来查看 Docker 版本信息：

   ```SH
   docker version
   ```

   如果一切正常，您将看到 Docker 的版本号和相关信息



------

## 安装 Docker-Compose



1. 运行以下命令以下载 Docker Compose 的当前稳定版本：

   ```SH
   sudo curl -L "https://github.com/docker/compose/releases/download/版本号/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

   注意将上述命令中的“版本号”替换为您希望安装的 Docker Compose 的实际版本号。例如，如果您想安装 1.28.2 版本，那么命令应该类似于：

   ```SH
   sudo curl -L "https://github.com/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. 下载完成后，您需要为 docker-compose 文件添加执行权限：

   ```SH
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. 通过运行以下命令来验证安装：

   ```SH
   docker-compose --version
   ```

   如果一切正常，将看到 Docker Compose 的版本号



------

# Integrate 阶段工具

## Jenkins 介绍



Jenkins 是一个用 Java 编写的开源持续集成（CI）工具，起源于 Hudson（Hudson 是商用的）。它提供了一个友好的操作界面，使开发者能够更专注于业务逻辑的实现，而不是陷入繁杂的集成工作中。Jenkins 在持续集成领域有着主导地位，被广泛应用于各种规模和技术的团队与项目中，包括但不限于.NET、Ruby、Groovy、Grails、PHP 以及 Java 等

Jenkins 的主要功能包括自动化构建、测试和部署应用，可以实时监控集成中存在的错误，并提供详细的日志文件和提醒功能。此外，它还能够以图表的形式形象地展示项目构建的趋势和稳定性。通过 Jenkins，用户可以定时拉取代码并编译，进行静态代码分析，定时打包发布测试版，甚至进行自定义操作，如运行单元测试等

Jenkins 的特点在于其易用性和良好的扩展性。其用户界面直观且吸引人，学习曲线平滑，使得用户能够轻松上手。同时，Jenkins 拥有大量的开源插件，涵盖系统版本控制、构建工具、代码质量度量、构建通知、外部系统集成、用户界面定制化等多个方面，使得 Jenkins 能够灵活地适应各种需求。这些插件的安装通常都非常快捷和简单

更多关于 Jenkins 的详细介绍和使用方法，可以访问其官方网站 [https://jenkins.io/](https://jenkins.io/)



------

## 安装 Jenkins



1. 启动 Docker 并下载 Jenkins 镜像文件

   ```SH
   docker pull jenkins/jenkins
   ```

2. 创建 Jenkins 挂载目录并授权权限

   ```SH
   mkdir -p /var/jenkins_mount  
   chmod 777 /var/jenkins_mount
   ```

3. 使用 docker run 命令来创建并启动 Jenkins 容器。以下是一个示例命令：

   ```SH
   docker run -d \  
     --name myjenkins \  
     -p 8080:8080 \  
     -p 50000:50000 \  
     -v /var/jenkins_mount:/var/jenkins_home \  
     jenkins/jenkins:lts
   ```

   这个命令做了以下几件事情：

   * -d：在后台运行容器
   * --name myjenkins：给容器命名为 myjenkins
   * -p 8080:8080 和 -p 50000:50000：将容器的 8080 和 50000 端口映射到宿主机的相应端口上
   * -v /var/jenkins_mount:/var/jenkins_home：将宿主机的 /var/jenkins_mount 目录挂载到容器的 /var/jenkins_home 目录，用于持久化存储 Jenkins 的配置和数据
   * jenkins/jenkins:lts：使用 Jenkins 的长期支持（LTS）版本镜像

4. 启动完成后，通过访问 localhost:8080 来访问 Jenkins。如果是远程服务器，则使用服务器的 IP 地址或域名替换 localhost。首次访问时，Jenkins 会要求解锁并设置管理员密码，这通常会在 Jenkins 容器的启动日志中提供

<br>

首次登录时会需要安装一些默认的 Jenkins 插件，通常会有很多插件安装失败，这是正常的

![image-20240310235637958](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240310235637958.png)

可以点击继续，然后点击 Dashboard 的 Manage Jenkins 的 Manage Plugins，重新下载所需插件。如果仍旧安装失败，可以把 Jenkins 的镜像源改为国内的镜像源



------

## 配置 Jenkins



为 Jenkins 配置 Maven 和 JDK，你需要确保 Jenkins 能够访问到这些工具的安装路径，并在构建过程中使用它们

<br>

**配置 JDK**

1. 登录 Jenkins，进入 Manage Jenkins > Global Tool Configuration，在 JDK installations 部分，点击 Add JDK 按钮
2. 选择 Provide own JDK 选项，在 Name 字段中输入 JDK 的名称，比如 JDK11
3. 在 JAVA_HOME 字段中输入 JDK 的安装路径，比如 /usr/lib/jvm/java-11-openjdk-amd64
4. 保存配置

<br>

**配置 Maven**

1. 进入 Jenkins 的 Global Tool Configuration 页面，向下滚动到 Maven installations 部分，点击 Add Maven 按钮
2. 选择 Install automatically 选项让 Jenkins 自动下载和安装 Maven，或者选择 Provide own Maven 选项并提供 Maven 的安装路径，如果选择 Provide own Maven，请在 Name 字段中输入 Maven 的名称，比如 Maven3.8.1
3. 在 MAVEN_HOME 字段中输入 Maven 的安装路径，比如 /usr/local/maven，保存配置

<br>

**在 Jenkins Job 中使用 JDK 和 Maven**

1. 创建或编辑 Jenkins Job：进入 Jenkins 的 Job 配置页面
2. 配置 JDK：在 Build Environment 部分，勾选 Inject environment variables to the build process 选项，并在 JDK 字段中选择你之前配置的 JDK
3. 配置 Maven：在 Build 部分，选择 Invoke top-level Maven targets 并在 Maven Version 字段中选择你之前配置的 Maven
4. 保存并构建：保存 Job 配置，并触发构建。Jenkins 将会使用你配置的 JDK 和 Maven 来执行构建过程



------

## 实现基础 CI 操作



**准备项目代码**

在实现基础 CI 操作之前，需要先准备一个可运行的 Java Demo 项目，并将它推到 Git 仓库中

<br>

**创建 Jenkins 任务并配置代码地址**

1. 进入 Jenkins Dashboard，选择 New Item，为项目流程取名，选择 Freestyle Project

   ![image-20240314221047741](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240314221047741.png)

   ![image-20240314221132036](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240314221132036.png)

2. 下移到 Source Code Management 部分，选择方式为 Git，如果项目并输入项目 URL，如果项目权限为 private 则还需要输入用户名及密码

   ![image-20240314221518281](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240314221518281.png)

3. 配置完成之后，可以尝试执行任务，如果成功的话则会讲 GIt 仓库的代码拉取到 Jenkins 本地

<br>

**将拉取下来的项目代码使用 Maven 打包**

1. 跳转到 Source Code Management 下方 Build 部分，选择方式为 Maven

   ![image-20240314221812258](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240314221812258.png)

2. 点击完成后选择配置的 Maven 并输入打包命令

   ![image-20240314221906027](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240314221906027.png)

3. 完成这一步之后再次尝试执行任务，成功的话 Jenkins 会将拉取下来的项目代码打成 jar 包

<br>

**将 jar 包推送到目标服务器**

1. 在 Post-build Actions 部分，选择 Send build，输入需要推送的文件地址

   ![image-20240314222500415](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240314222500415.png)

2. 完成这一步之后再次尝试执行任务，成功的话可以在目标服务器上看到打成的 jar 包

<br>

**使用 Docker 镜像的方式启动 Project**

1. 上传上去的 jar 包可以直接使用 Java -jar 命令执行，但是如果想使用 Docker 镜像的模式的话，则需要对项目进行一些改造

2. 在项目根目录下创建一个 docker/Dockerfile 文件，添加如下内容

   ```dockerfile
   # 选择对应的 JDK 镜像版本
   FROM daocloud.io/library/java:8u40-jdk
   COPY mytest.jar /usr/local
   WORKDIR /usr/local
   CMD java -jar mytest.jar
   ```

3. 再创建一个 docker/docker-compose.yml 文件

   ```yml
   version: '3.1'
   services:
     mytest:
       build:
         context: ./
         dockerfile: Dockerfile
       image: mytest:v1.0.0
       container_name: mytest
       ports: 
         - 8081:8080
   ```

4. 将添加好的文件推送到 Git 仓库

5. 继续修改 Post-build Actions，添加 source files

   ![image-20240317214447338](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240317214447338.png)

6. 在 Exec command 部分添加后续操作指令

   ```sh
   # 进入工作目录
   cd /usr/local/test/docker
   # 将 jar 包复制过来
   mv ../target/*.jar ./
   # 重启 docker compose
   docker-compose down
   docker-compose up -d --build
   # 删除之前打包的镜像
   docker iamge prune -f
   ```

   

------

## 实现基础 CD 操作



在实际开发过程中，可能会需要给同一个 Git 分支根据提交的版本不同打上不同的 tag，然后随时切换 tag 进行部署，这就需要用到参数化部署

<br>

1. 跳转到 General 部分，选择参数化部署

   ![image-20240317221359455](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240317221359455.png)

2. 添加内容，name 随便写，不重复即可，parameter type 选择 tag

   ![image-20240317221503093](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240317221503093.png)

3. 在 Maven 打包时，需要先切换到对应的 Tag 节点，在 Build 部分添加 Excute Shell 操作

   ![image-20240317222032769](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240317222032769.png)

4. 通过手动拖拽的方式将 Execute Shell 拖到最上方，并添加 checkout 操作

   ![image-20240317222228901](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240317222228901.png)

5. 进入 Git 仓库，将代码分支打上不同的 tag

6. 进入 Dashboard，选择 Build with param，看到可以选择不同的 Tag 进行部署了

   ![image-20240317222511745](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240317222511745.png)



------

# 集成 SonarQube

## SonarQube 简介



SonarQube 是一个用于代码质量管理的开源平台，专门用于管理源代码的质量。通过 SonarQube，开发人员能够检测、跟踪并解决代码中的潜在缺陷、糟糕的复杂度分布、缺乏单元测试等各种问题

该平台提供了对大量编程语言的支持，包括但不限于 Java、C#、C/C++、PL/SQL、Cobol、JavaScript 和 Groovy 等。通过插件形式，SonarQube 可以对这些编程语言的代码进行静态分析，从而发现代码中的质量问题。此外，SonarQube 还提供了对国际化以及报告文档化的支持

SonarQube 可以从多个维度对代码质量进行检测和分析，包括代码量、安全隐患、编写规范隐患、重复度、复杂度、代码增量以及测试覆盖率等。这些分析结果可以通过 Web 界面管理平台展示出来，方便开发人员进行代码的优化和规范编写

SonarQube 可以与代码编辑器、持续集成 / 持续部署（CI/CD）平台以及源代码管理系统（SCM）进行完美集成。这种集成使得开发人员可以在编写代码的过程中就实时获取到代码质量反馈，并直接在平台上看到代码问题是由哪位开发人员提交的

SonarQube 是一个 C/S 架构的服务，包括 Web 服务器、搜索引擎（用于支持 UI 搜索）以及后台计算服务等组件。后台数据库则用于存储 SonarQube 实例的配置信息、项目以及视图的质量快照等



------

## 安装 SonarQube



1. 在需要安装的路径下创建 docker-compose.yml 文件，内容如下

   ```YML
   version: '3'  
     
   services:  
     sonarqube:
       # 安装最新版本
       image: sonarqube:latest  
       ports:  
         - "9000:9000"
       # 配置参数
       environment:  
         - SONARQUBE_JDBC_URL=jdbc:postgresql://postgres:5432/sonar  
         - SONARQUBE_JDBC_USERNAME=sonar  
         - SONARQUBE_JDBC_PASSWORD=sonar  
         - SONARQUBE_WEB_CONTEXT=/
       # 依赖于 postgres 之后再构建
       depends_on:  
         - postgres  
       # 数据卷配置
       volumes:  
         - sonarqube_data:/opt/sonarqube/data  
         - sonarqube_extensions:/opt/sonarqube/extensions  
         - sonarqube_logs:/opt/sonarqube/logs  
     
     postgres:  
       image: postgres:12  
       # 配置参数
       environment:  
         - POSTGRES_USER=sonar  
         - POSTGRES_PASSWORD=sonar  
         - POSTGRES_DB=sonar  
       # 数据卷配置
       volumes:  
         - postgresql_data:/var/lib/postgresql/data  
     
   volumes:  
     sonarqube_data:  
     sonarqube_extensions:  
     sonarqube_logs:  
     postgresql_data:
   ```

2. 执行启动命令

   ```YML
   docker-compose up -d
   ```

3. 一旦服务启动，您可以通过访问`http://<your_host_ip>:9000`来访问 SonarQube。使用 SonarQube 提供的默认管理员用户名和密码（通常是“admin”和“admin”）登录



------

## Jenkins 集成 SonarQube



**Jenkins 服务器安装 Sonar-Scanner**

1. 访问 Sonar Scanner 的官方下载页面或相应的版本发布页面，获取 Sonar Scanner 的安装包。通常，Sonar Scanner 提供 ZIP 格式的压缩包，可以直接下载

2. 将下载的 ZIP 压缩包解压到你选择的目录

3. 编辑系统的环境变量文件，如 /etc/profile，添加 Sonar Scanner 的路径。这样，你就可以在系统的任何位置直接调用 Sonar Scanner

   ```bash
   export SONAR_SCANNER_HOME=/usr/local/sonar-scanner  
   export PATH=$PATH:$SONAR_SCANNER_HOME/bin
   ```

4. 打开终端，输入 sonar-scanner -v 或 sonar-scanner --version，如果成功显示 Sonar Scanner 的版本信息，则表示安装成功

<br>

**Jenkins 安装 Sonar-Scanner 插件**

1. 在 Jenkins 的主界面上，点击左侧的 Manage Jenkins 链接，然后在下拉菜单中选择 Manage Plugins
2. 在插件管理页面的 Available 标签页中，输入 Sonar Scanner 或 SonarQube Scanner 进行搜索并安装
3. 如果安装过程中提示需要重启 Jenkins，那么请按照提示进行重启
4. 重启 Jenkins 后，你可以再次进入插件管理页面，在 Installed 标签页中检查 Sonar Scanner 插件是否已经成功安装

<br>

**在 Jenkins 中配置 SonarQube 服务器**

1. 在 Jenkins 主界面左侧，点击 Manage Jenkins 链接。然后，选择 Configure System 以进入全局配置页面
2. 在全局配置页面的底部，找到 SonarQube servers 部分。点击 Add SonarQube server 按钮来添加一个新的 SonarQube 服务器配置
3. 配置 Name、Sonar URL 以及 Token 验证信息
4. 完成 SonarQube 服务器的配置后，点击页面底部的 Save 按钮以保存更改

<br>

**为项目条件 SonarQube 扫描步骤**

1. 在 Build 部分，点击 Add build step 并选择 Execute SonarQube Scanner
2. 在 SonarQube Scanner 配置中，选择配置的 SonarQube 服务器
3. 指定项目的基本信息和其他必要的配置，如源代码路径、SonarQube 项目键等



------

# Harbor 镜像仓库

## Harbor 简介



Harbor 是由 VMware 公司中国团队为企业用户设计的 Registry server 开源项目，主要用于存储和分发 Docker 镜像。作为一个企业级私有 Registry 服务器，Harbor 通过添加安全、标识和管理等企业必需的功能特性，扩展了开源 Docker Distribution，为用户提供了更好的性能和安全，同时提升了用户使用 Registry 构建和运行环境传输镜像的效率

Harbor 的主要特点包括：

1. 安全的访问控制：支持基于用户、团队和角色的细粒度访问控制，有助于企业保护镜像的安全性
2. 镜像复制：支持镜像在多个 Registry 节点上的复制功能，提高了镜像的可用性和容错性，确保数据和知识产权在公司内部网络中得到有效管控
3. 可扩展性：能够轻松扩展到数百个节点，满足企业不断增长的 Docker 镜像管理需求
4. 多租户支持：支持多租户环境，帮助企业在不同的团队和部门之间隔离 Docker 镜像

此外，Harbor 还提供了审计管理功能，所有针对镜像仓库的操作都可以被记录追溯，用于审计管理。同时，Harbor 已拥有英文、中文、德文、日文和俄文的本地化版本，更多语言将会陆续添加



------

## 安装 Harbor



1. 关闭 iptables 和 firewalld 服务，并禁用 SELinux，以确保 Harbor 能够正常运行
2. 从 Harbor 的官网下载最新的安装包，并通过适当的方式（如 xftp）将其传送到目标虚拟机上，然后解压
3. 修改 harbor.yml 文件，设置正确的主机地址、端口号以及密码
4. 执行 . /prepare 脚本以准备 Harbor 安装所需的配置和依赖项，然后执行 . /install.sh 脚本来安装 Harbor
5. 执行 docker-compose up -d 命令来启动 Harbor 服务
6. 在浏览器中输入 Harbor 的 URL，即可访问 Harbor 的 Web 界面。使用管理员账号登录后，可以浏览、检索 Docker 镜像仓库，管理项目和命名空间



![image-20240322233651066](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240322233651066.png)



------

## 推送镜像到 Harbor



1. 首先，需要在本地计算机上构建 Docker 镜像。假设你有一个名为 myapp 的应用程序

2. 配置了 Docker 客户端，以便能够与 Harbor 仓库进行通信。这通常涉及到修改 Docker 的配置文件（如 /etc/docker/daemon.json），添加 Harbor 仓库的地址到 insecure-registries 列表中，并重启 Docker 服务

   ```JSOn
   {
   	"insecure-registries": "Harbor_URL:PORT"
   }
   ```

   

3. 在推送镜像之前，你需要登录到 Harbor 仓库。执行 docker login <harbor_registry> 命令，其中 <harbor_registry> 是你的 Harbor 仓库的地址。在提示时输入你的用户名和密码

4. 在推送之前，你需要给镜像打上标签，使其与 Harbor 仓库关联。执行 docker tag <image_id> <harbor_registry>/myproject/<image_name>:<image_version> 命令，其中 <image_id> 是你要推送的镜像的 ID，<harbor_registry> 是你的 Harbor 仓库的地址，myproject 是你在 Harbor 中创建的项目名称，<image_name> 是你要推送的镜像的名称，<image_version> 是镜像的标签

5. 最后，执行 docker push <harbor_registry>/myproject/<image_name>:<image_version> 命令来推送镜像到 Harbor 仓库



------

## Jenkins 容器内部使用 Docker



Jenkins 通常用于自动化构建、测试和部署应用程序。这些过程可能需要创建、运行和管理 Docker 容器。如果 Jenkins 容器能够直接使用宿主机的 Docker 守护进程，那么它可以无缝地访问和管理宿主机的 Docker 资源，无需在 Jenkins 容器内部再安装一个完整的 Docker 环境

<br>

1. 为了让 Jenkins 容器能够访问 Docker 守护进程，你需要设置 /var/run/docker.sock 的权限

   ```SH
   chown root:root /var/run/docker.sock  
   chmod o+rw /var/run/docker.sock
   ```

2. 修改 Jenkins 的 docker-compose.yaml 文件内容

   ```yaml
   version: "3.1"  
   services:  
     jenkins:  
       image: jenkins/jenkins  
       container_name: jenkins  
       ports:  
         - 8080:8080  
         - 50000:50000  
       volumes:  
         - ./data/:/var/jenkins_home/  
         # 添加以下内容
         - /var/run/docker.sock:/var/run/docker.sock  
         - /usr/bin/docker:/usr/bin/docker  
         - /etc/docker:/etc/docker  
   ```

3. 使用 Docker Compose 或直接使用 docker run 命令，重启 Jenkins 容器

4. 登录到 Jenkins 界面，并尝试执行 Docker 命令或构建使用 Docker 的任务，以验证配置是否正确



------

## 将镜像推送到 Harbor 仓库



在之前的 Integrate 阶段，是在 Jenkins 中制作镜像并推送到目标服务器，现在需要将镜像推送到 Harbor 仓库，然后由目标服务器拉取镜像

<br>

1. 制作镜像不需要 docker-compose.yaml 文件，可以删除项目源码中的 docker/docker-compose.yaml 文件

2. 删除之前的 SSH 操作

   ![image-20240324234821133](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240324234821133.png)

3. 在 SonarQube 之后增加构建步骤，添加 shell 命令

   ![image-20240324234922949](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240324234922949.png)

4. Shell 命令如下

   ```SH
   # 将生成的 jar 包移到 dockerfile 的同级目录下
   mv target/*.jar docker/
   # 构建镜像
   docker build -t mytest:$tag docker/
   # 登录 Harbonr
   docker login -u Harbon用户名 -p Harbon密码 Harbon服务器地址:端口
   # 打标签并推送到 Harbon 服务器
   docker tag mytest:$tag Harbon服务器地址:端口/项目名/mytest:$tag
   docker push Harbon服务器地址:端口/项目名/mytest:$tag
   ```

5. 完成后尝试执行任务，成功的话可以在 Harbon 仓库中看到生成的镜像



------

## 目标服务器准备文件



在项目部署的目标服务器中添加 deploy.sh 文件，具体内容如下：

```SH
# 接收传入的参数
harbor_addr=$1
harbor_repo=$2
project=$3
version=$4
port=$5

# 查询需要部署的容器是否正在运行，是的话则关闭并删除容器
imageName=$harbor_addr/$harbor_repo/$project:$version
containerId=`docker ps -a | grep ${project} | awk '{print $1}'`
if [ "$containerId" != "" ] ; then
  docker stop $containerId
  docker rm $containerId
fi

# 查询当前服务器中是否有需要运行的镜像，有的话则删除所有同名镜像，无论版本
tag=`docker images | grep ${project} | awk '{print $2}'`
if [[ "$tag" =~ "$version" ]] ; then
  docker rmi $imageName
fi

# 登录 Harbor
docker login -u admin -p Harbor12345 $harbor_addr

# 拉取目标镜像
docker pull $imageName

# 运行镜像
docker run -d -p $port:$port --name $project $imageName

# 任务完成打印 SUCESS
echo "SUCEES"
```



------

## Jenkins 执行目标服务器脚本文件



1. 为了让 deploy.sh 在任何环境下都可以执行，将其移入到环境变量目录 /usr/bin 下

2. 使用脚本文件部署，之前的 Shell 命令就可以去掉了，删除以下内容

   ![image-20240325231112411](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240325231112411.png)

3. 添加构建后步骤

   ![image-20240325231410058](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240325231410058.png)

4. 这次不用传任何的文件，只需配置执行脚本的命令即可

   ```sh
   deploy.sh Harbor_URL:Harbor_Port Harbor仓库名 ${JOB_NAME} $tag 8080
   ```

5. 配置完成后，尝试执行任务，成功的话可以看到项目已经在目标服务器上重新部署了



------

# Jenkins 流水线

## 简介



使用 Freestyle Project 构建的项目，所有的任务在一个步骤中完成的，如果构建失败，并不能直观的看出是哪一步出了错，为此，Jenkins 提供了 Pipeline 的一种项目构建方式

![image-20240325235411051](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240325235411051.png)

<br>

使用 Pipeline 方式构建的项目，没有了操作步骤，具体的操作方式都是通过流水线完成的

使用 Jenkins 提供的 Hello World 方式配置脚本

![image-20240325235609615](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240325235609615.png)

<br>

配置完成后保存并执行任务，可以看到 Jenkins 显示了操作步骤的名称以及消耗时间

![image-20240325235709863](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240325235709863.png)



------

## 语法简介



Jenkins 配置的简单语法如下

```jenkinsfile
// 所有的脚本命令都放在 pipeline 中
pipeline {

	// 可以指定任务在集群的哪台服务器上执行，any 表示不做要求
	agent any
	
	// 可以声明全局变量，便于后续使用
	environment {
		key = 'value'
	}
	
	// 执行步骤，可以配置多个
	stages {
		stage('拉取 git 仓库代码') {
			steps {
				echo '拉取代码'
			}
		}
		stage('SnoarQube 扫描') {
			steps {
				echo 'SnoarQube 扫描'
			}
		}
		stage('CI') {
			steps {
				echo 'CI'
			}
		}
		stage('CD') {
			steps {
				echo 'CD'
			}
		}
	}

}
```



jenkins 也提供了流水线语法的工具来帮助生成内容，比如说从 Git 仓库中拉取代码，选择需要生成的内容并添加参数就可以了

![image-20240326000542712](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326000542712.png)

将生成好的脚本替换脚本文件的对应内容就可以了



------

## 从 Git 拉取 Jenkinsfile



Jenkins 还支持将流水线文件放在 Git 仓库中，由 Jenkins 从 GIt 仓库中拉取，便于版本管理

![image-20240326233011658](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326233011658.png)



------

## Maven 构建项目



使用 Jenkins 的流水线语法工具生成 Maven 的报的命令

Maven 打包的命令本质就是执行 Shell 脚本

![image-20240326233353310](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326233353310.png)

生成后用其替换 Jenkinsfile 文件的对应步骤



------

## SonarQube 扫描



SonarQube 扫描的本质也是执行 Shell 脚本

![image-20240326233618548](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326233618548.png)



------

## 制作 Docker 镜像



![image-20240326233717014](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326233717014.png)



------

## 推送镜像到 Harbor



![image-20240326233925666](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326233925666.png)



------

## 通知目标服务器部署项目



由目标服务器执行脚本文件，需要使用 SSH Publisher

![image-20240326234136378](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240326234136378.png)



------

## 部署完成后使用钉钉通知



1. 在钉钉中创建机器人并获取 Webhook 地址
2. 登录 Jenkins，进入系统管理 -> 插件管理。在可选插件中搜索 DingTalk 插件，并安装。安装完成后，重启 Jenkins 以使插件生效
3. 进入 Jenkins 的系统管理页面，滑到页面底部，找到“钉钉”配置项。在钉钉配置项中，设置 Webhook 地址，这个地址是在钉钉中创建的机器人的地址。配置通知时机，比如构建成功、失败或不稳定时发送通知，可以选择是否勾选“详细日志”，勾选后会在详细日志中打印告警信息，保存配置
4. 进入 Jenkins 首页，找到目标流水线项目。进入项目的配置页面，在构建后操作或类似的选项中，添加钉钉通知步骤
5. 手动触发流水线项目的构建



------

# Kubernates

## 简介



Kubernetes，简称 K8s，是一个开源的容器编排系统，用于自动化部署、扩展和管理容器化应用程序。它的名称来源于希腊语，意为“舵手”或“飞行员”，象征着它在容器化应用部署和管理中的核心地位

K8s 的设计初衷是为了解决传统应用部署方式的局限性。传统的应用部署通常依赖于特定的操作系统和配置，这限制了应用的可移植性和可扩展性。而 K8s 通过容器化技术，将应用及其依赖项打包成独立的容器，使得应用可以在不同的云环境和操作系统上无缝运行

K8s 的核心功能包括服务发现和负载均衡、自动部署和回滚、资源管理、自动扩展和收缩以及自我修复等。它可以将多个容器组合成一个服务，并通过内置的负载均衡器将请求分发到容器。同时，K8s 可以自动部署容器镜像，并在出现问题时进行回滚，确保应用的稳定运行。此外，它还可以根据容器的需求动态分配资源，如 CPU 和内存，并根据应用程序的负载自动扩展和收缩容器集群

K8s 的另一个重要特点是其强大的自我修复能力。当节点故障或容器出现问题时，K8s 可以自动检测和修复这些问题，确保应用的持续可用性



------

## 使用 kubeadm 安装 kubernetes



https://www.kuboard.cn/install/history-k8s/install-k8s-1.19.x.html



------

## 在 Kubernates 中安装 Kuboard



https://www.kuboard.cn/install/v3/install-in-k8s.html



------

## 配置 Harbor 镜像仓库



Kubernetes 需要从 Harbor 中拉取 Docker 镜像并部署，因此也需要配置 Harbor 仓库的信息，首先在修改 Docker 的配置文件（如 /etc/docker/daemon.json ），添加 Harbor 的地址作为不安全的注册表

```JSON
{
	"insecure-registries": "Harbor_URL:PORT"
}
```



然后配置 Harbor 仓库的地址及账户信息，这一步可以在 Kuboard 上完成

![image-20240330231759775](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/DevOps/DevOps%20%E6%95%99%E7%A8%8B/image-20240330231759775.png)



------

## 编写 yml 文件



```yml
apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: test-deployment  
spec:  
  replicas: 3 # 根据需要调整副本数  
  selector:  
    matchLabels:  
      app: test  
  template:  
    metadata:  
      labels:  
        app: test  
    spec:  
      containers:  
      - name: test-container  
        image: my-docker-image # 替换为您的 Docker 镜像名称  
        ports:  
        - containerPort: 80 # 根据您的应用实际端口进行调整

---

apiVersion: v1  
kind: Service  
metadata:  
  name: test  
spec:  
  selector:  
    app: test # 对应 Deployment 中的 label  
  ports:  
    - protocol: TCP  
      port: 80 # Service 的端口  
      targetPort: 80 # 容器内应用监听的端口，与 Deployment 中定义的一致  
  type: ClusterIP # 如果您希望服务在集群内部可用  
  # 如果您希望服务可以从集群外部访问，可以设置为 NodePort 或 LoadBalancer
  
---

apiVersion: networking.k8s.io/v1  
kind: Ingress  
metadata:  
  name: test-ingress  
  annotations:  
    nginx.ingress.kubernetes.io/rewrite-target: /  
spec:  
  rules:  
  - http:  
      paths:  
      - path: /  
        pathType: Prefix  
        backend:  
          service:  
            name: test  
            port:  
              number: 80
```

<br>

执行如下命令，成功的话则会在 Kubernates 中将 Deployment、Service 和 Ingress 都启动起来

```bash
kubectl apply -f test.yml  
```



------

## 上传 yml 文件并同事 K8s 执行



将编写好的 yml 文件放到 Git 仓库中



1. 安装 Git 和 Kubernetes 插件
2. 在 Jenkins 中配置 Git 仓库的 URL 和认证信息
3. 使用 Kubernetes 插件配置 Jenkins 与 Kubernetes 集群的连接
4. 在 Git 仓库中创建一个 Jenkinsfile，用于定义构建和部署流程
5. 触发 Jenkins 构建

<br>

```groovy
pipeline {  
    agent any  
  
    stages {  
        stage('Clone Git Repository') {  
            steps {  
                git branch: 'master', url: 'YOUR_GIT_REPOSITORY_URL'  
            }  
        }  
        stage('Deploy to Kubernetes') {  
            steps {  
                // 假设你的 YAML 文件位于仓库的 'k8s' 目录下，文件名为 'deployment.yml'  
                script {  
                    def yamlFilePath = 'k8s/deployment.yml'  
                    def kubernetes = new KubernetesClientBuilder()  
                        .withMasterUrl('YOUR_KUBERNETES_MASTER_URL')  
                        .withCredentials('YOUR_KUBERNETES_CREDENTIALS')  
                        .build()  
                      
                    // 使用 Jenkins 的 'kubectl' 命令或者 Kubernetes 客户端库来部署 YAML 文件  
                    // 这里是一个示例，你可能需要根据你的具体需求进行调整  
                    sh "kubectl apply -f ${yamlFilePath}"  
                    // 或者使用 Kubernetes 客户端库（需要额外的依赖和配置）  
                    // kubernetes.load(new File(yamlFilePath)).create()  
                }  
            }  
        }  
    }  
}
```



------

## 自动化 CI



如果使用的是 GitHub 或 GitLab 等支持 Webhooks 的 Git 托管服务，可以配置它们来在 master 分支发生变动时通知  Jenkins 执行构建

1. 在 Git 托管服务上配置 Webhook：登录到 Git 托管服务（如 GitHub 或 GitLab），找到你的仓库设置，然后配置 Webhook。将 Jenkins 提供的回调 URL 添加到 Webhook 配置中，并确保选择了正确的触发事件（通常是 push 事件）
2. 在 Jenkins 中安装并配置相关插件：确保 Jenkins 安装了 Git 托管服务相对应的插件（如 GitHub 或 GitLab 插件）。这些插件通常提供了额外的配置选项来支持 Webhook 触发构建
3. 在 Jenkins 项目中启用 Webhook 触发：在 Jenkins 项目配置中，找到构建触发器部分，并启用 Git 托管服务相对应的 Webhook 触发选项
4. 测试 Webhook：在 Git 托管服务的 Webhook 配置页面上，通常会有一个测试或发送测试事件的按钮。点击这个按钮来测试 Webhook 是否能够成功触发 Jenkins 构建
