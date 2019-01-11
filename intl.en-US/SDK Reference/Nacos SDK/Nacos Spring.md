# Nacos Spring {#concept_d45_dy5_4fb .concept}

This topic explains how to use Nacos Spring SDK.

## Get the SDK {#section_tln_cvv_42b .section}

To get Nacos Spring SDK, add the following configuration to the pom.xml file in the Maven project.

```
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-spring-context</artifactId>
    <version>${latest.version}</version>
</dependency>
```

## Example {#section_kvv_bnt_4fb .section}

1.  Add `@EnableNacosConfig` annotation to enable the configuration management service of Nacos Spring. Use `@NacosPropertySource` to load the configuration source with the `dataId` of `com.alibaba.nacos.example`, and turn on automatic update.

    ```
    @Configuration
    // The endpoint and namespace can be found in the namespace details. We recommend that you use the accessKey and secretKey of the RAM account.
    @EnableNacosConfig(globalProperties = @NacosProperties(endpoint = "${endpoint}", namespace = "${namespace}",
    accessKey = "${accessKey}", secretKey = "${secretKey}"))
    @NacosPropertySource(dataId = "com.alibaba.nacos.example", autoRefreshed = true)
    public class NacosConfiguration {
    }
    ```

2.  Set the value of the properties with the `@NacosValue` annotation of Nacos.

    ```
    Controller
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

3.  Open the ACM console and create a new configuration under the corresponding namespace.

    -   Data ID: `com.alibaba.nacos.example`

    -   Select `Properties` for the configuration format, and put the specific key-value pairs in the configuration body:

        ```
        connectTimeoutInMills=3000
        ```

    For the complete sample code, see [nacos-spring-config-example](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-example/nacos-spring-config-example).


## Related documents {#section_jpz_z3x_fgb .section}

-   [Nacos spring Project](https://github.com/nacos-group/nacos-spring-project/blob/master/README.md)

