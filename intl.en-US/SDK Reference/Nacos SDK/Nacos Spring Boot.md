# Nacos Spring Boot {#concept_ydg_v2v_4fb .concept}

This topic explains how to use nacos-config-spring-boot-starter.

## Get the Starter {#section_tln_cvv_42b .section}

To get the nacos-config-spring-boot-starter, add the following configuration to the pom.xml file in the Maven project.

```
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-config-spring-boot-starter</artifactId>
    <version>${latest.version}</version>
</dependency>
```

## Example {#section_kvv_bnt_4fb .section}

1.  Configure the connection in application.properties.

    ```
    # The value of endpoint and namespace can be found in namespace details.
    nacos.config.endpoint=${endpoint}
    nacos.config.namespace=${namespace}
    # We recommend that you use the accessKey and secretKey of the RAM account
    nacos.config.access-key=${accessKey}
    nacos.config.secret-key=${secretKey}
    
    ```

2.  Use `@NacosPropertySource` to load the configuration source with the `dataId` of `com.alibaba.nacos.example`, and turn on automatic update.

    ```
    @SpringBootApplication
    @NacosPropertySource(dataId = "com.alibaba.nacos.example", autoRefreshed = true)
    public class NacosConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(NacosConfigApplication.class, args);
        }
    }
    ```

3.  Set the value of the properties with the @NacosValue annotation of Nacos.

    ```
    @Controller
    @RequestMapping("config")
    public class ConfigController {
        
        @NacosValue("${connectTimeoutInMills:5000}", autoRefreshed = true)
        private int connectTimeoutInMills;
      
        @RequestMapping(value = "/get", method = GET)
        @ResponseBody
        public int get() {
            return connectTimeoutInMills;
        }
    }
    ```

4.  Open the ACM console and create a new configuration under the corresponding namespace.

    -   Data ID: `com.alibaba.nacos.example`

    -   Select `Properties` for the configuration format, and put the specific key-value pairs in the configuration body:

        ```
        connectTimeoutInMills=3000
        ```

    For the complete sample code, see [nacos-spring-boot-config-example](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-boot-example/nacos-spring-boot-config-example).


## Related documents {#section_c4l_zhx_fgb .section}

-    [Nacos Config Spring Boot](https://github.com/nacos-group/nacos-spring-boot-project/blob/master/NACOS-CONFIG-QUICK-START.md)


