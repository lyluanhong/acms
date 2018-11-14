# SDK 简介 {#concept_lz3_5tv_42b .concept}

ACM SDK 是 ACM 提供给用户的获取配置手段，用于获取、监听配置。

ACM SDK 目前支持以下类型：

-   ACM Java Native SDK：支持 ACM 配置监听和变更的 Java 原生 SDK。
-   Spring Cloud ACM：支持 Spring Cloud Config 接口规范的 Java SDK。后续将不再继续维护，推荐使用 Nacos 的 [spring-cloud-starter-alibaba-nacos-config](https://help.aliyun.com/document_detail/94708.html)。
-   ACM Node.js SDK：支持 ACM 配置监听和变更的 Node.js 原生 SDK。
-   ACM C++ SDK：支持 ACM 配置监听和变更的 C++ 原生 SDK。
-   ACM Python（已开源）：支持 ACM 配置监听和变更的 Python 原生 SDK。
-   ACM PHP \(已开源）：支持 ACM 配置监听的 PHP 原生 SDK。
-   Nacos Client：支持 ACM 配置监听和变更的 Java 原生 SDK。

ACM SDK 的功能特性如下：

|功能 / 语言|Java Native|Java Spring Cloud|Python|Node.JS|C++|PHP|Nacos SDK|
|-------|-----------|-----------------|------|-------|---|---|---------|
|获取特定配置|支持|支持|支持|支持|支持|支持|支持|
|监听特定配置|支持|支持|支持|支持|支持|不支持|支持|
|写入配置|支持|不支持|不支持|不支持|不支持|支持|支持|
|支持固定租户下枚举配置|支持|不支持|不支持|不支持|不支持|不支持|不支持|
|支持单一 IP 方式进行服务端连接|支持|不支持|支持|不支持|不支持|不支持|支持|
|支持多 IP LB 方式进行服务端连接\*|支持|支持|支持|支持|支持|支持|支持|
|支持 HmacSHA1 算法方式用户认证|支持|支持|支持|支持|支持|支持|支持|
|支持本地缓存备份\*\*|支持|支持|支持|支持|支持|不支持|支持|
|[支持 ECS 实例 RAM 角色鉴权](https://help.aliyun.com/document_detail/72013.html)|支持|支持|支持|不支持|不支持|不支持|支持|
|开源地址|计划中|计划中|[acm-sdk-python](https://github.com/alibaba/acm-sdk-python)|计划中|计划中|[acm-sdk-php](https://github.com/alibaba/acm-sdk-php)|[Nacos](https://github.com/alibaba/nacos)|

-   \* 多 IP LB 方式是指 ACM SDK 基于地址服务器返回的多 ACM 服务端 IP 地址的 LoadBalance 功能，以提高性能和实现高可用。
-   \*\* 本地缓存备份功能可以让 ACM SDK 在没有服务端连接情况下，通过读取上次获取配置后保存的本地缓存备份文件，来避免客户端应用宕机。
-   \*\*\* `spring-cloud-starter-acm` 后续将不再继续维护，推荐使用 Nacos 的 [spring-cloud-starter-alibaba-nacos-config](https://help.aliyun.com/document_detail/94708.html)。

