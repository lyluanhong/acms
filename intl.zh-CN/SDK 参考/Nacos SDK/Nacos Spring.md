# Nacos Spring {#concept_d45_dy5_4fb .concept}

本文将介绍 Nacos Spring SDK 的接入方法。

## 准备工作 {#section_ktm_h5r_ngb .section}

-   您已成功创建[创建命名空间](../../../../../intl.zh-CN/用户指南/创建命名空间.md#)。
-   您已成功[创建配置](../../../../../intl.zh-CN/用户指南/创建配置.md#)，并将配置格式选择 `Properties`。

## Nacos Spring SDK 的使用步骤 {#section_uxl_jrs_ngb .section}

您可以下载[demo 工程](https://github.com/nacos-group/nacos-examples/tree/acm/nacos-spring-example/nacos-spring-config-example)来操作以下步骤。

1.  在您的 Maven 项目的 pom.xml 文件中增加以下配置来获取 Nacos Spring SDK。

    ```
    <dependency>
        <groupId>com.alibaba.nacos</groupId>
        <artifactId>nacos-spring-context</artifactId>
        <version>${latest.version}</version>
    </dependency>
    ```

2.  在您的工程中使用注解 `@EnableNacosConfig`启用 Nacos Spring 的配置管理服务。例如：

    **说明：** 

    -   请将 `${endpoint}`、`${namespace}`、`${accessKey}` 和 `${secretKey}` 替换成您的真实信息。
    -   在控制台选择**命名空间**标签页，在命名空间列表中单击目标命名空间**操作**栏的**详情**，即可查看 endpoint 和 namespace 等信息。
    ```
    @Configuration
    // 在命名空间详情处可以获取到 endpoint 和 namespace；推荐使用 RAM 账户的 accessKey 和 secretKey
    @EnableNacosConfig(globalProperties = @NacosProperties(endpoint = "${endpoint}", namespace = "${namespace}",
    accessKey = "${accessKey}", secretKey = "${secretKey}"))
    ```

3.  使用 `@NacosPropertySource` 加载配置源，并开启自动更新。

    **说明：** 请将 `${dataId}` 替换成您的配置的 dataId。

    ```
    
    @NacosPropertySource(dataId = "${dataId}", autoRefreshed = true)
    public class NacosConfiguration {
    }
    ```

4.  使用 Nacos 的 `@NacosValue` 注解设置属性值。

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

5.  打开 ACM 控制台，在配置列表中的目标配置右侧**操作**栏单击**编辑**，更改配置内容为想要注入到应用中的具体 key-value 并单击**发布**。例如：

    ```
    connectTimeoutInMills=3000
    ```


动态发布配置之后，本地工程的 Console 区域会打印日志，您可以根据日志信息检查是否发布成功。

## 相关文档 {#section_jpz_z3x_fgb .section}

-   [Nacos Spring Project](https://github.com/nacos-group/nacos-spring-project/blob/master/README.md)

