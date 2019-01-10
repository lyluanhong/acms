# Prerequisites {#concept_kf1_vsw_42b .concept}

spring-cloud-starter-acm won't be maintained any more. Therefore, we recommend that you use [spring-cloud-starter-alibaba-nacos-config](reseller.en-US//Nacos Spring Cloud.md#) from Nacos instead.

## Steps for using the Spring Cloud ACM SDK are as follows. {#section_cz2_xsw_42b .section}

1.  Add Maven dependency.

    ```
    <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-acm</artifactId>
         <version>1.0.8</version>
     </dependency>
    
    ```

2.  Configure the application name and the application group.

    Configure `application.properties` in Spring Boot, and configure `spring.application.group` and `spring.application.name`.

    ```
    spring.application.group=com.alibaba.cloud.acm
    spring.application.name=sample-app
    ```

3.  Configure the ACM environment and authentication information.

    Edit the `application.properties` file in Spring Boot, and configure `alibaba.acm.endpoint`、`alibaba.acm.namespace`、`alibaba.acm.accessKey` 和 `alibaba.acm.secretKey`：

    ```
    spring.application.group=com.alibaba.cloud.acm
    spring.application.name=sample-app
    alibaba.acm.endpoint=xxx
    
    # Namespace ID
    alibaba.acm.namespace=xxx
    
    # Access ACM with instance RAM role
    # alibaba.acm.ramRoleName=xxx
    
    alibaba.acm.accessKey=xxx
    alibaba.acm.secretKey=xxx
    
    # If it is an encrypted configuration, then add the following two lines for automatic decryption.
    # alibaba.acm.openKMSFilter=true
    # Regionid can be obtained by the Zone ID in the namespace details
    # alibaba.acm.regionId=xxx
    
    # If group is not DEFAULT_GROUP, then set alibaba.acm.group manually.
    # alibaba.acm.group=xxx
    
    # Options include properties, yaml, and yml, where properties is the default (only version 1.0.8 and above)
    #alibaba.acm.file-extension=properties
    
    ```

4.  Add application configuration in the ACM console.

    Log on to the ACM console and create a new configuration under the corresponding namespace.

    -   Write the Data ID in the following format:

        `${spring.application.group}:${spring.application.name}.{alibaba.acm.file-extension}`

        For example: `com.alibaba.cloud.acm:sample-app.properties`

    -   Select `Properties` for the configuration format, and put the specific key-value pairs in the configuration body:

        ```
        user.id = 001
        user.name = juven2
        user.age = 88
        ```


## Notes {#section_gbv_jtw_42b .section}

-   `spring-cloud-starter-acm 1.0.7` and higher version now supports `Spring Boot 2.x`.
-   `spring-cloud-starter-acm 1.0.8` and higher version now supports `YAML`.
-   We recommend that you use `2.0.1. RELEASE` and higher version of `Spring Boot 2.x`. `2.0.0. RELEASE` has [a bug that prevents it from reading old data](https://github.com/spring-projects/spring-boot/issues/12451).
-   To download the complete sample code, click: [spring-cloud-acm-sample.zip](https://acm-public.oss-cn-hangzhou.aliyuncs.com/sample/spring-cloud-acm-sample.zip).

## Related documents { .section}

-   [Access ACM with instance RAM role](../../../../../reseller.en-US/User Guide/Access ACM with instance RAM role.md#)

