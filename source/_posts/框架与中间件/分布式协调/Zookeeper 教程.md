---
title: Zookeeper 教程
date: 2025-10-25 07:25:41
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%88%86%E5%B8%83%E5%BC%8F%E5%8D%8F%E8%B0%83/Zookeeper%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%88%86%E5%B8%83%E5%BC%8F%E5%8D%8F%E8%B0%83/Zookeeper%20%E6%95%99%E7%A8%8B/preview.jpg
tags:
  - 框架和中间件
  - 分布式协调
  - Zookeeper
categories: 框架和中间件
---



# Zookeeper 入门

## 什么是 Zookeeper



Zookeeper 是一个分布式协调服务框架，由雅虎创建，后成为 Apache 的顶级项目。它提供了一种可靠的方式来协调分布式系统中的各个节点，就像是分布式系统的 “管家”，帮助解决分布式环境下的数据一致性、服务发现、配置管理等诸多难题



------

## 应用场景



**服务发现**

在分布式系统中，常常有多个服务实例。例如，一个电商系统中有多个商品服务实例、订单服务实例等。Zookeeper 可以维护服务实例的信息，当客户端需要调用某个服务时，能通过 Zookeeper 快速找到可用的服务实例地址，实现服务的自动发现，大大提高了系统的灵活性和可扩展性

<br>

**分布式锁**

在分布式环境下，多个进程可能同时竞争资源。以分布式数据库的事务操作为例，可能多个事务都想对同一数据进行修改。Zookeeper 可以提供分布式锁机制，确保同一时间只有一个进程能获取到锁，访问共享资源，避免数据不一致问题

<br>

**配置管理**

分布式系统中，各个节点可能需要相同的配置信息，如数据库连接字符串、系统参数等。通过 Zookeeper，管理员可以集中管理这些配置，当配置发生变化时，能及时通知到各个节点，保证所有节点使用的配置一致



------

## 特点



**高可用性**

Zookeeper 集群通常由多个节点组成，只要集群中超过半数的节点正常工作，整个集群就能对外提供服务。即使部分节点出现故障，也不会影响系统的正常运行，确保了服务的高可用性

<br>

**顺序一致性**

Zookeeper 对所有的写操作都进行了顺序编号，客户端看到的 Zookeeper 数据视图是一致的，并且所有的读操作都能看到最新的写操作结果，保证了数据的顺序一致性

<br>

**原子性**

Zookeeper 的所有操作要么全部成功，要么全部失败，不存在部分成功的情况，保证了数据操作的原子性



------

## 工作机制



Zookeeper 构建了与文件系统类似的树形数据模型。以根节点 “/” 为起始，每个节点都被称作 ZNode。ZNode 具备独特性质，它既可以存储数据，也能拥有子节点。例如，在一个电商系统中，可能存在 “/ecommerce” 根节点，其下有 “/ecommerce/products” 子节点用于存储商品相关信息，“/ecommerce/orders” 子节点用于处理订单数据等。这种层级化的数据结构，使得 Zookeeper 能高效组织和管理分布式系统中的各类配置信息、服务状态等数据



------

# Zookeeper 安装

## Docker 安装



1. 拉取 Docker 镜像

   在安装 Zookeeper 之前，确保已经安装并启动了 Docker。打开命令行终端，执行以下命令拉取官方的 Zookeeper 镜像：

   ```sh
   docker pull zookeeper
   ```

   该命令会从 Docker Hub 上下载最新版本的 Zookeeper 镜像到本地。如果需要指定版本，例如下载 3.7.1 版本，可以使用以下命令：

   ```sh
   docker pull zookeeper:3.7.1
   ```

2. 运行 Docker 镜像

   拉取镜像后，通过以下命令来运行 Zookeeper 容器：

   ```sh
   # -d：表示以后台守护进程的方式运行容器
   # --name zookeeper：为容器命名为zookeeper，方便后续管理和识别
   # -p 2181:2181：将容器内部的 2181 端口映射到宿主机的 2181 端口。Zookeeper 客户端通过 2181 端口与 Zookeeper 服务器进行通信，这样在宿主机上就可以通过localhost:2181访问到容器内运行的 Zookeeper 服务
   # zookeeper：指定要运行的镜像名称
   docker run -d --name zookeeper -p 2181:2181 zookeeper
   ```

