# Prerequisites {#concept_upt_bvv_42b .concept}

This topic explains how to use the ACM API with sample code.

## Get the SDK {#section_tln_cvv_42b .section}

Enter the following POM configuration to establish API dependency on the SDK.

```
&lt;dependency>
    &lt;groupId>com.alibaba.edas.acm&lt;/groupId>
    &lt;artifactId>acm-sdk&lt;/artifactId>
    &lt;version>1.0.8&lt;/version>
&lt;/dependency>
&lt;! -- Remove the following if logging implementation is available. -->
&lt;dependency>
    &lt;groupId>ch.qos.logback&lt;/groupId>
    &lt;artifactId>logback-classic&lt;/artifactId>
    &lt;version>1.1.7&lt;/version>
&lt;/dependency>
```

## Sample code { .section}

```
import java.util.properties;
import com.alibaba.edas.acm.ConfigService;
import com.alibaba.edas.acm.exception.ConfigException;
import com.alibaba.edas.acm.listener.ConfigChangeListener;
import com.alibaba.edas.acm.listener.PropertiesListener;
// Sample code, for sample test only.
public class ACMTest {
    // Attribute/Switch
     private static String config = "DefaultValue";
    Private static properties acmproperties = new properties ();
    public static void main(String[] args) {
        try {
            // Copy the corresponding values from the "Namespace Management" in the console.
            Properties properties = new Properties();
            Properties. put ("endpoint", "$ endpoint ");
            Properties. put ("namespace", "$ namespace ");
            // Access ACM with instance RAM role
            // Properties. Put ("ramrolename", "$ ramrolename ");
            Properties.put("accessKey", "$accessKey");
            properties.put("secretKey", "$secretKey");
            // If it is an encrypted configuration, then add the following two lines for automatic decryption.
            //properties.put("openKMSFilter", true);
            //properties.put("regionId", "$regionId");
            ConfigService.init(properties);
            // Actively get the configuration.
            String content = ConfigService.getConfig("${dataId}", "${group}", 6000);
            System.out.println(content);
            // Add listeners to the configuration during initialization, which calls back a notification when the configuration is changed.
            ConfigService.addListener("${dataId}", "${group}", new ConfigChangeListener() {
                public void receiveConfigInfo(String configInfo) {
                    // After the configuration is updated, the latest value is returned to the user via this callback function.
                    // Remember not to make blocking operations in callback function. Otherwise the notification thread will be blocked.
                    config = configInfo;
                    System.out.println(configInfo);
                }
            });
            /**
             * The following listener can be used if the content of the configuration value is in properties (key=value) format.  This allows you to manage multiple configuration items in one configuration.
             */
            /**
            ConfigService.addListener("${dataId}", "${group}", new PropertiesListener() {
                @Override
                public void innerReceive(Properties properties) {
                    // TODO Auto-generated method stub
                    acmProperties = properties;
                    System.out.println(properties);
                }
            });
            **/
        } catch (ConfigException e) {
            e.printStackTrace();
        }
        // Keep the main thread alive throughout the test, because the configuration subscription runs in a daemon thread, which exits once the main thread exits.  The following code is not required in a real environment.
        while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    // Expose the configuration value via the get configuration API for external use.
    public static String getConfig() {
        return config;
    }
    // Expose the configuration value via the get configuration API for external use.
    public static Object getPorpertiesValue(String key) {
        If (acmproperties! = null) {
            return acmProperties.get(key);
        }
        return null;
    }
}
```

## Options of passing parameters {#section_thh_2vv_42b .section}

To help you get onboard, the sample code initializes the parameters with code. However, the real production process may involve different environments \(namely different accounts, regions, or namespaces\), and the parameters vary with environments, so you must use variables to pass the parameters.  To facilitate the input parameter configuration and reduce the configuration cost, ACM provides multiple options of passing parameters.

**Note:** EDAS automatically injects for you during the publishing, and therefore you don’t need to take any action.

|Initialization parameters|How to pass parameters|
|-------------------------|----------------------|
|endpoint|From highest priority to lowest priority:1.  By JVM parameters:`-Daddress.server.domain=xxx`
2.  By environmental variables: `address_server_domain=xxx`
3.  By code: see the preceding sample code

|
|namespace|From highest priority to lowest priority:1.  By JVM parameters:`-Dtenant.id=xxx`
2.  By code: see the preceding sample code

|
|ramRoleName|Note: The authorization priority is higher than the accesskey/ridgekey priority from high to low1.  By JVM parameters: `-Dram.role.name=xxx`
2.  By code: see the preceding sample code

|
|accessKey/secretKey|From highest priority to lowest priority:1.  By files: store accessKey and secretKey in the format of Properties \(which meets the requirement of the `public void java.io.Reader.Properties.load(Reader reader)` method\) in a file specified with `-Dspas.identity`. If not specified, then the `/home/admin/.spas_key/&lt;ApplicationName>` file is used by default \(the application name is specified with `-Dproject.name`\)
2.  Environment variable: `spas_accessKey=xxx spas_secretKey=xxx`
3.  By code: see the preceding sample code

|

## Related documents {#section_egh_gvv_42b .section}

-   [Access ACM with instance RAM role](../../../../reseller.en-US/User Guide/Access ACM with instance RAM role.md#)

