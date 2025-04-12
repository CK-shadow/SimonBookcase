---
title: 工作流引擎 Camunda 7
date: 2024-08-10 12:59:43
tags: BPM
categories: 
  - 系统架构
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/preview.jpg
---



# 工作流引擎 Camunda 7

## 工作流引擎简介

### BPM



BPM 系统，全称 Business Process Management System，即业务流程管理系统。它是对端到端业务流程进行建模、分析和优化，以实现战略业务目标的一种信息化系统。其特点是以流程驱动为核心，实现端到端全流程信息化管理。BPM 系统可以应用于经常重复、正在进行或可预测的任务和流程，是企业数字化转型的重要一环

BPM 系统的主要功能包括：

1. 流程设计：通过可视化工具，用户可以轻松地创建、修改和测试业务流程模型，如审批流程、采购流程、销售流程等
2. 流程自动化：通过集成各种应用和系统，自动执行从审批到采购等各个流程中的任务，减少人工干预，提高流程的准确性和效率
3. 监控和报告：实时监控流程运行情况，并提供各种报告和分析工具，帮助企业了解流程性能和瓶颈，以便及时发现和解决问题，优化流程
4. 定制化：根据企业的特定需求进行定制化开发，实现业务流程的个性化管理，提高工作效率
5. 集成性：可以与其他企业级应用系统无缝集成，确保数据的一致性和互操作性



------

### 常见的开源工作流引擎



目前基于 Java 语言开发的主流开源工作流引擎有 osworkflow、jbpm、activiti、flowable、camunda。其中 osworkflow、jbpm 技术较老已经过时，activiti 包括 activiti5、activiti6、activiti7 三个版本，flowable 分开源版和商业版，camunda 包括 camunda7 和 camunda8 两个系列的版本



Activiti 5 是由 Alfresco 软件在 2010 年 5 月 17 日发布的业务流程管理（BPM）框架。这是一个开源的、灵活的、易扩展的可执行流程语言框架，它覆盖了业务流程管理、工作流、服务协作等领域。Activiti 5 的特色是提供了 Eclipse 插件，使开发人员可以通过插件直接绘制出业务流程图

Activiti 6 以及 Activiti 5 是由 Tijs Rademakers 团队开发的。然而，在 Activiti 6 的开发过程中，Tijs Rademakers 团队转去开发 Flowable 框架，因此 Activiti 6 以及 Activiti 5 的代码已经交接给了 Salaboy 团队进行维护



![image-20240419235735908](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240419235735908.png)



------

### Activiti



Activiti6 以及 Activiti5 的代码官方已经暂停维护了，Salaboy 团队目前在开发 Activiti7 框架，Activiti7 内核使用的还是 activiti6，并没有为引擎注入更多的新特性，只是在 Activiti 之外的上层封装了一些应用

Activiti7 即 Activiti Cloud：定位云产品，完全面向云原生架构设计开发，依赖 k8s 等多个 CNCF 云原生组件，开发、集成、部署和运维均比较复杂，对团队技术人员能力要求高，一般中小型项目，不建议选择 Activiti7，其它大型项目需谨慎选择 Activiti7

Flowable 和 Activiti7 都是基于 Activiti6 开发的，Flowable 修复了 Activiti6  的 bug 并添加了一些新功能，而 Activiti7 就是噱头，内核使用的还是 Activiti6。并没有为引擎注入更多的新特性，只是在 Activiti6 之外的上层封装了一些应用

并且 Activiti 的文档也比较确实，Activiti8 已经推出了，但仍未找到相关的介绍及文档，Activiti 的 POM 依赖也不再 Maven 中央仓库中。因此，谨慎选择 Activiti



------

### Flowable



Flowable 基于 Activiti6 衍生出来的版本，Flowable 目前最新版本是 v7.0.1，开发团队是从 Activiti 中分裂出来的，修复了一众 Activiti6 的 bug，并在其基础上研发了 DMN 支持，BPEL 支持等等，相对开源版，其商业版的功能会更强大