3. 配置 Docker（可选）

   默认情况下，容器内的 Zookeeper 会使用默认配置运行。如果需要自定义配置，可以通过挂载配置文件的方式来实现。首先，在宿主机上创建一个目录用于存放 Zookeeper 配置文件，例如`/my - zk - config`，内容如下：

   ```cfg
   tickTime=2000
   dataDir=/var/lib/zookeeper
   clientPort=2181
   ```

   启动 Zookeeper

   ```sh
   docker run -d --name zookeeper -p 2181:2181 -v /my-zk-config:/conf zookeeper
   ```

   这会将宿主机上`/my - zk - config`目录下的`zoo.cfg`文件挂载到容器内的`/conf`目录，容器内的 Zookeeper 就会使用该配置文件启动

4. 验证 Zookeeper 安装

   进入 Zookeeper 容器内部，使用自带的客户端工具进行验证。执行以下命令进入容器并创建节点：

   ```sh
   docker exec -it zookeeper bin/zkCli.sh
   create /test "hello"
   ```

   然后可以通过 get 命令获取该节点的数据：

   ```
   get /test
   ```

   如果能正确获取到设置的数据，说明 Zookeeper 服务正常运行



------

## 常用配置解读



* tickTime

  单位为毫秒。是 Zookeeper 服务器之间或客户端与服务器之间心跳的时间间隔

* dataDir

  指定 Zookeeper 存储数据的目录路径

* dataLogDir

  用于指定事务日志单独存储的目录，默认情况下，事务日志和数据快照都存储在dataDir指定的目录中。但在高并发写入场景下，将事务日志存储在单独的磁盘分区或设备上，可减少 I/O 竞争，提升性能

* clientPort

  设置 Zookeeper 服务器监听客户端连接的端口。默认值为 2181

* maxClientCnxns

  限制每个客户端与 Zookeeper 服务器建立的最大并发连接数。默认值为 60



------

## 集群选举机制



在 Zookeeper 集群中，选举机制是保障集群正常运行的关键部分，它决定了哪个节点将成为 Leader，负责协调集群内的事务

选举触发场景

* 当一个全新的 Zookeeper 集群启动时，各个节点都不知道当前的 Leader 是谁，此时会触发选举流程，以确定一个节点作为 Leader 来管理集群
* 在集群运行过程中，如果 Leader 节点因为硬件故障、网络问题或其他原因无法正常工作，集群需要重新选举一个新的 Leader，以保证集群的可用性和数据一致性

<br>

选举核心要素

* ZXID：Zookeeper 通过 ZXID 来标识事务操作的先后顺序。ZXID 是一个 64 位的数字，高 32 位表示 epoch（时代），低 32 位表示该 epoch 内的事务计数。每次有新的事务操作时，ZXID 会递增。例如，在一个事务操作序列中，ZXID 依次为 0x100000001、0x100000002 等，ZXID 越大，表示对应的事务越新
* 节点 ID：个 Zookeeper 节点在配置文件中都有一个唯一的 ID，取值范围是 1 - 255。节点 ID 用于在选举过程中区分不同的节点。例如，在一个包含三个节点的集群中，节点 ID 分别为 1、2、3

<br>

假设我们有一个由三个节点（节点 1、节点 2、节点 3）组成的 Zookeeper 集群，节点 ID 分别为 1、2、3，初始状态下集群尚未选举出 Leader

**投票阶段**

当集群启动时，每个节点都会给自己投票，认为自己是 Leader。此时，每个节点的投票信息包含自己的节点 ID 和当前的 ZXID。例如，节点 1 的投票为（1，ZXID1），节点 2 的投票为（2，ZXID2），节点 3 的投票为（3，ZXID3）。各节点会将自己的投票信息发送给集群中的其他节点

**投票与比较阶段**

每个节点接收到其他节点的投票后，会按照以下规则进行比较，首先比较 ZXID，ZXID 越大的投票越优先。例如，如果节点 1 收到节点 2 的投票（2，ZXID2），且 ZXID2 > ZXID1，那么节点 1 会更新自己的投票为（2，ZXID2），并将更新后的投票再次发送给其他节点；如果 ZXID 相同，则比较节点 ID，节点 ID 大的优先。例如，若节点 1 收到节点 3 的投票（3，ZXID1），由于 ZXID 相同，而节点 ID 3 > 节点 ID 1，节点 1 同样会更新自己的投票为（3，ZXID1）并重新发送

