# Nacos Spring {#concept_d45_dy5_4fb .concept}

本文将介绍 Nacos Spring SDK 的接入方法。

## 获取 SDK {#section_tln_cvv_42b .section}

在 Maven 项目的 pom.xml 文件中填写以下配置，即可获取 Nacos Spring SDK。

```
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-spring-context</artifactId>
    <version>${latest.version}</version>
</dependency>
```

## 使用示例 {#section_kvv_bnt_4fb .section}

1.  添加 `@EnableNacosConfig` 注解启用 Nacos Spring 的配置管理服务。使用 `@NacosPropertySource` 加载了 `dataId` 为 `com.alibaba.nacos.example` 的配置源，并开启自动更新。

    ```
    @Configuration
    // 在命名空间详情处可以获取到 endpoint 和 namespace；推荐使用 RAM 账户的 accessKey 和 secretKey
    @EnableNacosConfig(globalProperties = @NacosProperties(endpoint = "${endpoint}", namespace = "${namespace}",
    accessKey = "${accessKey}", secretKey = "${secretKey}"))
    @NacosPropertySource(dataId = "com.alibaba.nacos.example", autoRefreshed = true)
    public class NacosConfiguration {
    }
    ```

2.  通过 Spring 的 `@Value` 注解设置属性值。

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

3.  打开 ACM 控制台，在相应的命名空间（namespace）下新建配置。

    -   Data ID ：`com.alibaba.nacos.example`

    -   配置格式选择 `Properties`，配置内容即为想要注入到应用中的具体 key-value：

        ```
        connectTimeoutInMills=3000
        ```

    完整示例代码，请参看：[nacos-spring-config-example](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-example/nacos-spring-config-example)