以 Flowable6.4.1 版本为分水岭，大力发展其商业版产品，开源版本维护不及时，部分功能已经不再开源版发布，比如表单生成器（表单引擎）、历史数据同步至其他数据源、ES 等

Flowable 的商业版可以使用，如果要使用开源版的话，需要确认所需的功能是否齐全，因此也不推荐



------

### Camunda 



Camunda 有 Camunda7 和 Camunda8 两个版本

Camunda7： Camunda7 基于 activiti5 发展来来，所以其保留了 PVM，最新版本 Camunda7.18，BPMN 标准模型，保持每年发布 2 个小版本的节奏，除了开源版本同时也提供了商业版，不过对于一般企业应用，开源版本也足够了。Camunda7 在功能方面比 Flowable、Activiti 流程引擎强大，性能和稳定性更突出

Camunda8：2022 年 4 月，官方发布了 Camunda8 新版本，Camunda7 和 Camunda8 在技术架构方面有本质区别。Camunda8 定位于云架构 SaaS 模式，基于 Zeebe 流程引擎内核，采用 gRPC API 接口技术，不再使用关系型数据库。在开源和商业授权方面，Camunda8 有诸多限制，Camunda8 仅有 Zeebe、modeler、elastic 组件是开源的，可以免费使用，其它的组件 Camunda Operate、Camunda Tasklist 、 Camunda Optimize 等组件是需要商业授权才能使用

Camunda BPMN 引擎性能和稳定性更好，功能也比较完善，除了 BPMN，Camunda 还支持企业和社区版本中的 CMMN（案例管理）和 DMN（决策自动化）。Camunda 不仅带有引擎，还带有非常强大的工具，用于建模，任务管理，操作监控和用户管理，所有这些都是开源的

私有化部署流程引擎需求的推荐选择 camunda7，大部分组件开源，可免费使用，技术生态较好，程序员上手容易



------

## Camunda 简介

### 概念



流程（PROCESS） : 通过工具建模最终生成的 BPMN 文件，里面有整个流程的定义

流程实例（Instance） ：流程启动后的实例

流程变量（Variables） ：流程任务之间传递的参数

任务（TASK） ：流程中定义的每一个节点

流程部署 ：将之前流程定义的 .bpmn 文件部署到工作流平台



------

### 官方文档



官网地址：https://docs.camunda.org/manual/latest/user-guide/process-engine/process-engine-api/



------

### 核心模块



![image-20240430233908116](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240430233908116.png)



* ProcessEngine

  流程引擎，可以通过他获取相关 service，里面集成了很多相关 service，默认实现如下

  ![image-20240430233957757](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240430233957757.png)

* RepositoryService

  此服务提供用于管理和操作部署和流程定义的操作，使用 Camunda 的第一要务

* RuntimeService

  运行相关，启动流程实例、删除、搜索等

* TaskService

  所有围绕任务相关的操作，如完成、分发、认领等

* HistoryService

  提供引擎搜集的历史数据服务

* IdentityService

  用户相关，实际中用不太到

------

### DMN 和 CMMN



Camduna 除了支持 BPMN 的功能，还支持 DMN 和 CMMN

DMN 是一个描述和贯彻基于决策的业务逻辑的开放标准，是一种利用表格来表述系统业务决策的图形性语言。DMN 适用于需要明确决策逻辑和规则的场景，如规则引擎、业务规则管理系统等。DMN 模型可以帮助组织更好地管理和优化决策过程，提高决策的质量和效率

与 BPMN 相比，CMMN 更加灵活，能够处理复杂的非结构化业务流程，并提供更灵活的建模能力，以支持动态调整和适应变化的需求。CMMN 更适用于处理复杂、非结构化和不确定性的业务场景，而 BPMN 则更适用于描述和管理结构化的业务流程