**确认 Leader**

随着投票的不断交换和更新，当某个节点获得超过半数（在这个三节点集群中，超过半数即 2 票）的相同投票时，该节点就会被选举为 Leader。例如，假设节点 2 的 ZXID 最大，经过几轮投票交换后，节点 1 和节点 3 都将投票更新为（2，ZXID2），此时节点 2 获得了两票，超过了半数，节点 2 就被选举为 Leader



------

## 脑裂



当 Leader 节点出现网络故障或负载过大时，无法及时回应 Flower 节点的心跳检测，这时候 Flower 节点可能会认为 Leader 节点已经宕机，重新触发选举流程并选出 Leader。这时候如果原先的 Leader 节点恢复了正常，此时整个集群就会有两个 Leader，这就是脑裂。脑裂会造成集群数据不一致，甚至会造成服务不可用

<br>

脑裂的解决方案：

* 增加冗余网络链路：通过部署多条网络链路，当一条链路出现故障时，其他链路可以继续维持节点之间的通信。例如，在跨机房的 Zookeeper 集群中，可以同时部署光纤和微波两条网络链路，互为备份，降低因网络故障导致脑裂的风险
* 启用仲裁机制：引入仲裁服务器（如使用 Keepalived 等工具），当集群中出现网络分区时，仲裁服务器可以判断哪个子集群应该成为真正的集群，避免多个子集群各自为政。例如，在一个三节点的 Zookeeper 集群中，添加一个仲裁服务器，当出现网络分区时，仲裁服务器可以根据预定义的规则，如优先选择包含节点 ID 最大的子集群作为有效集群，从而防止脑裂产生的不良影响
* 合理设置心跳参数：适当调整 Zookeeper 集群中节点之间的心跳检测时间和超时时间。较短的心跳检测时间可以更快地发现节点故障，但可能会增加网络开销；较长的超时时间可以减少误判，但可能会导致故障响应延迟。需根据实际情况合理设置，例如将心跳检测时间设置为 1 秒，超时时间设置为 3 秒，以在一定程度上避免因网络波动导致的误选举，降低脑裂发生的可能性



------

# 客户端命令行操作

## 命令行语法



| 命令基本语法 | 功能描述                                                     |
| ------------ | ------------------------------------------------------------ |
| help         | 显示所有操作命令                                             |
| ls path      | 使用 ls 命令来查看当前 znode 的子节点（可监听）<br />-w 监听子节点变化<br />-s 附加次级信息 |
| create       | 普通创建<br />-s 含有序列<br />-e 临时（重启或者超时消失）   |
| get path     | 获得节点的值（可监听）<br />-w 监听子节点变化内容<br />-s 附加次级信息 |
| set          | 设置节点的具体值                                             |
| stat         | 查看节点的状态                                               |
| delete       | 删除节点                                                     |
| deleteall    | 递归删除节点                                                 |

<br>

当使用 get 指令获取节点信息时，会输出以下内容：

```
cZxid = 0x100000002
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x100000002
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x100000003
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
```

* cZxid：创建该节点的事务 ID，是 Zookeeper 中事务的唯一标识，通过这个 ID 可以了解节点创建的顺序等信息
* ctime：节点的创建时间
* mZxid：最后一次修改该节点的事务 ID，通过对比 cZxid 和 mZxid，可以知道节点是否被修改过
* mtime：节点的最后修改时间
* pZxid：该节点的子节点列表最后一次修改的事务 ID，当有子节点被创建、删除或子节点的顺序发生变化时，pZxid 会更新
* cversion：子节点版本号，每当子节点列表发生变化，如添加或删除子节点，这个版本号就会递增
* dataVersion：节点数据的版本号，当节点的数据内容发生变化时，版本号会递增
* aclVersion：节点访问控制列表（ACL）的版本号，当 ACL 发生变化时，版本号递增
* ephemeralOwner：如果该节点是临时节点，这个字段存储创建该临时节点的会话 ID；如果是持久节点，该值为0x0
* dataLength：节点数据的长度
* numChildren：该节点的子节点数量



------

## 节点类型



Zookeeper 有三种基本节点类型：

