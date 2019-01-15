# Get configurations {#concept_u3j_mrw_42b .concept}

## Description {#section_dpg_1rw_42b .section}

It obtains configurations from ACM when the service starts.

```
public static String getConfig(String dataId, String group, long timeoutMs) throws ConfigException
```

## Request parameters {#section_x5s_brw_42b .section}

|Parameter|Parameter type|Description|
|---------|--------------|-----------|
|dataId|String|Configuration ID. Use a naming rule such as `package.class` \(for example `com.taobao.tc.refund.log.level`\) to ensure global uniqueness. We recommend that you indicate business meaning of the configuration in the “class” section. All characters must be in lower case. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 256 bytes.|
|group|String|Configuration group. We recommend that you use `product name: module name` \(for example `ACM: Test`\) to ensure the uniqueness. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 128 bytes.|
|timeout|String|Length of configuration read time-out \(in ms\). Recommended value: 3000.|

## Return values {#section_v5w_brw_42b .section}

|Parameter type|Description|
|--------------|-----------|
|String|Configuration value|

## Request example {#section_kjx_brw_42b .section}

```
try {
    // Initialize the configuration service. Retrieves the following parameters in console with sample code.
    ConfigService.init("${endpoint}", "${namespace}", "${accessKey}", "${secretKey}");
    // Actively retrieves configuration
    String content = ConfigService.getConfig("${dataId}", "${group}", 3000);
    System.out.println(content);
} catch (ConfigException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
}
```

## Exception {#section_u3l_b4s_lgb .section}

A `ConfigException` exception is thrown in case of a configuration read time-out or a network error.