------

## 创建项目

### 项目依赖



首先引入 Camunda 相关依赖 https://mvnrepository.com/search?q=org.camunda.bpm.springboot

需要 3 个 maven 依赖，分别是对应 流程引擎、Web 管理平台、提供 rest api 操作接口包

```xml
<dependency>
    <groupId>org.camunda.bpm.springboot</groupId>
    <artifactId>camunda-bpm-spring-boot-starter</artifactId>
    <version>7.21.0</version>
</dependency>
<dependency>
    <groupId>org.camunda.bpm.springboot</groupId>
    <artifactId>camunda-bpm-spring-boot-starter-rest</artifactId>
    <version>7.21.0</version>
</dependency>
<dependency>
    <groupId>org.camunda.bpm.springboot</groupId>
    <artifactId>camunda-bpm-spring-boot-starter-webapp</artifactId>
    <version>7.21.0</version>
</dependency>
```



需要注意的是，Camunda 版本与 SpringBoot 版本有着严格的对应关系，以下是官方提供的版本对照表

https://docs.camunda.org/manual/latest/user-guide/spring-boot-integration/version-compatibility/

![image-20240503171611229](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240503171611229.png)



```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>camunda-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>camunda-demo</name>
    <description>camunda-demo</description>
 
    <properties>
        <java.version>17</java.version>
    </properties>
 
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.camunda.bpm.springboot</groupId>
            <artifactId>camunda-bpm-spring-boot-starter</artifactId>
            <version>7.21.0</version>
        </dependency>
        <dependency>
            <groupId>org.camunda.bpm.springboot</groupId>
            <artifactId>camunda-bpm-spring-boot-starter-rest</artifactId>
            <version>7.21.0</version>
        </dependency>
        <dependency>
            <groupId>org.camunda.bpm.springboot</groupId>
            <artifactId>camunda-bpm-spring-boot-starter-webapp</artifactId>
            <version>7.21.0</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.32</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
 
 
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
 
</project>
```



------

### 配置文件



Camunda  支持多种类型的数据库，包括 MySQL、Oracle、H2、DB2 、PostgreSQL 等，示例使用的是 MySQL



```yaml
server:
  port: 8081
 
# camunda登录信息配置
camunda.bpm:
  admin-user:
    id: admin  #用户名
    password: 123456  #密码
    firstName: Simon
  filter:
    create: All tasks
 
# mysql连接信息
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:8101/camunda
    username: root
    password: 123456
    type: com.mysql.cj.jdbc.MysqlDataSource
```



------

### 数据库表



项目启动成功后，Camunda 会自动创建数据库表，共 49 张，不同版本表的数量可能会有不同，其中与 BPMN 引擎相关的有 20 张，DMN 引擎相关的有 2 张，CMMN 引擎相关的有 5 张，其它的还有历史记录相关、用户信息相关的表等

表名都以 ACT 开头，第二部分是说明表用途的两字符标识，主要有以下几大模块：

* ACT_RE_*：RE代表存储库。带有此前缀的表包含“静态”信息，如流程定义和流程资源（图片、规则等）
* ACT_RU_*：RU 代表运行时间。这些是包含流程实例、用户任务、变量、作业等运行时数据的运行时表。引擎仅在流程实例执行期间存储运行时数据，并在流程实例结束时删除记录。这使运行时表保持小而快
* ACT_ID_*：ID代表身份。这些表包含用户、组等标识信息
* ACT_HI_*：HI 代表历史。这些表包含历史数据，如过去的流程实例、变量、任务等
* ACT_GE_*：通用数据，用于各种用例



以下是一些比较重要的表：