* 持久节点

  一旦创建，除非主动删除，否则它将一直稳固存在于 Zookeeper 系统中。在企业级应用里，用于存储核心业务配置的节点，如数据库连接配置节点 “/config/db - connection”，就常被设置为持久节点，确保系统运行过程中配置的稳定性

* 临时节点

  临时节点与客户端会话紧密绑定。当客户端与 Zookeeper 断开连接，对应的临时节点会自动被删除。在服务注册场景中，服务实例向 Zookeeper 注册的节点多为临时节点。比如，一个新上线的商品服务实例，在 Zookeeper 中创建临时节点 “/services/product - service/instance - 1” 进行注册，若该服务实例意外下线，其对应的临时节点自动删除，其他服务或客户端便能及时知晓该服务的状态变化

* 顺序节点

  在创建节点时，Zookeeper 会自动为其添加一个单调递增的序号后缀。以分布式锁场景为例，多个客户端竞争锁资源时，会创建顺序节点，如 “/lock - node/lock - 0000000001”“/lock - node/lock - 0000000002” 等，通过比较节点序号大小，就能确定获取锁的先后顺序，保证资源访问的有序性

  顺序节点可以是持久顺序节点也可以是临时顺序节点

<br>

此外，还有两种特殊的节点类型：

* 容器节点

  容器节点是一种特殊的节点类型，当最后一个子节点被删除后，容器节点会被标记为待删除状态，在一段时间（默认 1 分钟）后自动删除。可以用于实现一些具有自动清理功能的资源管理场景，比如在分布式任务调度中，当所有子任务完成后，对应的任务容器节点可以自动清理

* TTL 节点

  可以为节点设置一个生存时间（Time To Live），在指定的时间过后，如果没有对节点进行更新操作，节点会自动被删除。适用于一些需要自动过期的数据存储场景，比如存储临时的认证信息、缓存数据等，以保证数据的时效性和系统的资源利用效率



------

## 监听器原理



客户端在向 Zookeeper 服务器发送请求时，如 getData、exists、getChildren 等操作，可以同时指定一个 Watcher。这个 Watcher 会被绑定到对应的操作请求上，并发送到 Zookeeper 服务器。服务器会将 Watcher 与相应的节点路径以及客户端会话进行关联，保存在内存中的 Watcher 管理器中

当 Zookeeper 服务器上的节点数据或状态发生变化时，服务器会检查是否有客户端对该节点或相关节点注册了 Watcher。如果有，服务器会根据事件类型和节点路径，找到对应的 Watcher，并将其放入一个待发送的事件队列中

Zookeeper 服务器会将事件队列中的 Watcher 事件发送给对应的客户端。客户端接收到事件通知后，会根据事件类型和节点路径，在本地回调相应的 Watcher 处理逻辑。客户端可以在 Watcher 的回调函数中执行相应的业务逻辑，如更新本地缓存、触发重新配置等操作

Watcher 具有一次性触发的特点，即一旦事件发生并触发了 Watcher，该 Watcher 就会被自动移除。如果客户端还需要继续监听该节点的后续事件，需要重新注册 Watcher

<br>

**应用场景**

* 分布式数据发布 / 订阅：可以实现配置信息的动态更新，客户端可以注册 Watcher 监听配置节点的变化，当配置信息更新时，客户端会收到通知并及时更新本地配置
* 集群管理：用于检测集群中节点的上下线状态，通过监听节点的创建和删除事件，其他节点可以及时感知到集群成员的变化，从而进行相应的处理，如重新分配任务、调整负载等
* 分布式锁：在实现分布式锁时，客户端可以通过监听锁节点的状态来判断是否获取到锁，当锁被释放时，其他客户端可以收到通知并尝试获取锁



------

# 客户端 API 操作

## 连接客户端



1. Apache Curator 是一个用于简化 Zookeeper 客户端编程的开源框架，它提供了更高级的 API 和更完善的错误处理机制。在 pom.xml 中添加以下依赖：

   ```xml
   <dependency>
       <groupId>org.apache.curator</groupId>
       <artifactId>curator-recipes</artifactId>
       <version>5.3.0</version>
   </dependency>
   <dependency>
       <groupId>org.apache.curator</groupId>
       <artifactId>curator-framework</artifactId>
       <version>5.3.0</version>
   </dependency>
   ```

