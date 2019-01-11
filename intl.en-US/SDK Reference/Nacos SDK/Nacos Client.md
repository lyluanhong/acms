# Nacos Client {#concept_hwf_kmt_4fb .concept}

This topic explains how to use Nacos Client SDK.

## Get the SDK {#section_tln_cvv_42b .section}

To get Nacos Client SDK, add the following configuration to the pom.xml file in the Maven project.

```
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>${latest.version}</version>
</dependency>
<! -- Remove the following if logging implementation is available. -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

## Example {#section_kvv_bnt_4fb .section}

Run the following code in your project for configuration management.

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
 * Sample code, for sample test only.
 */
public class NacosExample {
    private static final Logger LOGGER = LoggerFactory.getLogger(NacosExample.class);
    public static void main(String[] args) throws NacosException {
        Properties properties = new Properties();
        String dataId = "com.alibaba.nacos.example";
        String group = "DEFAULT_GROUP";
        String content = "connectTimeoutInMills=5000";
        // Copy endpoint and namespace from the namespace details on the namespaces page of the console 
        properties.put(PropertyKeyConst.ENDPOINT, "${endpoint}");
        properties.put(PropertyKeyConst.NAMESPACE, "${namespace}");
        // We recommend that you use the accessKey and secretKey of the RAM account.
        properties.put(PropertyKeyConst.ACCESS_KEY, "${accessKey}");
        properties.put(PropertyKeyConst.SECRET_KEY, "${secretKey}");
        ConfigService configService = NacosFactory.createConfigService(properties);
        // Publish configuration
        boolean publishConfig = configService.publishConfig(dataId, group, content);
        LOGGER.info("publishConfig: {}", publishConfig);
        wait2Sync();
        // Query configuration
        String config = configService.getConfig(dataId, group, 5000);
        LOGGER.info("getConfig: {}", config);
        // Listen for configuration changes
        configService.addListener(dataId, group, new PropertiesListener() {
            @Override
            public void innerReceive(Properties properties) {
                LOGGER.info("innerReceive: {}", properties);
            }
        });
        // Update configuration
        boolean updateConfig = configService.publishConfig(dataId, group, "connectTimeoutInMills=3000");
        LOGGER.info("updateConfig: {}", updateConfig);
        wait2Sync();
        // Delete configuration
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

## Options of passing parameters {#section_thh_2vv_42b .section}

To help you get onboard, the sample code initializes the parameters with code. However, the real production process may involve different environments \(namely different accounts, regions, or namespaces\), and the parameters vary with environments, so you must use variables to pass the parameters. To facilitate the input parameter configuration and reduce the configuration cost, ACM provides multiple options of passing parameters.

**Note:** EDAS automatically injects for you during the publishing, and therefore you don’t need to take any action.

|Initialization parameters|How to pass parameters|
|-------------------------|----------------------|
|endpoint|From highest priority to lowest priority:1.  By JVM parameters: `-Daddress.server.domain=xxx`
2.  By environmental variables: `address_server_domain=xxx`
3.  By code: see the preceding sample code

|
|namespace|From highest priority to lowest priority:1.  By JVM parameters:`-Dtenant.id=xxx`
2.  By code: see the preceding sample code

|
|accessKey/secretKey|From highest priority to lowest priority:1.  By files: store accessKey and secretKey in the format of Properties \(which meets the requirement of the `public void java.io.Reader.Properties.load(Reader reader)` method\) in a file specified with `-Dspas.identity`. If not specified, then the `/home/admin/.spas_key/<ApplicationName>` file is used by default \(the application name is specified with `-Dproject.name`\)
2.  Environment variable: `spas_accessKey=xxx spas_secretKey=xxx`
3.  By code: see the preceding sample code

|

