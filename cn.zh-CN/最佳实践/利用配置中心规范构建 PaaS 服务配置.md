# 利用配置中心规范构建 PaaS 服务配置 {#concept_irk_xkc_p2b .concept}

MQ（Message Queue），即消息队列，是一种常用的异步 RPC 技术。本文以使用 ACM 对 MQ 实现流量控制的场景为例，介绍了如何以规范的配置命名格式来进行限流设置。

## 配置规范问题的产生 {#section_y31_zkc_p2b .section}

如果是单一应用的单一属性配置，使用以下配置文件即可完成，没有配置规范的问题。

```
//配置目录结构
--app
   |--src
      |--config
         |--application.properties
//配置内容
RCV_INTERVAL_TIME=20
```

但是，如果是为分布式 PaaS 服务编写分布式规则，PaaS 服务提供方（而非应用方）在设计配置时会遇到不少问题。以 MQ 限流场景为例，可能遇到的问题有：

-   如何区分全局配置和局部应用配置：例如，PaaS 服务方在统一管控平台提供的服务时，如何既有全局的规则配置，又能针对某个应用进行特殊配置。
-   如何区分不同集群 MQ 服务：例如，在保证配置命名统一的情况下，如何区分 MQ1 Cluster 和 MQ2 Cluster 的配置。
-   如何使用同一套配置中心来隔离 Dev、Test、Staging、Prod 等不同环境。

以上 MQ 限流场景需求如图所示。

![](https://img.alicdn.com/tfs/TB1HeM3ox6I8KJjy0FgXXXXzVXa-1135-500.png)

显然，不恰当的配置命名规范将影响以上配置的易用性。

接下来，本文围绕配置中心介绍这方面的最佳实践。为了说明配置命名规范，首先需要介绍一下配置中心的配置结构组织的能力。

## 配置中心的配置结构功能 {#section_ej1_zkc_p2b .section}

除了集中管理配置和订阅推送等能力，配置中心还具备配置结构化能力，能帮助管理员大幅简化各种复杂应用场景下的配置管理。

1. 配置中心的配置结构能力说明

-   租户隔离：配置中心根据用户或场景对配置进行隔离的能力。有了租户隔离，不同的配置在不同的租户可以重名，而且具有不同的鉴权机制。
-   最小配置集合：配置中心如何将若干配置组合成一个配置集合。将不同配置放在一个最小配置集合来更改和发布，可以统一发布类似的配置，以便应用统一处理。配置路径的概念类似于文件路径或者网络域名，它让不同配置集合之间具有层级关系。
-   具体配置的 Key-Value 形式：用户如何在配置中心中设置具体配置内容。

2. 配置中心配置结构能力产品比较

为了更直观地说明，我们对比了几个配置中心产品：

-   阿里云 ACM：阿里云应用配置管理，前身为 Diamond，是国内最早的配置中心产品。目前在 Git 上有不同开源版本，在阿里云上有商业版可供使用。
-   Spring Cloud Config：Spring Cloud 官方配置中心工具，主要用于 Java Spring 领域。
-   ZooKeeper：虽然具备部分配置中心能力，但是由于定位于分布式协调信息管理，因此只适合在应用规模不大的情况下用作配置中心。鉴于其使用广泛，此处也将其纳入对比范围。

对比详情如下：

|功能|ACM|Spring Cloud Config|ZooKeeper|
|--|---|-------------------|---------|
|租户级隔离|采用 Namespace 和 Group 双重隔离，其中不同 Namespace 需要不同 AK/SK 鉴权，Group 则不需要。|一个 Git 项目为一个租户。|没有直接可用的租户隔离技术。|
|最小配置集合|以 DataID 为标识的配置集为最小配置集合。配置集合没有路径概念，但是通过合理设计结合通配符查询，可以间接达到配置路径的效果。|Git 项目中的配置文件为最小配置集合，没有配置路径概念。|Znode 为最小配置集合，有配置路径概念。|
|配置 Key-Value|KV 内容由用户在 DataID 下自由组装，格式不限，可以是 properties、Json、XML 等，管理页面提供校验功能。|通过 Git 项目中 properties 形式的配置文件设置 KV。|通过设置 Znode 的 Value来设置 KV，内容格式无限制。|

综上所述，ACM 在租户隔离和最小配置集合方面都有较大的灵活性。下文介绍如何利用 ACM 的 Namespace、Group、DataID 等配置功能来设计一个用于执行 QoS限流策略的合理配置结构。

## 基于配置中心的分布式服务配置设计最佳实践 {#section_gj1_zkc_p2b .section}

1. 配置结构

为了满足 MQ 配置的功能需求，结合 ACM 的特点，可采用以下配置方法。

-   以不同 Namespace 隔离不同环境的 MQ 配置。例如：

    -   ProdEnv 命名空间用于生产环境，TestEnv 用于测试环境，DevEnv 用于开发环境。
    -   不同环境通过 AK/SK 天然隔离，安全性进一步加强。
-   以 Group 区分不同集群提供的 MQ 服务，以隔离配置和简化访问形式。

    -   例如，对于专门为子部门核心交易部门服务的 MQ 集群，和为子部门交易类目部门服务的 MQ 集群，可通过 Group 区分不同的全局配置。这样做的优点在于，在生产系统中，所有应用采用同一（子）公司的 AK/SK（或类似认证体系密钥），因而简化了部署，有效隔离了不同集群的配置，降低了配置复杂性。

        ```
        DataID: mq.global.qos
        Group: Trading
        
        DataID: mq.global.qos
        Group: ProductCategory
        ```

-   全局配置以全局统一 DataID 命名配置项存放。

    -   其中，配置 ID 以 `mq` 开头，`global` 表示全局配置，`qos` 表示 qos 方面的配置，Group 可使用默认值。

        ```
        DataID: mq.global.qos
        Group: Default_Group
        ```

-   应用局部配置以相同前缀 qos.\* 来命名 ID。

    -   配置 ID 以 `mq` 开头，`app.[appname]` 表示需要重载的 app 配置项，Group 可使用默认值。

        ```
        DataID: mq.app.app1.qos
        Group: Default_Group
        DataID: mq.app.app2.qos
        Group: Default_Group
        ```


3. 配置具体 KV 的设置

在很多配置中心产品中，例如 Appolo、ACM System Manager Parameter Store，每个具体的配置是配置中心最小粒度的管理单元，用户需要在某个粒度上逐一设置配置的 KV。但是 ACM 并没有该限制。常用做法有两种：

-   仿照以上配置中心，将每个 Key 存在一个独特的 DataID上，例如：
    -   mq.global.qos.RCV\_INTERVAL\_TIME 设置为 50
    -   mq.global.qos.MAX\_THREAD 设置为 20
-   将常用配置聚合成一个 DataID，并编辑为一个配置文件（格式不限，例如 Properties、Json、XML 等\)

    -   例如 mq.global.qos 设置如下：

        ```
        //MQ 限流 QoS设置
        RCV_INTERVAL_TIME = 50
        MAX_THREAD = 20
        ```


