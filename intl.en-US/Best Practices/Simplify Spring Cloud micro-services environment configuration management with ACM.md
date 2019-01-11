# Simplify Spring Cloud micro-services environment configuration management with ACM {#concept_hc1_rsc_p2b .concept}

This article describes how to use Alibaba Cloud Configuration Center \(ACM\) in concert with Spring Cloud to help you simplify environment configuration management in a micro-service architecture, by testing different databases connected to the production environment and configuring different data source \(including connection pool\) parameters.

## Environment attributes of configuration {#section_bqx_rsc_p2b .section}

During the continuous delivery of the system, the diversity and complexity of the system’s final running environment undoubtedly make it harder for us to manage configurations. Eugen Paraschiv briefly discussed this in his post [Configuration Must Be Environment Specific](http://www.baeldung.com/project-configuration-with-spring?spm=a2c4g.11186623.2.4.SrAWeG). It is also elaborated in depth in the “Containerization, scheduling, and configuration management” section in [Configuration management challenges in modern application architectures](https://yq.aliyun.com/articles/332375?spm=a2c4g.11186623.2.5.SrAWeG).

Due to the differences between the configurations of different environments, the artifacts of those environments are not consistent, and sometimes Docker can't deliver the "build once, run anywhere" experience as expected. Here are some simple examples to help you better understand.

-   The logLevel should be set to DEBUG in the development environment, INFO in the staging environment, and WARNING in the production environment.
-   The database runs on a 4-core 8 GB-RAM machine in the development environment, but on a 32-core 96 GB-RAM machine in the production environment.
-   The maximum thread number in the execution thread pool of the daily environment should be set to 15, while this number should be larger in the production environment, which is 150 by default.
-   In the online environment, application data sources in the Central Data Center need to connect to Database A, while application data sources in Shenzhen Data Center should connect to Database B due to the proximity.
-   Two-way synchronization switch should be switched off only in and only in Mini Taobao environment.
-   The new features should be made available only in the online Hangzhou Unit environment rather than other unit environments.

In this article we will briefly describe how to use Alibaba Cloud ACM in Spring Cloud to replace Spring Cloud Config in simplifying environment configuration management, and help you understand the ACM-based solutions to simplify micro-service environment configuration management. We will also list the pros and cons of ACM and Spring Cloud Config.

## User stories {#section_eqx_rsc_p2b .section}

In daily engineering practice, we often picture a simple scenario with a user story to facilitate the elaboration and communication. This is an illustration used for preaching in the early stages:

![](https://img.alicdn.com/5476e8b07b923/TB1M2BOdm_I8KJjy0FoXXaFnVXa)

Taking Movie Service as an example. Let's assume that we need to retrieve a list of all movies from the relational database MySQL\(RDS\). We only need the top-configuration machines for the production database, and we need to use different databases in the testing, pre-release, and production environments. Therefore, our applications need to have different data source configuration, connection pool configuration, database security configuration, and so on in different environments.

The following figure shows how to map different environments with ACM Namespace, and set different data source configurations for Movie Service in different running environments.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_acm_dev_stage_prod.png)

## Create micro-service: movie service {#section_fbf_fyc_p2b .section}

-   Create Spring Boot Starter micro-service: movie service

    Movie service’s business logic is very straightforward: to list all movies in MySQL\(RDS\):

    ![](https://img.alicdn.com/5476e8b07b923/TB14wIym0fJ8KJjy0FeXXXKEXXa)

    Here we created a standard JPA application \(similar to the sample project on Spring official website [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)\). The project structure is as shown in the following figure:

    ![](https://img.alicdn.com/5476e8b07b923/TB16Yn1m8fH8KJjy1XbXXbLdXXa)

-   Introduce JPA, MySQL, connection pool HikariCP and WEB dependencies

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

-   Create MySQL\(RDS\) database and users

    ```
    mysql> create database db_example; -- Create the new database
    mysql> create user 'springuser'@'localhost' identified by 'ThePassword'; -- Creates the user
    mysql> grant all on db_example.* to 'springuser'@'localhost'; -- Gives all the privileges to the new user on the newly created database
    ```

    For detailed steps, see the “Create the database” section in [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/).

-   Create a web Controller

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


## Use Namespace in ACM to create isolated environment configuration {#section_grx_rsc_p2b .section}

**Note:** To use ACM on Alibaba Cloud, you must enable this service. For instructions, see [Activate ACM](../../../../../reseller.en-US/Quick Start/Activate ACM.md#). Once you activate the service and log on, you can create namespaces and configurations in the .

-   Create three environments in ACM: dev, stage, and prod

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_namespace_dev_stage_prod.png)

-   Create configuration respectively for dev, stage, and prod environments

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_configuration_dev_stage_prod.png)


In the previous step, we set different values for the same configuration item in different environments. Taking the `spring.datasource.url` configuration item as an example, we connect different databases to different environments by setting different URLs, and only enables SSL \(useSSL=true\) in the production environment.

```
dev:
    spring.datasource.url=jdbc:mysql://localhost:3306/db_example? useSSL=false
prod:
    spring.datasource.url=jdbc:mysql://30.5.101.169:3306/db_example? useSSL=true
```

In addition, we have set a larger database connection pool and a smaller timeout value for the prod environment.

```
dev:
    spring.datasource.hikari.connection-timeout=60000
    spring.datasource.hikari.maximum-pool-size=10
prod:
    spring.datasource.hikari.connection-timeout=15000
    spring.datasource.hikari.maximum-pool-size=200
```

To make it easier to debug, we only enables SQL Trace in the dev environment.

```
dev:
    spring.jpa.show-sql=true
```

## Integrate Movie Service with ACM {#section_qrx_rsc_p2b .section}

Next, we integrate Movie Service with ACM to obtain the corresponding environment configuration from ACM. For instructions on how to use ACM in Spring Cloud, see [Spring Cloud ACM](../../../../../reseller.en-US/SDK Reference/Spring Cloud ACM/Prerequisites.md#).

-   Introduce ACM dependencies to Movie Service

    ```
    <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-acm</artifactId>
       <version>1.0.1</version>
    </dependency>
    ```

-   Configure ACM connection information, namespace, accessKey, secretKey, and other information in application.properties

    ```
    spring.application.name=movie-service
    spring.application.group=com.alibaba.cloud.acm
    alibaba.acm.endpoint=acm.aliyun.com
    alibaba.acm.namespace=<your_namespace_id>
    alibaba.acm.accessKey=<your_ak>        
    alibaba.acm.secretKey=<your_sk>
    ```


**Note:** 

You can find your namespace\_id, accessKey, secretKey, and other information in “namespace details” or “code example of configuration”.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/db_namespace_details.png)

## Access Movie Service from your browser {#section_zrx_rsc_p2b .section}

![](https://img.alicdn.com/5476e8b07b923/TB1G8Qym0fJ8KJjy0FeXXXKEXXa)

## View pushing and refreshing history of ACM configurations {#section_bsx_rsc_p2b .section}

If spring-boot-starter-actuator dependency has been introduced to Movie Service, and `management.security.enabled=false` is set in application.properties, you can view the application’s configuration consumption and refreshing history from endpoint `http://<<ip:port>>/acm`.

![](https://img.alicdn.com/5476e8b07b923/TB10VsTm8HH8KJjy0FbXXcqlpXa)

You can also view the configuration’s push track, configuration version, and other information from the . For instructions, see ACM documentation.

## Brief comparison between ACM and Spring Cloud Config {#section_c4t_czc_p2b .section}

|What’s compared|Spring cloud config|Alibaba Cloud ACM|
|---------------|-------------------|-----------------|
|Spring Cloud seamless integration|Supported|Supported|
|Source code distribution method|Open source|To be open-sourced soon|
|Billing method|Free of charge|Free of charge|
|Large scale \(over 100,000 configuration items\) production verification|No publicly available cases of large scale production verification|Verified with Alibaba data center’s production environment featuring millions of configuration items, with over 100 million configuration changes pushed every day, and the Double 11 shopping spree and many other demanding scenarios|
|Configuration management UI console|No console, dependent on IDE, GIT, and other third-party tools|Professional configuration management UI console|
|Multi-language support|Mainly supports Java ecosystem, without any native clients for other languages|Supports Node.js, C++ and other native multi-language clients|
|Multi-data center, Local active-active disaster recovery, multi-zone, and other architectures|Dependent on support of GIT, ZooKeeper, and so on, without an explicit official statement|Supported|
|Configuration change push|Dependent on RabbitMQ/Kafka|Built-in push mechanism, without external dependency|
|Timeliness of large scale configuration push|Dependent on SLA and Webhooks such as GIT Webhooks and so on. Enterprise level large scale production capability is yet to be verified.|Industrial level production in milliseconds|
|Audit capability for configuration changes|Weak|Built-in audit mechanism \(with an audit capability conforming to National Graded Protection of Information Security - Level 3\)|
|Push track|Unable to view real-time monitoring that is configured to push to the client|Provides configuration change push tracks for monitoring configuration change push status|
|Data isolation|Supports application, profile, label, git repo, and other isolation policies|In addition to isolation policies provided by Spring Cloud, ACM supports multi-tenant, app, data\_id, group and other multi-level isolation policies|
|Production and O&M cost|High \(must have sufficient GIT/RabbitMQ knowledge and talent reserve\)|Low \(no third-party component dependencies\)|
|High availability|N/A \(customers must assume all risks\)|99.99% \(Alibaba Cloud assumes the risks\)|
|Secure communication|Supports SSL|Supports SSL|
|Disaster tolerance|Two levels \(storage, server cache\)|Three levels \(plus local disaster recovery of the client\)|

## Download project {#section_wsx_rsc_p2b .section}

Sample project used in this article can be downloaded from [movie-service.tar.gz](http://acm-public.oss-cn-hangzhou.aliyuncs.com/movie-service.tar.gz?spm=a2c4g.11186623.2.13.SrAWeG&file=movie-service.tar.gz).

This project has passed the test in the following environments:

-   Spring Cloud Edgware.RELEASE
-   Spring Boot 1.5.9. RELEASE
-   HikariCP 2.7.6
-   MySQL 5.7.11
-   ACM 4.2.0
-   ACM Spring Cloud SDK 1.0.1

**Note:** Before running this project locally, make sure to set your own ACM accessKey and secretKey in application.properties.