* `act_hi_actinst`： 执行的活动历史
* `act_hi_taskinst`：执行任务历史
* `act_hi_procinst`：执行流程实例历史
* `act_hi_varinst`：流程变量历史表
* `act_re_procdef`：流程定义存储
* `act_re_deployment`: 自动部署，springboot 每次启动都会重新部署，生成记录
* `act_ru_execution`：运行时流程实例
* `act_ru_task`：运行时的任务
* `act_ru_variable`：运行时的流程变量
* `act_ge_bytearray`：每次部署的文件 2 进制数据，所以如果文件修改后，重启也没用，因为重新生成了记录，需要清掉数据库，或者这个表记录



------

### 登录界面



登录地址为 `http://localhost:8081/`，输入用户名密码即为配置文件里面的 admin,123456

![image-20240503204831152](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240503204831152.png)

登陆成功后，如下所示

![image-20240503204847458](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240503204847458.png)



------

## 业务集成

### 下载绘图工具



Camunda Modeler：https://camunda.com/download/modeler/



------

### 绘制流程图



以下是一个简单的 OA 流程示例

![image-20240503224255041](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240503224255041.png)



------

### 任务分类



![image-20240503224430463](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240503224430463.png)

常见的任务有两种：User Task 和 Service Task



User Task 具体来说就是需要手动执行的任务，即需要写完业务代码后，调用代码，才会完成的任务

```JAVA
taskService.complete(taskId, variables);
```

Service Task 则是系统会自动帮助完成的任务



------

### 网关



![image-20240503225612136](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240503225612136.png)



* 排他网关（Exclusive Gateway）：只允许一条分支执行，根据条件表达式或规则选择下一个节点。流程执行到该网关时，会按照输出流的顺序逐个计算条件，当某个条件的计算结果为 true 时，会继续执行当前网关对应的输出流；如果所有条件的计算结果都是 false，并且存在默认顺序流（由 default 属性指定），则会执行默认顺序流；否则，引擎会抛出异常
* 并行网关（Parallel Gateway）：同时执行多条分支，当所有分支完成后，才继续执行下一个节点。这种网关通常用于并行处理任务，可以提高流程的执行效率
* 包容网关（Inclusive Gateway）：允许多条分支执行，并根据条件表达式或规则选择下一个节点。但如果没有任何一个分支满足条件，则选择默认分支。这种网关在处理多个可选任务时非常有用，可以根据实际情况选择执行哪些任务
* 事件网关（Event-based Gateway）：等待事件发生，并选择一个或多个分支执行。它可以根据事件的类型和属性来选择分支。这种网关通常用于响应外部事件或触发特定操作



![image-20240504211704530](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240504211704530.png)

如上图示例，

当 switch1 为 true，switch2 为 false 时，只执行任务一

当 switch1 为 false，switch2 为 true 时，只执行任务二

当 switch1 和 switch2 都为 true 时，任务一和任务二都会执行

当 switch1 和 switch2 都为 false 时，则直接走网关，然后任务结束



------

### 文件集成



将画好的流程图保存文件为 test_1.bpmn，在刚才的 springboot 项目中 resources 新建一个 bpmn 文件夹，放进去

![image-20240504211032605](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240504211032605.png)



重启项目，发现 web 界面中已经被集成进来了

![image-20240504211113205](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240504211113205.png)



------

### 代码集成



**Controller**

```JAVA
public class TaskController {

    @Resource
    private TestTaskService testTaskService;
    
    // 开启流程
    @PostMapping("/start/process")
    public void startProcess() {
        testTaskService.startProcess();
    }
    
    // 开启流程
    @PostMapping("/start/process")
    public void startProcess() {
        testTaskService.startProcess();
    }
    
    // 查询流程定义
    @GetMapping("/process/def")
    public List<ProcessDefinition> findProcess() {
        return testTaskService.findProcess();
    }
    
    // 查询任务
    @GetMapping("/task")
    public List<Task> findTasks() {
        return testTaskService.findTasks();
    }
    
}
```



**Service**

