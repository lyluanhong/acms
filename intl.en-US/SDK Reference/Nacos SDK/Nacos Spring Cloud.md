# Nacos Spring Cloud {#concept_edp_2fc_pfb .concept}

This topic explains how to integrate spring-cloud-starter-alibaba-nacos-config.

## Get the Starter {#section_tln_cvv_42b .section}

Add the following configuration to the pom.xml file of the Maven project to obtain spring-cloud-starter-alibaba-nacos-config.

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>${latest.version}</version>
</dependency>
```

## Example {#section_kvv_bnt_4fb .section}

1.  Configure the connection and configuration source in bootstrap.properties. The `dataId` of the configuration source is `com.alibaba.nacos.example.properties`.

    ```
    # The value of endpoint and namespace can be found in namespace details.
    spring.cloud.nacos.config.endpoint=${endpoint}
    spring.cloud.nacos.config.namespace=${namespace}
    # We recommend that you use the accessKey and secretKey of the RAM account
    spring.cloud.nacos.config.access-key=${accessKey}
    spring.cloud.nacos.config.secret-key=${secretKey}
    spring.application.name=com.alibaba.nacos.example
    # Specify the configuration extension name, including properties, yaml, and yml, where properties is the default value.
    spring.cloud.nacos.config.file-extension=properties
    
    ```

2.  Sets the property value with Spring's @Value annotation. Enable automatic update of configurations with Spring Cloud native annotation @RefreshScope.

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

3.  Open the ACM console and create a new configuration under the corresponding namespace.

    -   Data ID: `com.alibaba.nacos.example.properties`

    -   Select `Properties` for the configuration format, and put the specific key-value pairs in the configuration body:

        ```
        connectTimeoutInMills=3000
        ```

    For the complete sample code, see [nacos-spring-cloud-config-example](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-cloud-example/nacos-spring-cloud-config-example).


## Related documents {#section_apj_hjx_fgb .section}

-   [Spring Cloud Alibaba Nacos Config](https://github.com/spring-cloud-incubator/spring-cloud-alibaba/wiki/Nacos-config)


