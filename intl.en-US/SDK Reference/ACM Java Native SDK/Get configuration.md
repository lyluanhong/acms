# Get configuration {#concept_u3j_mrw_42b .concept}

## Description {#section_dpg_1rw_42b .section}

Obtains configurations from ACM when the service starts.

```
public static String getConfig(String dataId, String group, long timeoutMs) throws ConfigException
```

## Request parameters {#section_x5s_brw_42b .section}

|Parameter name|Parameter type|Description|
|--------------|--------------|-----------|
|dataId|string|Configuration ID. Use a naming rule like package.class \(for example, com.taobao.tc.refund.log.level\) to ensure global uniqueness. It is recommended to indicate business meaning of the configuration in the “class” section. All characters must be in lowercase. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 256 bytes.|
|group|string|Configuration group. We recommend that you use product name: module name \(for example ACM: Test\) to guarantee the uniqueness. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 128 bytes.|
|timeout |string|Length of configuration read time-out \(in ms\). Recommended value: 3000.|

## Return values {#section_v5w_brw_42b .section}

|Parameter type|Description|
|--------------|-----------|
|string|Configuration value|

## Request example {#section_kjx_brw_42b .section}

```
try {
    // Initialize the configuration service, and the console automatically obtains the following parameters through the sample code.
    ConfigService.init("${endpoint}", "${namespace}", "${accessKey}", "${secretKey}");
    // Actively get the configuration.
    String content = ConfigService.getConfig("${dataId}", "${group}", 3000);
    System.out.println(content);
} catch (ConfigException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
}
```

## 异常说明 {#section_snn_ksw_42b .section}

读取配置超时或网络异常，抛出 ConfigException 异常。

