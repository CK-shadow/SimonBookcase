---
title: K8S 从入门到实践
date: 2024-12-24 22:51:03
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/preview.jpg
tags:
  - 云原生与运维
  - K8S
categories: 云原生与运维
---



# 简介

## 概述



kubernetes，简称 K8s，是用 8 代替 8 个字符“ubernete”而成的缩写。是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes 的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes 提供了应用部署，规划，更新，维护的一种机制

传统的应用部署方式是通过插件或脚本来安装应用。这样做的缺点是应用的运行、配置、管理、所有生存周期将与当前操作系统绑定，这样做并不利于应用的升级更新 / 回滚等操作，当然也可以通过创建虚拟机的方式来实现某些功能，但是虚拟机非常重，并不利于可移植性

新的方式是通过部署容器方式实现，每个容器之间互相隔离，每个容器有自己的文件系统，容器之间进程不会相互影响，能区分计算资源。相对于虚拟机，容器能快速部署，因为容器与底层设施、机器文件系统是解耦的

<br>

Kubernetes 是一个轻便的和可扩展的开源平台，用于管理容器化应用和服务。通过 Kubernetes 能够进行应用的自动化部署和扩缩容。在 Kubernetes 中，会将组成应用的容器组合成一个逻辑单元以更易管理和发现

Kubernetes 积累了作为 Google 生产环境运行工作负载 15 年的经验，并吸收了来自于社区的最佳想法和实践



------

## 特性与功能



* 自动装箱

  基于容器对应用运行环境的资源配置要求自动部署应用容器

* 自我修复

  当容器失败时，会对容器进行重启

  当所部署的 Node 节点有问题时，会对容器进行重新部署和重新调度

  当容器未通过监控检查时，会关闭此容器直到容器正常运行时，才会对外提供服务

* 水平扩展

  通过简单的命令、用户UI 界面或基于 CPU 等资源使用情况，对应用容器进行规模扩大或规模剪裁

* 服务发现

  用户不需使用额外的服务发现机制，就能够基于 Kubernetes 自身能力实现服务发现和负载均衡

* 滚动更新

  可以根据应用的变化，对应用容器运行的应用，进行一次性或批量式更新

* 版本回退

  可以根据应用部署情况，对应用容器运行的应用，进行历史版本即时回退

* 密钥与配置管理

  在不需要重新构建镜像的情况下，可以部署和更新密钥和应用配置，类似热部署

* 存储编排

  自动实现存储系统挂载及应用，特别对有状态应用实现数据持久化非常重要

  存储系统可以来自于本地目录、网络存储(NFS、Gluster、Ceph 等)、公共云存储服务

* 批处理

  提供一次性任务，定时任务；满足批量数据处理和分析的场景



------

## 架构简介



![image-20231024232533562](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231024232533562.png)



k8s 集群控制节点，对集群进行调度管理，接受集群外用户去集群操作请求

* master：主控节点
  * API Server：集群统一入口，以 restful 风格进行操作，同时交给 etcd 存储，提供认证、授权、访问控制、API注册和发现等机制
  * scheduler：节点的调度，选择 node 节点应用部署
  * controller-manager：处理集群中常规后台任务，一个资源对应一个控制器
  * etcd：存储系统，用于保存集群中的相关数据
* Work node：工作节点
  * Kubelet：master 派到 node 节点代表，管理本机容器 
  * kube-proxy：提供网络代理，负载均衡等操作
* 容器运行环境【Container Runtime】
  * 容器运行环境是负责运行容器的软件
  * Kubernetes 支持多个容器运行环境：Docker、containerd、cri-o、rktlet 以及任何实现 Kubernetes CRI (容器运行环境接口) 的软件
* fluentd：是一个守护进程，它有助于提升集群层面日志



------

## 核心概念



* Pod

  Pod 是 K8s 中最小的单元

  一组容器的集合

  共享网络【一个 Pod 中的所有容器共享同一网络】

  生命周期是短暂的（服务器重启后，就找不到了）

* Volume

  声明在 Pod 容器中可访问的文件目录

  可以被挂载到 Pod 中一个或多个容器指定路径下

  支持多种后端存储抽象【本地存储、分布式存储、云存储】

* Controller

  确保预期的 pod 副本数量【ReplicaSet】

  无状态应用部署【Deployment】，无状态就是指，不需要依赖于网络或者 ip

  有状态应用部署【StatefulSet】，有状态需要特定的条件

  确保所有的 node 运行同一个 pod 【DaemonSet】

  一次性任务和定时任务【Job 和 CronJob】

* Deployment

  定义一组 Pod 副本数目，版本等

  通过控制器【Controller】维持 Pod 数目【自动回复失败的 Pod】

  通过控制器以指定的策略控制版本【滚动升级、回滚等】

* Service

  定义一组 pod 的访问规则

  Pod 的负载均衡，提供一个或多个 Pod 的稳定访问地址

  支持多种方式【ClusterIP、NodePort、LoadBalancer】

  可以用来组合 pod，同时对外提供服务

* Namespace

  一个集群内部的逻辑隔离机制【鉴权、资源】

  每个资源都属于一个 namespace

  同一个 namespace 所有资源不能重复

  不同 namespace 可以资源名重复

* API

  我们通过 Kubernetes 的 API 来操作整个集群

  同时我们可以通过 kubectl 、ui、curl 最终发送 http + json/yaml 方式的请求给 API Server，然后控制整个 K8S 集群，K8S 中所有的资源对象都可以采用 yaml 或 json 格式的文件定义或描述

  如下：使用 yaml 部署一个 nginx 的 pod

  ![image-20231026235221519](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231026235221519.png)



