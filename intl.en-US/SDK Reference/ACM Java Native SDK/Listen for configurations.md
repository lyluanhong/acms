# Listen for configurations {#concept_wrp_zqw_42b .concept}

## Description {#section_dpg_1rw_42b .section}

If you want ACM to push configuration changes, you can use the ACM dynamic listener configuration interface.

```
public static void addListener(String dataId, ConfigChangeListenerAdapter listener)
```

## Request parameters {#section_x5s_brw_42b .section}

|Parameter name|Parameter type|Description|
|--------------|--------------|-----------|
|dataId|string|Configuration ID. Use a naming rule like package.class \(for example, com.taobao.tc.refund.log.level\) to ensure global uniqueness. It is recommended to indicate business meaning of the configuration in the “class” section. All characters must be in lowercase. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 256 bytes.|
|group|string|Configuration group. We recommend that you use **product name: module name** \(for example ACM: Test\) to guarantee the uniqueness. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 128 bytes.|
|listener|ConfigChangeListener|Listener. Configuration changes go into the callback function of the listener.|

## Returned values {#section_v5w_brw_42b .section}

|Parameter type|Description|
|--------------|-----------|
|string|Configuration value. This value is returned through the callback function during initialization or configuration modification.|

## Request example {#section_kjx_brw_42b .section}

```
// Initialize the configuration service, and the console automatically obtains the following parameters through the sample code.
Configservice. init ("$ {endpoint}", "$ {namespace }", "$ {accesskey}", "$ {ridgekey }");
// Add listeners to the configuration during initialization, which calls back a notification of configuration changes.
Configservice. addlistener ("$ {dataid}", "$ {group}", new fig () {
    public void receiveConfigInfo(String configInfo) {
        // After the configuration is updated, the latest value is returned to the user by this callback function.
        System.out.println(configInfo);
    }
});
// Keep the main thread alive throughout the test, because the configuration subscription runs in a daemon thread, which exits once the main thread exits. The following code is not required in a real environment.
while (true) {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

