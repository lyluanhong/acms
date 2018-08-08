# Java Native SDK 常见异常 {#concept_gz3_f3c_p2b .concept}

常见异常列表：

-   异常：Caused by: java.lang.RuntimeException: fail to get diamond-server serverlist! env:default
-   原因：公测期间 ACM Java Native SDK 或者 Spring Cloud ACM 的应用必须部署在阿里云的经典网络或者 VPC 网络内部才能访问 ACM 配置。这主要是出于安全和 QoS 的考虑。以后会视情况开放公网的配置读取入口。

