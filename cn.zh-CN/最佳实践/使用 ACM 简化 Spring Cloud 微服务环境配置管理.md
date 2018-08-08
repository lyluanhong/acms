# 使用 ACM 简化 Spring Cloud 微服务环境配置管理 {#concept_hc1_rsc_p2b .concept}

在本文中，我们以测试和生产环境连接不同的数据库、配置不同的数据源（包括连接池）参数为例，介绍了如何搭配使用阿里云配置中心 ACM 与 Spring Cloud，帮助您在微服务架构中简化环境配置管理。

## 配置的环境属性 {#section_bqx_rsc_p2b .section}

在系统持续交付的过程中，系统最终运行环境的多样性及复杂性毫无疑问增加了我们在配置管理工作上的负担，有时候，可以说配置就是因环境而生的。

这一点在 Eugen Paraschiv 的博文 [Configuration Must Be Environment Specific](http://www.baeldung.com/project-configuration-with-spring?spm=a2c4g.11186623.2.4.SrAWeG) 里有简单的阐述，在博文[《现代应用架构中的配置管理面临的挑战》](https://yq.aliyun.com/articles/332375?spm=a2c4g.11186623.2.5.SrAWeG)的容器化、调度与配置管理小节也有深入阐述。

如果要问，是什么导致了我们应用的构建物（artifact）在各个环境不能保持一致？有时候 Docker 无法轻易达成“Build Once, Run Anywhere!”的承诺，原因往往就是环境配置的差异。为帮助您理解，此处列举一些简单的例子：

-   在开发环境中将 logLevel 设置为 DEBUG，在预发环境中将 logLevel 设置为 INFO，在生产环境中将 logLevel 设置为 WARNING。
-   在开发环境中使用 4 核 8G 的机器运行数据库，而在生产中用 32 核 96G 的机器运行数据库。
-   在日常环境执行线程池的最大线程数应该设置为 15，而生产环境上该值应该大一些，默认设为 150。
-   在线上环境中，中心机房内应用数据源需要连接 A 库，而深圳机房，应用应该就近连接使用 B 库。
-   只有在小淘宝环境，双向同步开关才应该关闭。
-   这次的改动有点大，新的特性仅在线上的杭州单元把该特性开放出来，其他的单元环境先不要开放出来。

在本文中，我们简要介绍了如何使用阿里云 ACM 配置管理产品在 Spring Cloud 中替代 Spring Cloud Config 以简化环境配置管理，帮助您理解基于 ACM 简化微服务环境配置管理的方案。此外，本文还会简单比较一下 ACM 与 Spring Cloud Config 方案的优劣。

## 场景故事 {#section_eqx_rsc_p2b .section}

为帮助理解需求和场景，在日常工程实践中，我们一般会以用户故事（User Story）的方式预设一个简单的场景，以此来做阐释和交流。下面是一张早期的布道图：

![](https://img.alicdn.com/5476e8b07b923/TB1M2BOdm_I8KJjy0FoXXaFnVXa)

以 Movie Service 为例，假设我们需要从关系数据库 MySQL\(RDS\) 检索所有电影信息列表，但是只有生产库需要顶配的机器，测试、预发和生产环境需要使用不同的数据库，因此我们的应用需要在不同的环境具备不同的数据源配置、连接池配置、数据库安全配置等等。

本文介绍了如何基于阿里云 ACM 的 Namespace 映射不同环境，为 Movie Service 在不同运行环境设置不同的数据源配置。

如下图所示：

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_acm_dev_stage_prod.png)

## 创建微服务 Movie Service {#section_fbf_fyc_p2b .section}

-   新建 Spring Boot Starter 微服务应用 movie service

    movie service 的业务逻辑很简单——列出 MySQL\(RDS\) 中所有的 movie：

    ![](https://img.alicdn.com/5476e8b07b923/TB14wIym0fJ8KJjy0FeXXXKEXXa)

    这里我们创建了一个标准的 JPA 应用（类似于 Spring 官网的样例工程 [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)）。工程结构如图所示：

    ![](https://img.alicdn.com/5476e8b07b923/TB16Yn1m8fH8KJjy1XbXXbLdXXa)

-   引入 JPA、MySQL、连接池 HikariCP 以及 WEB 依赖

    ```
    <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <scope>runtime</scope>
    </dependency>
    <dependency>
       <groupId>com.zaxxer</groupId>
       <artifactId>HikariCP</artifactId>
       <version>2.7.6</version>
    </dependency>
    ```

-   创建 MySQL\(RDS\) 数据库及用户

    ```
    mysql> create database db_example; -- Create the new database
    mysql> create user 'springuser'@'localhost' identified by 'ThePassword'; -- Creates the user
    mysql> grant all on db_example.* to 'springuser'@'localhost'; -- Gives all the privileges to the new user on the newly created database
    ```

    详细步骤可参考 [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/) 中的“Create the database”小节。

-   创建 WEB Controller

    ```
    package com.alibaba.demo.microsvc.controller;
     import org.springframework.beans.factory.annotation.Autowired;
     import org.springframework.web.bind.annotation.RequestMapping;
     import org.springframework.web.bind.annotation.ResponseBody;
     import org.springframework.web.bind.annotation.RestController;
     import com.alibaba.demo.microsvc.dao.MovieRepository;
     import com.alibaba.demo.microsvc.model.Movie;
     @RestController
     public class MovieController {
         @Autowired
         MovieRepository movieRepository;
         @RequestMapping("/list-movies")
         public @ResponseBody Iterable<Movie> listMovies() {
               return movieRepository.findAll();
         }    
    }
    ```


## 在 ACM 中使用 Namespace 创建隔离的环境配置 {#section_grx_rsc_p2b .section}

**说明：** 在阿里云上使用 ACM 的前提是开通该项服务，开通流程可参考[开通 ACM 服务](../../../../intl.zh-CN/快速入门/开通 ACM 服务.md#)。开通服务并登录后，即可进入 [ACM 控制台](https://acm.console.alibabacloud.com/)创建命名空间及配置。

-   在 ACM 中创建 3 个环境（dev、stage、prod）

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_namespace_dev_stage_prod.png)

-   为 dev、stage、prod 环境分别创建配置

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_configuration_dev_stage_prod.png)


那么，至此我们完成了什么呢？

在上一步中，我们为相同配置项针对不同环境设置了不同的值，例`如 spring.datasource.ur`l 这个配置项，我们通过设置不同的 url 来为各环境连接不同的数据库，并且仅在生产环境开启 SSL \(useSSL=true\)。

```
dev:
    spring.datasource.url=jdbc:mysql://localhost:3306/db_example?useSSL=false
prod:
    spring.datasource.url=jdbc:mysql://30.5.101.169:3306/db_example?useSSL=true
```

同时，我们也为生产环境（prod）设置了更大的数据库连接池和更小的连接超时时间。

```
dev:
    spring.datasource.hikari.connection-timeout=60000
    spring.datasource.hikari.maximum-pool-size=10
prod:
    spring.datasource.hikari.connection-timeout=15000
    spring.datasource.hikari.maximum-pool-size=200
```

而为了方便开发调试，我们仅在开发环境打开了 SQL Trace。

```
dev:
    spring.jpa.show-sql=true
```

## Movie Service 与配置中心 ACM 集成 {#section_qrx_rsc_p2b .section}

现在我们将集成 Movie Service 与 ACM，以便从 ACM 中获取对应环境的配置。关于如何在 Spring Cloud 中使用 ACM，请参考 [Spring Cloud ACM](../../../../intl.zh-CN/SDK 参考/Spring Cloud ACM/环境准备.md#)。

-   为 movie service 引入 ACM 依赖

    ```
    <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-acm</artifactId>
       <version>1.0.1</version>
    </dependency>
    ```

-   在 application.properties 配置 ACM 连接信息、namespace、accessKey、secretKey 等信息

    ```
    spring.application.name=movie-service
    spring.application.group=com.alibaba.cloud.acm
    alibaba.acm.endpoint=acm.aliyun.com
    alibaba.acm.namespace=<your_namespace_id>
    alibaba.acm.accessKey=<your_ak>        
    alibaba.acm.secretKey=<your_sk>
    ```


**说明：** 

您可以在 ACM 的“命名空间详情”或者“配置的示例代码”里找到您的 namespace\_id、accessKey、secretKey 等信息。

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/db_namespace_details.png)

## 在浏览器里访问 Movie Service {#section_zrx_rsc_p2b .section}

![](https://img.alicdn.com/5476e8b07b923/TB1G8Qym0fJ8KJjy0FeXXXKEXXa)

## 查看 ACM 配置推送刷新信息 {#section_bsx_rsc_p2b .section}

如果在 movie service 引入了 spring-boot-starter-actuator 依赖，并且在 application.properties 设置了 `management.security.enabled=false`，则可以通过端点 `http://<<ip:port>>/acm` 看到应用的配置消费及刷新情况。

![](https://img.alicdn.com/5476e8b07b923/TB10VsTm8HH8KJjy0FbXXcqlpXa)

也可以在 [ACM 控制台](https://acm.console.alibabacloud.com/)上查看配置的推送轨迹、配置版本等信息。具体使用方法可参考 ACM 官方文档。

## ACM 与 Spring Cloud Config 的简单对比 {#section_c4t_czc_p2b .section}

|对比项|Spring Cloud Config|阿里云 ACM|
|---|-------------------|-------|
|Spring Cloud 无缝集成|支持|支持|
|源码分发方式|开源|即将开源|
|收费模式|免费|免费|
|大规模（超 10 万配置）生产验证|无公开的大规模生产验证案例|阿里巴巴数据中心生产环境超百万级配置，每天超亿级配置变更推送，双 11 等严苛场景验证|
|配置管控 UI 控制台|无控制台，依赖 IDE、GIT 等第三方工具|专业的配置管理 UI 控制台|
|多语言支持|主要支持 Java 生态，无其他语言的原生客户端|支持 nodejs、c++ 等原生多语言客户端|
|多机房、同城双活、异地多活、多可用区等架构|依赖 GIT、ZooKeeper 等能力支持，官方无明确说明|支持|
|配置变更推送|依赖 RabbitMQ/KAFKA|内置的推送机制，无外部依赖|
|大规模推送时效|依赖 GIT Web Hook 等 SLA、WEB HOOK 在企业级大规模生产能力待验证|工业级、毫秒级|
|配置变更审计能力|弱|内置的审计机制（审计能力符合国家安全等保三级标准）|
|推送轨迹|无法查看配置推送到客户端的实时监测|有配置变更推送轨迹帮助监控配置变更推送状况|
|数据隔离|application、profile、label、git repo 等隔离策略|除 Spring Cloud 提供的隔离级别，还提供多租户、app、data\_id、group 等多级隔离策略|
|生产运维成本|高（必须对 GIT/RabbitMQ 等有足够的知识储备和人才储备）|低（无三方组件依赖\)|
|高可用|N/A（客户自行承担风险）|99.99%（阿里云承担风险）|
|安全通信|支持 SSL|支持 SSL|
|容灾|2 级（存储，服务器缓存\)|3 级，另有客户端本地容灾能力|

## 总结 {#section_vsx_rsc_p2b .section}

在本文中，我们以测试和生产环境连接不同的数据库、配置不同的数据源（包括连接池）参数为例，介绍了如何搭配使用阿里云配置中心 ACM 与 Spring Cloud，帮助您在微服务架构中简化环境配置管理。

## 工程下载 {#section_wsx_rsc_p2b .section}

本文的样例工程可以从 [movie-service.tar.gz](http://acm-public.oss-cn-hangzhou.aliyuncs.com/movie-service.tar.gz?spm=a2c4g.11186623.2.13.SrAWeG&file=movie-service.tar.gz) 下载。

该工程在以下版本环境测试通过：

-   Spring Cloud Edgware.RELEASE
-   Spring Boot 1.5.9.RELEASE
-   HikariCP 2.7.6
-   MySQL 5.7.11
-   ACM 4.2.0
-   ACM Spring Cloud SDK 1.0.1

**说明：** 在本地运行该工程前，请务必在 application.properties 里设置您自己的 ACM accessKey 和 secretKey。