2. Java 代码

   ```java
   public class ZookeeperCuratorExample {
       private static final String ZOOKEEPER_CONNECTION_STRING = "localhost:2181";
       private static final String NODE_PATH = "/testNode";
   
       public static void main(String[] args) {
           // 创建 Curator 客户端实例
           CuratorFramework client = CuratorFrameworkFactory.newClient(
                   ZOOKEEPER_CONNECTION_STRING,
                   new ExponentialBackoffRetry(1000, 3)
           );
           // 启动客户端
           client.start();
   
           try {
               // 创建节点
               createNode(client);
               // 查看节点数据
               getNodeData(client);
               // 查看子节点列表
               getChildrenNodes(client);
               // 删除节点
               deleteNode(client);
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               // 关闭客户端连接
               client.close();
           }
       }
   
       private static void createNode(CuratorFramework client) throws Exception {
           String data = "Hello, Zookeeper!";
           // 创建持久节点
           client.create()
                 .creatingParentsIfNeeded()
                 .withMode(CreateMode.PERSISTENT)
                 .forPath(NODE_PATH, data.getBytes());
           System.out.println("节点创建成功：" + NODE_PATH);
       }
   
       private static void getNodeData(CuratorFramework client) throws Exception {
           Stat stat = new Stat();
           byte[] data = client.getData().storingStatIn(stat).forPath(NODE_PATH);
           System.out.println("节点数据：" + new String(data));
           System.out.println("节点状态信息：" + stat);
       }
   
       private static void getChildrenNodes(CuratorFramework client) throws Exception {
           List<String> children = client.getChildren().forPath(NODE_PATH);
           System.out.println("子节点列表：" + children);
       }
   
       private static void deleteNode(CuratorFramework client) throws Exception {
           client.delete()
                 .guaranteed()
                 .deletingChildrenIfNeeded()
                 .forPath(NODE_PATH);
           System.out.println("节点删除成功：" + NODE_PATH);
       }
   }
   ```

   

------

## 监听节点变化



```JAVA
public class ZookeeperCuratorWatchExample {
    private static final String ZOOKEEPER_CONNECTION_STRING = "localhost:2181";
    private static final String NODE_PATH = "/testNode";

    public static void main(String[] args) {
        // 创建 Curator 客户端实例
        CuratorFramework client = CuratorFrameworkFactory.newClient(
                ZOOKEEPER_CONNECTION_STRING,
                new ExponentialBackoffRetry(1000, 3)
        );
        // 启动客户端
        client.start();

        try {
            // 监听节点数据变化
            watchNodeDataChange(client);
            // 监听子节点变化
            watchNodeChildrenChange(client);

            // 为了持续监听，这里让主线程休眠一段时间
            Thread.sleep(60000);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 关闭客户端连接
            client.close();
        }
    }

    private static void watchNodeDataChange(CuratorFramework client) throws Exception {
        // 创建 NodeCache 用于监听节点数据变化
        NodeCache nodeCache = new NodeCache(client, NODE_PATH);
        nodeCache.getListenable().addListener(() -> {
            ChildData data = nodeCache.getCurrentData();
            if (data != null) {
                System.out.println("节点数据发生变化，新数据为：" + new String(data.getData()));
            } else {
                System.out.println("节点已被删除");
            }
        });
        // 启动 NodeCache
        nodeCache.start(true);
    }

    private static void watchNodeChildrenChange(CuratorFramework client) throws Exception {
        // 创建 PathChildrenCache 用于监听子节点变化
        PathChildrenCache pathChildrenCache = new PathChildrenCache(client, NODE_PATH, true);
        pathChildrenCache.getListenable().addListener((client1, event) -> {
            switch (event.getType()) {
                case CHILD_ADDED:
                    System.out.println("子节点添加：" + event.getData().getPath());
                    break;
                case CHILD_UPDATED:
                    System.out.println("子节点更新：" + event.getData().getPath());
                    break;
                case CHILD_REMOVED:
                    System.out.println("子节点删除：" + event.getData().getPath());
                    break;
                default:
                    break;
            }
        });
        // 启动 PathChildrenCache
        pathChildrenCache.start(PathChildrenCache.StartMode.POST_INITIALIZED_EVENT);
    }
}
```



------

## 判断节点是否存在



