# Nacos Client {#concept_hwf_kmt_4fb .concept}

本文将介绍 Nacos Client SDK 的接入方法。

## 获取 SDK {#section_tln_cvv_42b .section}

在 Maven 项目的 pom.xml 文件中填写以下配置，即可获取 Nacos Client SDK。

```
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>${latest.version}</version>
</dependency>
<!--  有日志实现，下面可去掉  -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

## 使用示例 {#section_kvv_bnt_4fb .section}

在您的工程中运行以下代码进行配置管理。

```
import com.alibaba.nacos.api.NacosFactory;
import com.alibaba.nacos.api.PropertyKeyConst;
import com.alibaba.nacos.api.config.ConfigService;
import com.alibaba.nacos.api.exception.NacosException;
import com.alibaba.nacos.client.config.listener.impl.PropertiesListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Properties;
/**
 * 示例代码，仅用于示例测试
 */
public class NacosExample {
    private static final Logger LOGGER = LoggerFactory.getLogger(NacosExample.class);
    public static void main(String[] args) throws NacosException {
        Properties properties = new Properties();
        String dataId = "com.alibaba.nacos.example";
        String group = "DEFAULT_GROUP";
        String content = "connectTimeoutInMills=5000";
        // 从控制台命名空间管理的"命名空间详情"中拷贝 endpoint、namespace 
        properties.put(PropertyKeyConst.ENDPOINT, "${endpoint}");
        properties.put(PropertyKeyConst.NAMESPACE, "${namespace}");
        // 推荐使用 RAM 账号的 accessKey、secretKey，
        properties.put(PropertyKeyConst.ACCESS_KEY, "${accessKey}");
        properties.put(PropertyKeyConst.SECRET_KEY, "${secretKey}");
        ConfigService configService = NacosFactory.createConfigService(properties);
        // 发布配置
        boolean publishConfig = configService.publishConfig(dataId, group, content);
        LOGGER.info("publishConfig: {}", publishConfig);
        wait2Sync();
        // 查询配置
        String config = configService.getConfig(dataId, group, 5000);
        LOGGER.info("getConfig: {}", config);
        // 监听配置
        configService.addListener(dataId, group, new PropertiesListener() {
            @Override
            public void innerReceive(Properties properties) {
                LOGGER.info("innerReceive: {}", properties);
            }
        });
        // 更新配置
        boolean updateConfig = configService.publishConfig(dataId, group, "connectTimeoutInMills=3000");
        LOGGER.info("updateConfig: {}", updateConfig);
        wait2Sync();
        // 删除配置
        boolean removeConfig = configService.removeConfig(dataId, group);
        LOGGER.info("removeConfig: {}", removeConfig);
        wait2Sync();
        config = configService.getConfig(dataId, group, 5000);
        LOGGER.info("getConfig: {}", config);
    }
    private static void wait2Sync() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            // ignore
        }
    }
}
```

## 传参方式 {#section_thh_2vv_42b .section}

为帮助您快速入门，以上示例代码采用了以代码初始化参数的方式。但是实际生产中可能有不同环境（如不同的账号、地域或命名空间等），参数因环境而异，因此需要通过变量传入。为方便您配置入参、降低您的配置成本，ACM 提供了多种传参方式。

**说明：** EDAS 在发布环节自动注入，因此您无需采取任何操作。

|初始化参数|传参方法|
|-----|----|
|endpoint|优先级由高到低：1.  JVM 参数：`-Daddress.server.domain=xxx`
2.  环境变量：`address_server_domain=xxx`
3.  代码设置：详见以上示例代码

|
|namespace|优先级由高到低：1.  JVM 参数：`-Dtenant.id=xxx`
2.  代码设置：详见以上示例代码

|
|accessKey/secretKey|优先级由高到低：1.  文件方式：accessKey 和 secretKey 以 Properties 格式（满足 `public void java.io.Reader.Properties.load(Reader reader)` 方法）存储在 `-Dspas.identity` 指定文件中。如果不指定，则默认取 `/home/admin/.spas_key/<应用名>`文件（应用名以 `-Dproject.name` 指定）
2.  环境变量：`spas_accessKey=xxx spas_secretKey=xxx`
3.  代码设置：详见以上示例代码

|

