# Nacos Spring Cloud {#concept_edp_2fc_pfb .concept}

本文介绍 spring-cloud-starter-alibaba-nacos-config 的接入方法。

## 获取 Starter {#section_tln_cvv_42b .section}

在 Maven 项目的 pom.xml 文件中填写以下配置，即可获取 spring-cloud-starter-alibaba-nacos-config。

```
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>${latest.version}</version>
</dependency>
```

## 使用示例 {#section_kvv_bnt_4fb .section}

1.  在 bootstrap.properties 中配置连接信息、配置源，此时配置源的 `dataId` 为`com.alibaba.nacos.example.properties` 。

    ```
    # 在命名空间详情处可以获取到 endpoint 和 namespace 的值
    spring.cloud.nacos.config.endpoint=${endpoint}
    spring.cloud.nacos.config.namespace=${namespace}
    # 推荐使用 RAM 账户的 accessKey 和 secretKey
    spring.cloud.nacos.config.access-key=${accessKey}
    spring.cloud.nacos.config.secret-key=${secretKey}
    spring.application.name=com.alibaba.nacos.example
    # 指定配置的后缀，支持 properties、yaml、yml，默认为 properties
    spring.cloud.nacos.config.file-extension=properties
    
    ```

2.  通过 Spring 的 @Value 注解设置属性值，通过 Spring Cloud 原生注解 @RefreshScope 实现配置自动更新。

    ```
    @RestController
    @RequestMapping("/config")
    @RefreshScope
    public class ConfigController {
        @Value("${connectTimeoutInMills:5000}")
        private int connectTimeoutInMills;
        public void setConnectTimeoutInMills(int connectTimeoutInMills) {
            this.connectTimeoutInMills = connectTimeoutInMills;
        }
        @RequestMapping(value = "/get", method = GET)
        @ResponseBody
        public int get() {
            return connectTimeoutInMills;
        }
    }
    ```

    **说明：** 需要同时有 `Setter` 方法才能在配置变更的时候自动更新。

3.  打开 ACM 控制台，在相应的命名空间（namespace）下新建配置。

    -   Data ID：`com.alibaba.nacos.example.properties`

    -   配置格式选择 `Properties`，配置内容即为想要注入到应用中的具体 key-value：

        ```
        connectTimeoutInMills=3000
        ```

    完整示例代码，请参看：[nacos-spring-cloud-config-example](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-cloud-example/nacos-spring-cloud-config-example)