```JAVA
public void startProcess() {
    ProcessInstance instance = runtimeService.startProcessInstanceByKey("key");
    System.out.println(instance.toString());
}

public List<ProcessDefinition> findProcesses() {
    return repositoryService.createProcessDefinitionQuery().list();
}

public List<Task> findTasks() {
    return taskService.createTaskQuery().list();
}
```



------

## API 的使用

### 流程相关 API



创建流程，会同时创建第一个任务

```JAVA
ProcessInstance instance = runtimeService.startProcessInstanceByKey(processKey, params);
```



暂停流程，流程暂停后，再执行相关任务会报错，需要先重新激活任务

```JAVA
runtimeService.suspendProcessInstanceById(instance.getId());
```



重新激活流程

```JAVA
runtimeService.activateProcessInstanceById(instance.getId());
```



删除流程，会同时删除任务

```JAVA
runtimeService.deleteProcessInstance(instance.getId(), "手动删除");
```



以上都可以在流程历史表  `act_hi_procinst`  里查询



------

### 任务相关 API



基于 service 的查询类，都可先构建一个 query，然后在附上查询条件

```JAVA
List<ProcessDefinition> list = repositoryService.createProcessDefinitionQuery().list();
List<Task> list = taskService.createTaskQuery().taskAssignee("zhangsan").list();
List<ProcessInstance> instances = runtimeService.createProcessInstanceQuery().listPage(1, 10);
```



查询历史任务

```JAVA
List<HistoricProcessInstance> list = historyService.createHistoricProcessInstanceQuery().list();
```



查询当前任务/分页

```JAVA
List<Task> list = taskService.createTaskQuery().orderByTaskCreateTime().desc().list();
```



任务回退，大体思路是拿到当前的任务，及当前任务的上一个历史任务，然后重启

```JAVA
 Task activeTask = taskService.createTaskQuery()
                .taskId(taskId)
                .active()
                .singleResult();
        List<HistoricTaskInstance> historicTaskInstance = historyService.createHistoricTaskInstanceQuery()
                .processInstanceId(instanceId)
                .orderByHistoricActivityInstanceStartTime()
                .desc()
                .list();
 
        List<HistoricTaskInstance> historicTaskInstances = historicTaskInstance.stream().filter(v -> !v.getTaskDefinitionKey().equals(activeTask.getTaskDefinitionKey())).toList();
 
        Assert.notEmpty(historicTaskInstances, "当前已是初始任务！");
        HistoricTaskInstance curr = historicTaskInstances.get(0);
 
        runtimeService.createProcessInstanceModification(instanceId)
                .cancelAllForActivity(activeTask.getTaskDefinitionKey())
                .setAnnotation("重新执行")
                .startBeforeActivity(curr.getTaskDefinitionKey())
                .execute();
```



------

### 流程变量传递



变量最终会存在  `act_ru_variable`  这个表里面

在绘制流程图的时候，如果是用户任务（UserService） 可以设置变量，比如执行人

![image-20240505224950289](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240505224950289.png)

有两种方式，一种是写死，如 zhangsan，或者如上图一样传入参数，其实就是启动参数的时候传入，传入参数，可选值为一个 Map<String, Object>，之后的流程可查看次参数，上面写的是 user， 所以 map 里面的 key 需要带着 user，不然会报错



关于扩展变量，可在流程图绘制这么设定，传递方式还是一样，流程图里面在下面写

![image-20240505225230656](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240505225230656.png)

启动任务：

```JAVA
ProcessInstance instance = runtimeService.startProcessInstanceByKey(key, new HashMap<>());
```

变量设置：

```JAVA
runtimeService.setVariable(instance.getId(), Constants.PATIENT_ID, relatedId);
```

变量查询：

```JAVA
Object variable = runtimeService.getVariable(instance.getId(), Constants.GENERAL_ID);
```

历史变量查询：

