# Nacos Spring Boot {#concept_ydg_v2v_4fb .concept}

本文介绍 nacos-config-spring-boot-starter 的接入方法。

## Starter 获取 {#section_tln_cvv_42b .section}

在 Maven 项目的 pom.xml 文件中填写以下配置，即可获取 nacos-config-spring-boot-starter。

```
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-config-spring-boot-starter</artifactId>
    <version>${latest.version}</version>
</dependency>
```

## 使用示例 {#section_kvv_bnt_4fb .section}

1.  在 application.properties 中配置连接信息。

    ```
    # 在命名空间详情处可以获取到 endpoint 和 namespace 的值
    nacos.config.endpoint=${endpoint}
    nacos.config.namespace=${namespace}
    # 推荐使用 RAM 账户的 accessKey 和 secretKey
    nacos.config.access-key=${accessKey}
    nacos.config.secret-key=${secretKey}
    
    ```

2.  使用 `@NacosPropertySource` 加载 `dataId` 为 `com.alibaba.nacos.example` 的配置源，并开启自动更新。

    ```
    @SpringBootApplication
    @NacosPropertySource(dataId = "com.alibaba.nacos.example", autoRefreshed = true)
    public class NacosConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(NacosConfigApplication.class, args);
        }
    }
    ```

3.  通过 Nacos 的 @NacosValue 注解设置属性值。

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

4.  打开 ACM 控制台，在相应的命名空间（namespace）下新建配置。

    -   Data ID ：`com.alibaba.nacos.example`

    -   配置格式选择 `Properties`，配置内容即为想要注入到应用中的具体 key-value：

        ```
        connectTimeoutInMills=3000
        ```

    完整示例代码，请参看：[nacos-spring-boot-config-example](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-boot-example/nacos-spring-boot-config-example)


