# Delete configurations {#concept_oz1_bsw_42b .concept}

## Description {#section_dpg_1rw_42b .section}

It deletes ACM configurations automatically with program to reduce operation and maintenance costs with automation.

**Note:** If the specified configuration exists, then it deletes the configuration. If the specified configuration doesn’t exist, then it returns a successful message.

```
public static boolean removeConfig(String dataId, String group) throws ConfigException
```

## Request parameters {#section_x5s_brw_42b .section}

|Parameter|Parameter Type|Description|
|---------|--------------|-----------|
|dataId|String|Configuration ID|
|group|String|Configuration group|

## Returned values {#section_v5w_brw_42b .section}

|Parameter type|Description|
|--------------|-----------|
|boolean|If the deletion is successful|

## Request example {#section_kjx_brw_42b .section}

```
try {
    // Initialize the configuration service. Retrieves the following parameters in console with sample code.
    ConfigService.init("${endpoint}", "${namespace}", "${accessKey}", "${secretKey}");
    // Actively retrieves configuration
    boolean isRemoveOk = ConfigService.removeConfig("${dataId}", "${group}");
    System.out.println(isRemoveOk);
} catch (ConfigException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
}
```

## Exception {#section_u3l_b4s_lgb .section}

A `ConfigException` exception is thrown in case of a configuration read time-out or a network error.