```JAVA
HistoricVariableInstance variableInstance = historyService.createHistoricVariableInstanceQuery().processInstanceId(bo.getId().toString()).
            variableName(Constants.PATIENT_ID).singleResult();
//变量值
variableInstance.getValue();
//变量名称
variableInstance.getName();
```



------

### 用户任务



即需要用户参与的任务，因为工作流执行过程中需要涉及到审批、过审之类的需要用户参与的任务，这个时候需要用户参与，然后调用接口完成任务

比如以下是审批任务的示例：

```JAVA
@RestController  
public class ApprovalController {  

    @Autowired  
    private TaskService taskService;  

    @PostMapping("/approve-task")  
    public String approveTask(@RequestParam String taskId, @RequestParam(required = false) String approvalResult) {  

        // 可以根据需要设置流程变量  
        Map<String, Object> variables = new HashMap<>();  
        if (approvalResult != null) {  
            variables.put("approvalResult", approvalResult);  
        }  

        // 完成任务，表示审批完成  
        taskService.complete(taskId, variables);  

        // 返回成功消息或其他信息  
        return "Task " + taskId + " approved successfully.";  
    }  

}
```



------

### 系统任务



即自动执行的任务，比如用户提交后，系统自动存储、修改状态等自动完成的任务，可以选择多种任务类型

![image-20240505230438217](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240505230438217.png)



推荐使用 Delegate expression

在系统任务中，因为是自动执行，所以实际应用中需要嵌入各种业务逻辑，可以在流程图设计中，按照下面方式调用 java 代码执行，在 spring 中配置同名的 bean

![image-20240505230526732](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%20Camunda%207/image-20240505230526732.png)

配置表达式，可以实现 JavaDelegate 接口使用类名配置，快捷写法如下，比较推荐，此种可灵活配置 bean 和 spring 结合使用，注入 service 等业务方法

```JAVA
@Bean("t17")
JavaDelegate t17() {
    return execution -> {
        Map<String, Object> variables = execution.getVariables();
        Task task = taskService.createTaskQuery().processInstanceId(execution.getProcessInstanceId()).singleResult();
        //业务逻辑
        task.setOwner(String.valueOf(dentistId));
    };
}
```



使用 Java class，需要实现 JavaDelegate 接口，注意是全路径名，不可以使用 Spring 的 bean 配置

```JAVA
@Component
public class T17Delegate implements JavaDelegate {
 
    @Override
    public void execute(DelegateExecution execution) throws Exception {
            String taskId = execution.getId();
            String instanceId = execution.getProcessInstanceId();
            Map<String, Object> variables = execution.getVariables();
    }
}
```



------

### 流程权限及创始人设置



IdentityService 为鉴权相关服务，但是我们实际开发中，一般会用到我们自己的鉴权系统，所以可以使用 camunda 提供的 api 来设置，具体可以看 IdentityServiceImpl 这个类，其中也是使用了 ThreadLocal 来保存鉴权信息 ，代码如下

```JAVA
private ThreadLocal<Authentication> currentAuthentication = new ThreadLocal<Authentication>();
```



用户信息设置：

```JAVA
// Userutil是我们自己封装的用户工具类
identityService.setAuthenticatedUserId(UserUtil.getUserId().toString());
 
//获取
Authentication authentication = identityService.getCurrentAuthentication();
```



内置很多比如开启流程时候，会默认找当前登录的人，这个类 `DefaultHistoryEventProducer`

```JAVA
// set super process instance id
ExecutionEntity superExecution = executionEntity.getSuperExecution();
if (superExecution != null) {
  evt.setSuperProcessInstanceId(superExecution.getProcessInstanceId());
}

//state
evt.setState(HistoricProcessInstance.STATE_ACTIVE);

// set start user Id
evt.setStartUserId(Context.getCommandContext().getAuthenticatedUserId());
```



任务执行人及发起人设置

```JAVA
//根据任务id设置执行人
taskService.setAssignee(task.getId(), UserUtil.getUserId().toString());
```