```JAVA
public class ZookeeperCuratorCheckNodeExists {
    private static final String ZOOKEEPER_CONNECTION_STRING = "localhost:2181";
    private static final String NODE_PATH = "/testNode";

    public static void main(String[] args) {
        // 创建 Curator 客户端实例
        CuratorFramework client = CuratorFrameworkFactory.newClient(
                ZOOKEEPER_CONNECTION_STRING,
                new ExponentialBackoffRetry(1000, 3)
        );
        // 启动客户端
        client.start();

        try {
            // 判断节点是否存在
            boolean exists = checkNodeExists(client);
            if (exists) {
                System.out.println("节点 " + NODE_PATH + " 存在");
            } else {
                System.out.println("节点 " + NODE_PATH + " 不存在");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 关闭客户端连接
            client.close();
        }
    }

    private static boolean checkNodeExists(CuratorFramework client) throws Exception {
        // 使用 checkExists 方法判断节点是否存在
        Stat stat = client.checkExists().forPath(NODE_PATH);
        return stat != null;
    }
}
```



------

# 客户端写数据流程



1. **发起请求**

   客户端应用程序通过 Zookeeper 客户端库向 Zookeeper 集群中的任意一个节点发送写请求，这个节点可能是 Leader，也可能是 Follower

2. **转发请求（如有必要）**

   如果客户端发送请求的节点是 Follower，Follower 会将写请求转发给 Leader 节点。因为在 Zookeeper 集群中，所有的写操作都必须由 Leader 来协调处理，以保证数据的一致性和顺序性

3. **Leader 接收并处理请求**

   Leader 接收到写请求后，会为该请求分配一个全局唯一的事务 ID（zxid），这个 ID 会随着 Zookeeper 的运行单调递增，用于标识事务的先后顺序

   Leader 会将写操作记录到本地的事务日志中，同时会向集群中的所有 Follower 发送提议（Proposal），提议中包含了写操作的具体内容和 zxid

4. **Flower 处理协议**

   Follower 接收到 Leader 发送的提议后，会将提议记录到本地的事务日志中，并在本地执行写操作，然后 Follower 会向 Leader 发送一个 ACK 响应，表示自己已经成功接收并处理了提议

5. **Leader 进行投票决策**

   Leader 会等待集群中超过半数的 Follower 发送 ACK 响应。当收到超过半数的 ACK 后，Leader 认为该写操作可以提交。Leader 会向所有的 Follower 发送 Commit 消息，通知它们可以提交该事务

6. **Flower 提交事务**

   Follower 接收到 Leader 发送的 Commit 消息后，会将对应的事务提交，完成数据的持久化存储

7. **Leader 向客户端响应**

   Leader 在确定所有 Follower 都完成了事务提交后，会向客户端发送写操作成功的响应。客户端接收到响应后，就知道写操作已经成功完成



------

# 使用 Zookeeper 实现注册中心



服务提供者代码

```JAVA
public class ServiceProvider {
    private static final String ZOOKEEPER_CONNECTION_STRING = "localhost:2181";
    private static final String REGISTRY_PATH = "/service_registry";
    private CuratorFramework client;

    public ServiceProvider() {
        // 创建 Curator 客户端
        client = CuratorFrameworkFactory.newClient(
                ZOOKEEPER_CONNECTION_STRING,
                new ExponentialBackoffRetry(1000, 3)
        );
        client.start();
        try {
            // 检查 /service_registry 节点是否存在，如果不存在则创建该节点
            if (client.checkExists().forPath(REGISTRY_PATH) == null) {
                client.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath(REGISTRY_PATH);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 根据服务名称创建服务节点，如果服务节点不存在则创建。然后在服务节点下创建一个临时节点，节点数据为服务地址
    public void registerService(String serviceName, String serviceAddress) {
        try {
            String servicePath = REGISTRY_PATH + "/" + serviceName;
            if (client.checkExists().forPath(servicePath) == null) {
                client.create().withMode(CreateMode.PERSISTENT).forPath(servicePath);
            }
            String instancePath = servicePath + "/" + System.currentTimeMillis();
            client.create().withMode(CreateMode.EPHEMERAL).forPath(instancePath, serviceAddress.getBytes());
            System.out.println("Service registered: " + serviceName + " at " + serviceAddress);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        ServiceProvider provider = new ServiceProvider();
        provider.registerService("user-service", "127.0.0.1:8080");
    }
}
```

<br>

消费者代码

