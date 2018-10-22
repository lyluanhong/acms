# Listen to configurations {#concept_k3y_qdv_42b .concept}

## Description {#section_zdg_5cv_42b .section}

Capture configuration changes in real time by listening to configurations on ACM.  In case of any configuration changes, you can use the [Get Configurations](https://help.aliyun.com/document_detail/64131.html) API to obtain the latest value of the configuration and dynamically refresh the local cache.

Registered listening adopts asynchronous Servlet technique. Registered listening is essentially to compare the MD5 with configurations and configuration values to that in the backend.  If the MD5 values don’t match, then it immediately returns the different configuration.  If they match, it holds for 30 seconds.  And it returns an empty string.

## Request type {#section_a2g_5cv_42b .section}

POST

## Request URL {#section_b2g_5cv_42b .section}

/diamond-server/config.co

## Request parameters {#section_f2y_wcv_42b .section}

|Name |Type|Required|Description|
|-----|----|--------|-----------|
|Probe-Modify-Request|string|Yes|Listen to data messages. The format is dataId^2group^2contentMD5^2tenant^1.-   dataId: Configuration ID
-   group: Configuration group
-   contentMD5: MD5 of configuration content
-   tenant: The tenant, corresponding to the namespace field of ACM

|

## Header parameters {#section_m34_xcv_42b .section}

|Name|Type|Required|Description|
|----|----|--------|-----------|
|longPullingTimeout|string|Yes|If the long pulling timeout is 30 seconds, then enter 30000 here.|
|Spas-Accesskey|string|Yes|The accessKey can be found in the ACM console.|
|timeStamp|string|Yes|Request time string in millisecond.|
|Spas-Signature|string|Yes| SpasSigner.sign \( Probe- Modify- -RequestContent, secretKey\) // Sign the tenant, group, and timestamp with a key using the signature algorithm HMACSHA1.  The timestamp is signed to prevent replay attacks. The signature is valid for 60s.|

## Parameter description {#section_rv2_wdv_42b .section}

-   A delimiter to separate fields within a configuration: ^2 = Character.toString\(\(char\) 2
-   A delimiter to separate configurations: ^1 = Character.toString\(\(char\) 1\)
-   contentMD5: MD5\(content\). This is an empty string because the first local cache is empty.

## Response parameters {#section_n3m_ycv_42b .section}

|Parameter type|Description|
|--------------|-----------|
|string|Configuration value|

## 错误编码 {#section_amp_4tv_42b .section}

|错误代码|描述|语义|
|----|--|--|
|400|Bad Request|客户端请求中的语法错误|
|403|Forbidden|没有权限|
|404|Not Found|客户端错误，未找到|
|500|Internal Server Error|服务器内部错误|
|200|OK|正常|

## Examples {#section_jyd_1dv_42b .section}

-   Request example

    ```
    http://serverIp: 8080/diamond-server/config.co
    POST request body data:
    Probe- Modify- Request=dataId^2group^2contentMD5^2tenant^1
    ```

-   Return example

    ```
    In case of any configuration changes
    dataId^2group^2tenant^1 
    If no configuration changes: an empty string is returned
    ```