------

## 完整流程



![image-20231026235350994](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231026235350994.png)



1. 通过 Kubectl 提交一个创建 RC（Replication Controller）的请求，该请求通过 APlserver 写入 etcd
2. 此时 Controller Manager 通过 API Server 的监听资源变化的接口监听到此 RC 事件
3. 分析之后，发现当前集群中还没有它所对应的 Pod 实例
4. 于是根据 RC 里的 Pod 模板定义一个生成 Pod 对象，通过 APIServer 写入 etcd
5. 此事件被 Scheduler 发现，它立即执行执行一个复杂的调度流程，为这个新的 Pod 选定一个落户的 Node，然后通过 API Server 讲这一结果写入 etcd 中
6. 目标 Node 上运行的 Kubelet 进程通过 APiserver 监测到这个 " 新生的 Pod. 并按照它的定义，启动该 Pod 并任劳任怨地负责它的下半生，直到 Pod 的生命结束
7. 随后，我们通过 Kubectl 提交一个新的映射到该 Pod 的 Service 的创建请求
8. ControllerManager 通过 Label 标签查询到关联的 Pod 实例，然后生成 Service 的 Endpoints 信息，并通过 APIServer 写入到 etod 中
9. 接下来，所有 Node 上运行的 Proxy 进程通过 APIServer 查询并监听 Service 对象与其对应的 Endponts 信息，建立一个软件方式的负载均衡器来实现 Service 访问到后端 Pod 的流量转发功能



------

## 安装方式



目前生产部署 Kubernetes 集群主要有两种方式