```JAVA
public class ServiceConsumer {
    private static final String ZOOKEEPER_CONNECTION_STRING = "localhost:2181";
    private static final String REGISTRY_PATH = "/service_registry";
    private CuratorFramework client;

    // 同样使用 CuratorFrameworkFactory 创建并启动客户端
    public ServiceConsumer() {
        client = CuratorFrameworkFactory.newClient(
                ZOOKEEPER_CONNECTION_STRING,
                new ExponentialBackoffRetry(1000, 3)
        );
        client.start();
    }

    // 根据服务名称检查服务节点是否存在，如果存在则获取该服务节点下的所有实例节点。简单选择第一个实例节点，并获取其节点数据（即服务地址）
    public String discoverService(String serviceName) {
        try {
            String servicePath = REGISTRY_PATH + "/" + serviceName;
            if (client.checkExists().forPath(servicePath) != null) {
                List<String> instances = client.getChildren().forPath(servicePath);
                if (!instances.isEmpty()) {
                    // 简单选择第一个实例
                    String instancePath = servicePath + "/" + instances.get(0);
                    byte[] data = client.getData().forPath(instancePath);
                    return new String(data);
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static void main(String[] args) {
        ServiceConsumer consumer = new ServiceConsumer();
        String serviceAddress = consumer.discoverService("user-service");
        if (serviceAddress != null) {
            System.out.println("Discovered service at: " + serviceAddress);
        } else {
            System.out.println("Service not found");
        }
    }
}
```

<br>

此示例只是一个简单的注册中心实现，在实际应用中可能需要考虑更多的错误处理、负载均衡、服务实例动态变化的监听等问题



------

# 使用 Zookeeper 实现分布式锁



```JAVA
public class DistributedLock {
    private static final String ZOOKEEPER_CONNECTION_STRING = "localhost:2181";
    private static final int SESSION_TIMEOUT = 5000;
    private static final String LOCK_ROOT_PATH = "/distributed_locks";
    private static final String LOCK_NODE_NAME = "lock_";

    private ZooKeeper zooKeeper;
    private String currentLockPath;

    public DistributedLock() {
        try {
            CountDownLatch connectionLatch = new CountDownLatch(1);
            zooKeeper = new ZooKeeper(ZOOKEEPER_CONNECTION_STRING, SESSION_TIMEOUT, event -> {
                if (event.getState() == Watcher.Event.KeeperState.SyncConnected) {
                    connectionLatch.countDown();
                }
            });
            connectionLatch.await();

            // 创建锁的根节点
            Stat stat = zooKeeper.exists(LOCK_ROOT_PATH, false);
            if (stat == null) {
                zooKeeper.create(LOCK_ROOT_PATH, new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
            }
        } catch (IOException | InterruptedException | KeeperException e) {
            e.printStackTrace();
        }
    }

    public void lock() {
        try {
            // 创建临时顺序节点
            currentLockPath = zooKeeper.create(LOCK_ROOT_PATH + "/" + LOCK_NODE_NAME, new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);

            // 获取所有子节点并排序
            List<String> children = zooKeeper.getChildren(LOCK_ROOT_PATH, false);
            Collections.sort(children);

            // 判断当前节点是否是最小的节点
            String smallestNode = LOCK_ROOT_PATH + "/" + children.get(0);
            if (currentLockPath.equals(smallestNode)) {
                // 是最小节点，获取到锁
                System.out.println("获取到锁：" + currentLockPath);
                return;
            }

            // 不是最小节点，监听前一个节点的删除事件
            String prevNode = LOCK_ROOT_PATH + "/" + children.get(children.indexOf(currentLockPath.substring(LOCK_ROOT_PATH.length() + 1)) - 1);
            CountDownLatch latch = new CountDownLatch(1);
            zooKeeper.exists(prevNode, event -> {
                if (event.getType() == Watcher.Event.EventType.NodeDeleted) {
                    latch.countDown();
                }
            });
            latch.await();
            System.out.println("获取到锁：" + currentLockPath);
        } catch (KeeperException | InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void unlock() {
        try {
            if (currentLockPath != null) {
                zooKeeper.delete(currentLockPath, -1);
                System.out.println("释放锁：" + currentLockPath);
            }
        } catch (InterruptedException | KeeperException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        DistributedLock lock = new DistributedLock();
        lock.lock();
        try {
            // 模拟业务逻辑
            System.out.println("执行业务逻辑...");
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

