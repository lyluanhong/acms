# Publish configuration {#concept_oh5_5rw_42b .concept}

## Description {#section_dpg_1rw_42b .section}

It publishes ACM configurations automatically with program to reduce operation and maintenance costs with automation.

**Note:** It uses the same publishing interface to create or modify a configuration. If the specified configuration doesn’t exist, then it creates a configuration. If the specified configuration exists, then it updates the configuration.

```
public static boolean publishConfig(String dataId, String group, String content) throws ConfigException
```

## Request parameters {#section_x5s_brw_42b .section}

|Parameter name|Parameter type|Description|
|--------------|--------------|-----------|
|dataId|string|Configuration ID. Use a naming rule like `package.class` \(for example, `com.taobao.tc.refund.log.level`\) to ensure global uniqueness. It is recommended to indicate business meaning of the configuration in the “class” section. All characters must be in lower case.  Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 256 bytes.|
|group|string|Configuration group. We recommend that you use `product name: module name` \(for example `ACM: Test`\) to guarantee the uniqueness. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 128 bytes.|
|content|string|Configuration content. It must not exceed 100K bytes.|

## Returned values {#section_v5w_brw_42b .section}

|Parameter type|Description|
|--------------|-----------|
|boolean|If the publishing is successful|

## Request example {#section_kjx_brw_42b .section}

```
try {
    // Initialize the configuration service. Retrieves the following parameters in console with sample code
    ConfigService.init("${endpoint}", "${namespace}", "${accessKey}", "${secretKey}");
    // Actively retrieves configuration
    boolean isPublishOk = ConfigService.publishConfig("${dataId}", "${group}", "${content}");
    System.out.println(isPublishOk);
} catch (ConfigException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
}
```

## 异常说明 {#section_snn_ksw_42b .section}

读取配置超时或网络异常，抛出 ConfigException 异常。