在实践中，我们发现第二种方法更高效。除了更大的灵活性以外，还有一个优点是多个配置在一次变更中同时发布，既降低了性能开销，理论上也达到了变更批量变化的原子操作效果。

4. 配置结构示意图

以上设计的配置结构示意图如下：

![](https://img.alicdn.com/tfs/TB1IeM3ox6I8KJjy0FgXXXXzVXa-1196-611.png)

5. 方案优点总结

-   以 Namespace 隔离不同环境，使 MQ 配置项在不同 Namespace 可重复。不同 Namespace 通过管理人员、程序 AK/SK 等权限设置得到隔离保护，让配置项能统一，且各个环境互不干扰。

-   以 Group 隔离相同环境的不同集群，既能保证不同集群下配置的统一性（如配置名不变等），使代码更加简单，又能在逻辑上隔离不同的集群配置。

-   通过最小配置集 DataID 的规范命名设置，各 MQ 客户端既可以方便地查找 MQ Default 全局配置，又能查找到自己的应用特殊配置。此外，管理员在 ACM Portal 上通过前缀通配查找，能方便地查找出所有 MQ 对应的所有规则，使管理变得简单。示例如下：

    ![](https://img.alicdn.com/tfs/TB1MtU1oDnI8KJjy0FfXXcdoVXa-1053-524.png)


## 相关信息 {#section_dk1_zkc_p2b .section}

关于 ACM 配置结构和命名规范的更多信息，请参考：

-   [ACM应用配置管理官方网站](https://www.aliyun.com/product/acm)
-   阿里云 ACM 官方文档：[配置变更风险管理](intl.zh-CN/最佳实践/配置变更风险管理.md#)