* kubeadm

  kubeadm 是一个 K8S 部署工具，提供 kubeadm init 和 kubeadm join，用于快速部署 Kubernetes 集群

  [官网地址](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

* 二进制包

  从 github 下载发行版的二进制包，手动部署每个组件，组成 Kubernetes 集群

  Kubeadm 降低部署门槛，但屏蔽了很多细节，遇到问题很难排查。如果想更容易可控，推荐使用二进制包部署 Kubernetes 集群，虽然手动部署麻烦点，期间可以学习很多工作原理，也利于后期维护



------

## 命令行管理工具 kubectl



kubectl 是 Kubernetes 集群的命令行工具，通过 kubectl 能够对集群本身进行管理，并能够在集群上进行容器化应用的安装和部署

命令格式如下：

```
kubectl [command] [type] [name] [flags]
```

* command

  指定要对资源执行的操作，例如 create、get、describe、delete

* type

  指定资源类型，资源类型是大小写敏感的，开发者能够以单数 、复数和缩略的形式

* name

  指定资源的名称，名称也是大小写敏感的，如果省略名称，则会显示所有的资源

* flags

  指定可选的参数，例如，可用 -s 或者 -server 参数指定 Kubernetes API server 的地址和端口

<br>

* 基础命令

  |  命令   |                      介绍                      |
  | :-----: | :--------------------------------------------: |
  | create  |          通过文件名或标准输入创建资源          |
  | expose  |        将一个资源公开为一个新的Service         |
  |   run   |           在集群中运行一个特定的镜像           |
  |   set   |             在对象上设置特定的功能             |
  |   get   |               显示一个或多个资源               |
  | explain |                  文档参考资料                  |
  |  edit   |          使用默认的编辑器编辑一个资源          |
  | delete  | 通过文件名，标准输入，资源名称或标签来删除资源 |

* 部署命令

  |      命令      |                          介绍                          |
  | :------------: | :----------------------------------------------------: |
  |    rollout     |                     管理资源的发布                     |
  | rolling-update |               对给定的复制控制器滚动更新               |
  |     scale      | 扩容或缩容 Pod 数量，Deployment、ReplicaSet、RC 或 Job |
  |   autoscale    |       创建一个自动选择扩容或缩容并设置 Pod 数量        |

* 集群管理命令

  |     命令     |              介绍              |
  | :----------: | :----------------------------: |
  | certificate  |          修改证书资源          |
  | cluster-info |          显示集群信息          |
  |     top      |        显示资源(CPU/M)         |
  |    cordon    |        标记节点不可调度        |
  |   uncordon   |        标记节点可被调度        |
  |    drain     | 驱逐节点上的应用，准备下线维护 |
  |    taint     |      修改节点 taint 标记       |

* 故障和调试命令

  | 命令         | 介绍                                                         |
  | ------------ | ------------------------------------------------------------ |
  | describe     | 显示特定资源或资源组的详细信息                               |
  | logs         | 在一个 Pod 中打印一个容器日志，如果 Pod 只有一个容器，容器名称是可选的 |
  | attach       | 附加到一个运行的容器                                         |
  | port-forward | 转发一个或多个                                               |
  | proxy        | 运行一个 proxy 到 Kubernetes API Server                      |
  | exec         | 执行命令到容器                                               |
  | cp           | 拷贝文件或目录到容器中                                       |
  | auth         | 检查授权                                                     |

* 其它命令

  | 命令         | 介绍                                                   |
  | ------------ | ------------------------------------------------------ |
  | apply        | 通过文件名或标准输入对资源应用配置                     |
  | patch        | 使用补丁修改、更新资源的字段                           |
  | replace      | 通过文件名或标准输入替换一个资源                       |
  | convert      | 不同的 API 版本之间转换配置文件                        |
  | label        | 更新资源上的标签                                       |
  | annotate     | 更新资源上的注释                                       |
  | completion   | 用于实现 kubectl 工具自动补全                          |
  | api-versions | 打印受支持的 API 版本                                  |
  | config       | 修改 kubeconfig 文件（用于访问 API，比如配置认证信息） |
  | help         | 所有命令帮助                                           |
  | plugin       | 运行一个命令行插件                                     |
  | version      | 打印客户端和服务版本信息                               |

  



示例

```sh
# 创建一个nginx镜像
kubectl create deployment nginx --image=nginx

# 对外暴露端口
kubectl expose deployment nginx --port=80 --type=NodePort

# 查看资源
kubectl get pod, svc
```



------

## YAML 文件详解



k8s 集群中对资源管理和资源对象编排部署都可以通过声明样式（YAML）文件来解决，也就是可以把需要对资源对象操作编辑到 YAML 格式文件中，我们把这种文件叫做资源清单文件，通过 kubectl 命令直接使用资源清单文件就可以实现对大量的资源对象进行编排部署了。一般在我们开发的时候，都是通过配置 YAML 文件来部署集群的

<br>

比如，我们可以通过如下 yaml 文件来打包部署一个 nginx

```yaml
apiVersion: apps/v1 # 版本号，pod 资源
kind: Deployment # 类型，控制器
metadata:   ## 数据标签
  name: nginx-deployment
  labels:       # 子标签
    app: nginx   # 业务容器
spec:
  replicas: 3     # 副本集
  selector:     # 选择器
    matchLabels:    # 匹配标签
      app: nginx    # 对应上面的业务容器
  template:     # 模板
    metadata:
      labels:
        app: nginx
    spec:
      containers:    # 容器
      - name: nginx  # 对应上面的业务容器
        image: nginx:1.15.4   # 使用的镜像信息
        ports:
          - containerPort: 80   # 容器端口信息
---
apiVersion: v1     # 版本号
kind: Service    # 服务类型
metadata:
  name: nginx-service
  labels:
    app: nginx
spec:
  type: NodePort   # 端口映射
  ports:
    - port: 80   # 内部端口
      targetPort:   # 映射端口
  selector:   # 选择器
    app: nginx   # 选择业务进行发布
```

创建文件后，执行以下命令便可启动一个 nginx

```yaml
# 测试 yaml 文件命令的正确性
kubectl create -f nginx-deploy.yaml --dry-run
# 执行创建
kubectl create -f nginx-deploy.yaml
```



一般来说，我们很少自己手写 YAML 文件，因为这里面涉及到了很多内容，我们一般都会借助工具来创建

```yaml
# 尝试运行,并不会真正的创建镜像
kubectl create deployment web --image=nginx -o yaml --dry-run
# 或者可以输出到一个文件中
kubectl create deployment web --image=nginx -o yaml --dry-run > hello.yaml
```

然后我们就在文件中直接修改即可



------

# Pod

## 概述



Pod 是 K8S 系统中可以创建和管理的最小单元，是资源对象模型中由用户创建或部署的最小资源对象模型，也是在 K8S 上运行容器化应用的资源对象，其它的资源对象都是用来支撑或者扩展 Pod 对象功能的，比如控制器对象是用来管控 Pod 对象的，Service 或者 Ingress 资源对象是用来暴露 Pod 引用对象的，PersistentVolume 资源对象是用来为 Pod 提供存储等等，K8S 不会直接处理容器，而是 Pod，Pod 是由一个或多个 container 组成

Pod 是 Kubernetes 的最重要概念，每一个 Pod 都有一个特殊的被称为 “根容器”的 Pause 容器。Pause 容器对应的镜像属于 Kubernetes 平台的一部分，除了 Pause 容器，每个 Pod 还包含一个或多个紧密相关的用户业务容器

![image-20231029222434254](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231029222434254.png)





Pod 存在的意义

* 创建容器使用 docker，一个 docker 对应一个容器，一个容器运行一个应用进程
* Pod 是多进程设计，运用多个应用程序，也就是一个 Pod 里面有多个容器，而一个容器里面运行一个应用程序
* Pod 的存在是为了亲密性应用 

<br>

Pod 是在 K8S 集群中运行部署应用或服务的最小单元，它是可以支持多容器的。Pod 的设计理念是支持多个容器在一个 Pod 中共享网络地址和文件系统，可以通过进程间通信和文件共享这种简单高效的方式组合完成服务。同时 Pod 对多容器的支持是 K8S 中最基础的设计理念。在生产环境中，通常是由不同的团队各自开发构建自己的容器镜像，在部署的时候组合成一个微服务对外提供服务

Pod 是 K8S 集群中所有业务类型的基础，可以把 Pod 看作运行在 K8S 集群上的小机器人，不同类型的业务就需要不同类型的小机器人去执行。目前 K8S 的业务主要可以分为以下几种

* 长期伺服型：long-running
* 批处理型：batch
* 节点后台支撑型：node-daemon
* 有状态应用型：stateful application

上述的几种类型，分别对应的小机器人控制器为：Deployment、Job、DaemonSet 和 StatefulSet 



------

## 实现机制



Pod 的实现机制主要是共享网络和共享存储

容器本身之间相互隔离的，一般是通过 namespace 和 group 进行隔离，如果想要共享网络，容器就需要在一个 namespace 中。通过 Pause 容器，把其它业务容器加入到 Pause 容器里，让所有业务容器在同一个名称空间中，可以实现网络共享

Pod 持久化数据，专门存储到某个地方中，使用 Volumn 数据卷进行共享存储



------

## 镜像拉取策略



![image-20231030231122304](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231030231122304.png)



拉取策略主要分为了以下三种：

* IfNotPresent：默认值，镜像在宿主机上不存在才拉取
* Always：每次创建 Pod 都会重新拉取一次镜像
* Never：Pod 永远不会主动拉取这个镜像



------

## 资源限制



![image-20231030231237050](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231030231237050.png)



这里分了两个部分

* request：表示调度所需的资源
* limits：表示最大所占用的资源



------

## 重启机制



因为 Pod 中包含了很多个容器，假设某个容器出现问题了，那么就会触发 Pod 重启机制

![image-20231030231403024](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231030231403024.png)



重启策略主要分为以下三种

* Always：当容器终止退出后，总是重启容器，默认策略 【nginx 等，需要不断提供服务】
* OnFailure：当容器异常退出（退出状态码非 0）时，才重启容器
* Never：当容器终止退出，从不重启容器 【批量任务】



------

## 健康检查



有的时候，程序可能出现了 Java 堆内存溢出，程序还在运行，但是不能对外提供服务了，这个时候就不能通过 容器检查来判断服务是否可用了，这个时候就可以使用应用层面的检查

```
# 存活检查，如果检查失败，将杀死容器，根据Pod的restartPolicy【重启策略】来操作
livenessProbe

# 就绪检查，如果检查失败，Kubernetes会把Pod从Service endpoints中剔除
readinessProbe
```



![image-20231030231944080](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231030231944080.png)



Probe 支持以下三种检查方式

* http Get：发送 HTTP 请求，返回 200 - 400 范围状态码为成功
* exec：执行 Shell 命令返回状态码是 0 为成功
* tcpSocket：发起 TCP Socket 建立成功



------

## 创建流程



![image-20231031000026157](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031000026157.png)



1. 调用 Master 节点的 API Server，把创建信息存储在 etcd 中
2. Scheduler 监控到 API Server 中有新的创建任务，会通过调度算法，把 pod 调度某个 worker node 上
3. 在 worker node 节点，会通过 kubelet 调用 apiserver 读取 etcd 拿到分配在当前 node 节点上的 pod 信息，然后通过 docker 创建容器
4. 创建完成后，kubelet 再调用 API Server 更新 pod 状态，并更新到 etcd 之中



------

## 影响 Pod 调度的属性



* 根据 Request 找到足够的 Node 节点进行调度

  ![image-20231031222322238](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031222322238.png)

* 节点选择器标签

  ![image-20231031222347435](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031222347435.png)

  关于节点选择器，其实就是有两个环境，然后环境之间所用的资源配置不同

  ![image-20231031222359382](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031222359382.png)

  我们可以通过以下命令，给我们的节点新增标签，然后节点选择器就会进行调度了

  ```
  kubectl label node node1 env_role=prod
  ```

* 节点亲和性

  节点亲和性 nodeAffinity 和 之前 nodeSelector 基本一样的，根据节点上标签约束来决定 Pod 调度到哪些节点上

  硬亲和性：约束条件必须满足

  软亲和性：尝试满足，不保证

  ![image-20231031222509703](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031222509703.png)

  支持常用操作符：in、NotIn、Exists、Gt、Lt、DoesNotExists

  反亲和性：就是和亲和性刚刚相反，如 NotIn、DoesNotExists 等



------

## 污点



Taint 污点：节点不做普通分配调度，是节点属性



* 查看污点情况

  ```
  kubectl describe node k8smaster | grep Taint
  ```

  ![image-20231031223637834](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031223637834.png)

  污点值有三个：

  * NoSchedule：一定不被调度
  * PreferNoSchedule：尽量不被调度【也有被调度的几率】
  * NoExecute：不会调度，并且还会驱逐Node已有Pod

* 删除污点

  ```
  kubectl taint node k8snode1 env_role:NoSchedule-
  ```

* 添加污点

  ```
  kubectl taint node [node] key=value:污点的三个值
  ```

  

------

# Controller

## 简介



Controller 是在集群上管理和运行容器的对象，Controller 是实际存在的，Pod 是虚拟机的

Pod 是通过 Controller 实现应用的运维，比如弹性伸缩，滚动升级等

Pod 和 Controller 之间是通过 label 标签来建立关系，同时 Controller 又被称为控制器工作负载

![image-20231031234515801](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031234515801.png)



------

## Deployment 控制器



* Deployment 控制器可以部署无状态应用
* 管理 Pod 和 ReplicaSet
* 部署，滚动升级等功能
* 应用场景：web 服务，微服务

<br>

Deployment 表示用户对 K8S 集群的一次更新操作。Deployment 是一个比 RS(Replica Set, RS) 应用模型更广的 API 对象，可以是创建一个新的服务，更新一个新的服务，也可以是滚动升级一个服务。滚动升级一个服务，实际是创建一个新的 RS，然后逐渐将新 RS 中副本数增加到理想状态，将旧 RS 中的副本数减少到 0 的复合操作

这样一个复合操作用一个 RS 是不好描述的，所以用一个更通用的 Deployment 来描述。以 K8S 的发展方向，未来对所有长期伺服型的业务的管理，都会通过 Deployment 来管理



------

## Deployment 控制器部署应用



之前我们也使用Deployment部署过应用，如下代码所示

```
kubectrl create deployment web --image=nginx
```

但是上述代码不是很好的进行复用，因为每次我们都需要重新输入代码，所以我们都是通过YAML进行配置

但是我们可以尝试使用上面的代码创建一个镜像【只是尝试，不会创建】

```
kubectl create deployment web --image=nginx --dry-run -o yaml > nginx.yaml
```

然后输出一个yaml配置文件  nginx.yml，配置文件如下所示

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

我们看到的 selector 和 label 就是我们Pod 和 Controller之间建立关系的桥梁

![image-20231031235452893](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031235452893.png)





通过刚刚的代码，我们已经生成了 YAML 文件，下面我们就可以使用该配置文件快速创建 Pod 镜像了

```
kubectl apply -f nginx.yaml
```

![image-20231031235525441](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231031235525441.png)

但是因为这个方式创建的，我们只能在集群内部进行访问，所以我们还需要对外暴露端口

```
--port：就是我们内部的端口号
--target-port：就是暴露外面访问的端口号
--name：名称
--type：类型

kubectl expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1
```

同理，我们一样可以导出对应的配置文件

```
kubectl expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1 -o yaml > web1.yaml
```

得到的web1.yaml如下所示

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-11-16T02:26:53Z"
  labels:
    app: web
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:labels:
          .: {}
          f:app: {}
      f:spec:
        f:externalTrafficPolicy: {}
        f:ports:
          .: {}
          k:{"port":80,"protocol":"TCP"}:
            .: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
        f:selector:
          .: {}
          f:app: {}
        f:sessionAffinity: {}
        f:type: {}
    manager: kubectl
    operation: Update
    time: "2020-11-16T02:26:53Z"
  name: web2
  namespace: default
  resourceVersion: "113693"
  selfLink: /api/v1/namespaces/default/services/web2
  uid: d570437d-a6b4-4456-8dfb-950f09534516
spec:
  clusterIP: 10.104.174.145
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 32639
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: web
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```

然后我们可以通过下面的命令来查看对外暴露的服务

```
kubectl get pods,svc
```

然后我们访问对应的url，即可看到 nginx了



------

## 升级回滚和弹性伸缩



升级： 假设从版本为 1.14 升级到 1.15 ，这就叫应用的升级【升级可以保证服务不中断】

回滚：从版本 1.15 变成 1.14，这就叫应用的回滚

弹性伸缩：我们根据不同的业务场景，来改变 Pod 的数量对外提供服务，这就是弹性伸缩

<br>

应用升级和回滚

首先我们先创建一个 1.14 版本的 Pod

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx:1.14
        name: nginx
        resources: {}
status: {}
```

先指定版本为1.14，然后开始创建我们的Pod

```
kubectl apply -f nginx.yaml
```

同时，我们使用 docker images 命令，就能看到我们成功拉取到了一个 1.14 版本的镜像

![image-20231101232310584](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231101232310584.png)

使用下面的命令，可以将 nginx 从 1.14 升级到 1.15

```
kubectl set image deployment web nginx=nginx:1.15
```

查看升级状态

```
kubectl rollout status deployment web
```

查看历史版本

```
kubectl rollout history deployment web
```

回滚到指定版本

```
kubectl rollout undo deployment web --to-revision=2
```



弹性伸缩，也就是我们通过命令一下创建多个副本

```
kubectl scale deployment web --replicas=10
```

能够清晰看到，我们一下创建了 10 个副本

![image-20231101232528826](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231101232528826.png)



------

# Service

## 简介



前面我们了解到 Deployment 只是保证了支撑服务的微服务 Pod 的数量，但是没有解决如何访问这些服务的问题。一个 Pod 只是一个运行服务的实例，随时可能在一个节点上停止，在另一个节点以一个新的 IP 启动一个新的 Pod，因此不能以确定的 IP 和端口号提供服务

要稳定地提供服务需要服务发现和负载均衡能力。服务发现完成的工作，是针对客户端访问的服务，找到对应的后端服务实例。在 K8S 集群中，客户端需要访问的服务就是 Service 对象。每个 Service 会对应一个集群内部有效的虚拟 IP，集群内部通过虚拟 IP 访问一个服务

在 K8S 集群中，微服务的负载均衡是由 kube-proxy 实现的。kube-proxy 是 k8s 集群内部的负载均衡器。它是一个分布式代理服务器，在 K8S 的每个节点上都有一个；这一设计体现了它的伸缩性优势，需要访问服务的节点越多，提供负载均衡能力的 kube-proxy 就越多，高可用节点也随之增多。与之相比，我们平时在服务器端使用反向代理作负载均衡，还要进一步解决反向代理的高可用问题



------

## 存在的意义



因为 Pod 每次创建都对应一个 IP 地址，而这个 IP 地址是短暂的，每次随着 Pod 的更新都会变化，假设当我们的前端页面有多个 Pod 时候，同时后端也多个 Pod，这个时候，他们之间的相互访问，就需要通过注册中心，拿到 Pod 的 IP 地址，然后去访问对应的 Pod

页面前端的 Pod 访问到后端的 Pod，中间会通过 Service 一层，而 Service 在这里还能做负载均衡，负载均衡的策略有很多种实现策略，例如随机、轮询和响应比



Pod 和 Service 之间还是根据 label 和 selector 建立关联的 【和 Controller 一样】

![image-20231102221025520](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231102221025520.png)

在访问 service 的时候，其实也是需要有一个 ip 地址，这个 ip 肯定不是 pod 的 ip 地址，而是虚拟 IP



------

## 常用类型



Service 常用类型有三种：

* ClusterIp：集群内部访问
* NodePort：对外访问应用使用
* LoadBalancer：对外访问应用使用，公有云

<br>

我们可以导出一个文件 包含service的配置信息

```
kubectl expose deployment web --port=80 --target-port=80 --dry-run -o yaml > service.yaml
```

service.yaml 如下所示

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: web
status:
  loadBalancer: {}
```

如果我们没有做设置的话，默认使用的是第一种方式 ClusterIp，也就是只能在集群内部使用，我们可以添加一个 type 字段，用来设置我们的 service 类型

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: web
  type: NodePort
status:
  loadBalancer: {}
```

修改完命令后，我们使用创建一个 pod

```
kubectl apply -f service.yaml
```

然后能够看到，已经成功修改为 NodePort 类型了，最后剩下的一种方式就是 LoadBalanced：对外访问应用使用公有云

node 一般是在内网进行部署，而外网一般是不能访问到的，那么如何访问的呢

* 找到一台可以通过外网访问机器，安装 nginx，反向代理
* 手动把可以访问的节点添加到 nginx 中

如果我们使用 LoadBalancer，就会有负载均衡的控制器，类似于 nginx 的功能，就不需要自己添加到 nginx 上



------

# 配置管理

## Secret



Secret 的主要作用就是加密数据，然后存在 etcd 里面，让 Pod 容器以挂载 Volume 方式进行访问

场景：用户名 和 密码进行加密

一般场景的是对某个字符串进行 base64 编码 进行加密

```
echo -n 'admin' | base64
```

![image-20231104001600993](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231104001600993.png)



* 变量形式挂载到 Pod

  创建 secret 加密数据的 yaml 文件 secret.yaml

  ![image-20231104001652494](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231104001652494.png)

  然后使用下面命令创建一个 pod 并通过 get 命令查看

  ```
  kubectl create -f secret.yaml
  kubectl get pods
  ```

  ![image-20231104001735464](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231104001735464.png)

  然后我们通过下面的命令，进入到我们的容器内部

  ```
  kubectl exec -it mypod bash
  ```

  然后我们就可以输出我们的值，这就是以变量的形式挂载到我们的容器中

  ```
  # 输出用户
  echo $SECRET_USERNAME
  # 输出密码
  echo $SECRET_PASSWORD
  ```

  ![image-20231104001823827](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231104001823827.png)

  最后如果我们要删除这个 Pod，就可以使用这个命令

  ```
  kubectl delete -f secret-val.yaml
  ```

* 数据卷形式挂载到 Pod

  首先我们创建一个 secret-val.yaml 文件

  ![image-20231104001914207](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231104001914207.png)

  然后创建我们的 Pod

  ```
  # 根据配置创建容器
  kubectl apply -f secret-val.yaml
  # 进入容器
  kubectl exec -it mypod bash
  # 查看
  ls /etc/foo
  ```

  ![image-20231104001950657](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231104001950657.png)



------

## ConfigMap



ConfigMap 作用是存储不加密的数据到 etcd 中，让 Pod 以变量或数据卷 Volume 挂载到容器中

应用场景：配置文件



* 创建配置文件

  首先我们需要创建一个配置文件 redis.properties

  ```
  redis.port=127.0.0.1
  redis.port=6379
  redis.password=123456
  ```

* 创建 ConfigMap

  我们使用命令创建 configmap

  ```
  kubectl create configmap redis-config --from-file=redis.properties
  ```

  然后查看详细信息

  ```
  kubectl describe cm redis-config
  ```

  ![image-20231105000716919](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105000716919.png)

* 使用 Volume 数据卷形式挂载

  首先我们需要创建一个 cm.yaml

  ![image-20231105002346708](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105002346708.png)

  然后使用该 yaml 创建我们的 pod

  ```
  # 创建
  kubectl apply -f cm.yaml
  # 查看
  kubectl get pods
  ```

  最后我们通过命令就可以查看结果输出了

  ```
  kubectl logs mypod
  ```

  ![image-20231105002434517](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105002434517.png)

* 以变量的形式挂载 Pod

  首先我们也有一个 myconfig.yaml 文件，声明变量信息，然后以 configmap 创建

  ![image-20231105002510210](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105002510210.png)

  然后我们就可以创建我们的配置文件

  ```
  # 创建pod
  kubectl apply -f myconfig.yaml
  # 获取
  kubectl get cm
  ```

  然后我们创建完该 pod 后，我们就需要在创建一个 config-var.yaml 来使用我们的配置信息

  ![image-20231105002555640](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105002555640.png)

  最后我们查看输出

  ```
  kubectl logs mypod
  ```

  ![image-20231105002621957](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105002621957.png)



------

# Ingress

## 概述



原来是使用 Service 中的 NodePort 来实现

* 在每个节点上都会启动端口
* 在访问的时候通过任何节点，通过 ip + 端口号就能实现访问

但是 NodePort 还存在一些缺陷

* 因为端口不能重复，所以每个端口只能使用一次，一个端口对应一个应用
* 实际访问中都是用域名，根据不同域名跳转到不同端口服务中



在实际的访问中，我们都是需要维护很多域名， a.com 和 b.com

然后不同的域名对应的不同的 Service，然后 service 管理不同的 pod

需要注意，ingress 不是内置的组件，需要我们单独的安装，官方推荐的是使用 Nginx 实现的 Ingress Controller



------

## 部署流程



* 创建 Nginx Pod

  ```
  # 创建pod
  kubectl create deployment web --image=nginx
  # 查看
  kubectl get pods
  # 对外暴露端口
  kubectl expose deployment web --port=80 --target-port=80 --type:NodePort
  ```

* 部署 ingress controller

  下面我们来通过 yaml 的方式，部署我们的 ingress，配置文件如下所示

  ![image-20231105165231069](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105165231069.png)

  这个文件里面，需要注意的是 hostNetwork: true，改成 ture 是为了让后面访问到

  ```
  kubectl apply -f ingress-con.yaml
  # 查看是否部署成功
  kubectl get pods -n ingress-nginx
  ```

* 创建 ingress 规则文件

  ![image-20231105170002434](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105170002434.png)

  最后通过域名就能访问



------

# Helm

## 简介



Helm 就是一个包管理工具【类似于 npm】

![image-20231105221123686](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105221123686.png)





首先在原来项目中都是基于 yaml 文件来进行部署发布的，而目前项目大部分微服务化或者模块化，会分成很多个组件来部署，每个组件可能对应一个 deployment.yaml, 一个 service.yaml, 一个 Ingress.yaml 还可能存在各种依赖关系，这样一个项目如果有 5 个组件，很可能就有 15 个不同的 yaml 文件，这些 yaml 分散存放，如果某天进行项目恢复的话，很难知道部署顺序，依赖关系等，而所有这些包括

* 基于 yaml 配置的集中存放
* 基于项目的打包
* 组件间的依赖

但是这种方式部署，会有什么问题呢

* 如果使用之前部署单一应用，少数服务的应用，比较合适
* 但如果部署微服务项目，可能有几十个服务，每个服务都有一套 yaml 文件，需要维护大量的 yaml 文件，版本管理特别不方便

Helm的引入，就是为了解决这个问题

* 使用 Helm 可以把这些 YAML 文件作为整体管理
* 实现 YAML 文件高效复用
* 使用 helm 应用级别的版本管理

<br>

Helm 是一个 Kubernetes 的包管理工具，就像 Linux 下的包管理器，如 yum/apt 等，可以很方便的将之前打包好的 yaml 文件部署到 kubernetes 上

Helm有三个重要概念

* helm：一个命令行客户端工具，主要用于Kubernetes应用chart的创建、打包、发布和管理
* Chart：应用描述，一系列用于描述 k8s 资源相关文件的集合
* Release：基于 Chart 的部署实体，一个 chart 被 Helm 运行后将会生成对应的 release，将在 K8S 中创建出真实的运行资源对象。也就是应用级别的版本管理
* Repository：用于发布和存储 Chart 的仓库



------

## 组件及架构



Helm 采用客户端 / 服务端架构，有如下组件组成：

* Helm CLI 是 Helm 客户端，可以在本地执行
* Tiller 是服务器端组件，在 Kubernetes 集群上运行，并管理 Kubernetes 应用程序
* Repository 是 Chart 仓库，Helm 客户端通过 HTTP 协议来访问仓库中 Chart 索引文件和压缩包

![image-20231105222103410](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231105222103410.png)



2019年11月13日，Helm团队发布了Helm v3的第一个稳定版本，该版本主要变化如下：

* 最明显的变化是 Tiller 的删除
* relesase 可以在不同命名空间重用



------

## 安装 Helm



首先我们需要去[官网下载](https://helm.sh/docs/intro/quickstart/)

* 第一步，[下载Helm](https://github.com/helm/helm/releases) 安装压缩文件，上传到 linux 系统中
* 第二步，解压 helm 压缩文件，把解压后的 helm 目录复制到 usr/bin 目录中

或者直接使用命令

```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

<br>

我们都知道 yum 需要配置 yum 源，那么 helm 就就要配置 helm 源

```
# 配置微软源
helm repo add stable http://mirror.azure.cn/kubernetes/charts
# 配置阿里源
helm repo add aliyun https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
# 配置google源
helm repo add google https://kubernetes-charts.storage.googleapis.com/

# 更新
helm repo update
```

然后可以查看我们添加的仓库地址

```
# 查看全部
helm repo list
# 查看某个
helm search repo stable
```

或者可以删除我们添加的源

```
helm repo remove stable
```



------

## 快速部署应用



首先我们使用命令，搜索我们需要安装的应用

```
# 搜索 weave仓库
helm search repo weave
```

搜索完成后，使用命令进行安装

```
helm install ui aliyun/weave-scope
```

可以通过下面命令，来下载 yaml 文件【如果】

```
kubectl apply -f weave-scope.yaml
```

安装完成后，通过下面命令即可查看

```
helm list
```

![image-20231106001248562](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106001248562.png)

同时可以通过下面命令，查看更新具体的信息

```
helm status ui
```

但是我们通过查看 svc 状态，发现没有对象暴露端口

![image-20231106001320752](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106001320752.png)

所以我们需要修改 service 的 yaml 文件，添加 NodePort

```
kubectl edit svc ui-weave-scope
```

![image-20231106001345993](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106001345993.png)

这样就可以对外暴露端口了

![image-20231106001448418](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106001448418.png)

然后我们通过 ip + 32185 即可访问



------

## 自定义 Chart



使用命令，自己创建 Chart

```
helm create mychart
```

创建完成后，我们就能看到在当前文件夹下，创建了一个 mychart 目录

![image-20231106224804692](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106224804692.png)

* templates：编写 yaml 文件存放到这个目录
* values.yaml：存放的是全局变量的 yaml 文件
* chart.yaml：当前 chart 属性配置信息

在 templates 文件夹创建两个文件：deployment.yaml 和 service.yaml

```
# 导出deployment.yaml
kubectl create deployment web1 --image=nginx --dry-run -o yaml > deployment.yaml
# 导出service.yaml 【可能需要创建 deployment，不然会报错】
kubectl expose deployment web1 --port=80 --target-port=80 --type=NodePort --dry-run -o yaml > service.yaml
```

执行命令创建

```
helm install web1 mychart
```

当我们修改了 mychart 中的东西后，就可以进行升级操作

```
helm upgrade web1 mychart
```



------

## Chart 模板使用



刚刚我们创建 mychart 的时候，看到有 values.yaml 文件，这个文件就是一些全局的变量，然后在 templates 中能取到变量的值，下面我们可以利用这个，来完成动态模板

在 values.yaml 定义变量和值

![image-20231106225416515](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106225416515.png)

我们通过表达式形式 使用全局变量` {{.Values. 变量名称}}` 例如： `{{.Release.Name}}`

![image-20231106225452145](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106225452145.png)

在我们修改完上述的信息后，就可以尝试的创建应用了

```
helm install --dry-run web2 mychart
```

![image-20231106225509700](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106225509700.png)



------

# 持久化存储

## NFS



之前我们有提到数据卷：emptydir ，是本地存储，pod 重启，数据就不存在了，需要对数据持久化存储

对于数据持久化存储【pod 重启，数据还存在】，需要通过 nfs 网络存储【通过一台服务器来存储】

<br>

* 使用一台新的服务器安装 NFS

  ```
  # 安装 NFS
  yum install -y nfs-utils
  # 设置存放数据的目录
  mkdir -p /data/nfs
  # 打开文件
  vim /etc/exports
  # 添加如下内容
  /data/nfs *(rw,no_root_squash)
  ```

  执行完成后，即部署完我们的持久化服务器

* 操作 Node 节点

  ```
  yum install -y nfs-utils
  ```

* 回到 NFS 服务端，启动我们的 NFS 服务

  ```
  # 启动服务
  systemctl start nfs
  # 或者使用以下命令进行启动
  service nfs-server start
  ```

* 再回到 Node 节点

  ```
  # 创建一个pv文件
  mkdir pv
  # 进入
  cd pv
  ```

  然后创建一个 yaml 文件 nfs-nginx.yaml

  ![image-20231106234201675](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231106234201675.png)

  通过这个方式，就挂载到了刚刚我们的nfs数据节点下的 /data/nfs 目录

  我们通过这个 yaml 文件，创建一个 pod

  ```
  kubectl apply -f nfs-nginx.yaml
  ```



------

## PV 和 PVC



对于上述的方式，我们都知道，我们的 ip 和端口是直接放在我们的容器上的，这样管理起来可能不方便

![image-20231107000409573](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231107000409573.png)

所以这里就需要用到 pv 和 pvc 的概念了，方便我们配置和管理我们的 ip 地址等元信息

PV：持久化存储，对存储的资源进行抽象，对外提供可以调用的地方【生产者】

PVC：用于调用，不需要关心内部实现细节【消费者】

PV 和 PVC 使得 K8S 集群具备了存储的逻辑抽象能力。使得在配置 Pod 的逻辑里可以忽略对实际后台存储 技术的配置，而把这项配置的工作交给 PV 的配置者，即集群的管理者。存储的 PV 和 PVC 的这种关系，跟 计算的 Node 和 Pod 的关系是非常类似的；PV 和 Node 是资源的提供者，根据集群的基础设施变化而变 化，由 K8s 集群管理员配置；而 PVC 和 Pod 是资源的使用者，根据业务服务的需求变化而变化，由 K8s 集 群的使用者即服务的管理员来配置

<br>

* 创建一个 pvc.yaml

  ![image-20231107000516349](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231107000516349.png)

* 然后定义 pvc

  ![image-20231107000544166](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231107000544166.png)

* 然后在创建一个 pv.yaml

  ![image-20231107000608719](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231107000608719.png)

* 然后就可以创建 pod 了

  ```
  kubectl apply -f pv.yaml
  ```

  然后我们就可以通过下面命令，查看我们的 pv 和 pvc 之间的绑定关系

  ```
  kubectl get pv, pvc
  ```

  ![image-20231107000656704](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E4%BA%91%E5%8E%9F%E7%94%9F%E4%B8%8E%E8%BF%90%E7%BB%B4/K8S/K8S%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/image-20231107000656704.png)

  到这里为止，我们就完成了我们 pv 和 pvc 的绑定操作



------

# 部署 Docker 镜像



1. 首先根据镜像，导出 yaml

   ```
   # 导出yaml
   kubectl create deployment  javademo1 --image=registry.cn-
   shenzhen.aliyuncs.com/mogublog/java-project-01:1.0.0 --dry-run -o yaml > javademo1.yaml
   ```

   导出后的 javademo1.yaml 如下所示

   ```
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     creationTimestamp: null
     labels:
       app: javademo1
     name: javademo1
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: javademo1
     strategy: {}
     template:
       metadata:
         creationTimestamp: null
         labels:
           app: javademo1
       spec:
         containers:
         - image: registry.cn-shenzhen.aliyuncs.com/mogublog/java-project-01:1.0.0
           name: java-project-01
           resources: {}
   status: {}
   ```

2. 然后通过下面命令，通过 yaml 创建我们的 deployment

   ```
   # 创建
   kubectl apply -f javademo1.yaml
   # 或者我们可以进行扩容，多创建几个副本
   kubectl scale deployment javademo1 --replicas=3
   ```

3. 然后我们还需要对外暴露端口【通过 service 或者 Ingress】

   ```
   # 对外暴露端口
   kubectl expose deployment javademo1 --port=8111  --target-port=8111 --type=NodePort
   # 查看对外端口号
   kubectl get svc
   ```

4. 然后即可通过地址访问
