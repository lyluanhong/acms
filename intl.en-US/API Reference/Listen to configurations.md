# Listen to configurations {#concept_k3y_qdv_42b .concept}

This topic explains how to use API to listen to configurations in ACM.

## Description {#section_zdg_5cv_42b .section}

Capture configuration changes in real time by listening to configurations on ACM. In case of any configuration changes, you can use the [Get configurations](reseller.en-US/API Reference/Get configurations.md#) to obtain the latest value of the configuration and dynamically refresh the local cache.

Registered listening adopts asynchronous Servlet technique. Registered listening is essentially to compare the MD5 with configurations and configuration values to that in the backend. If the MD5 values don’t match, then it immediately returns the different configuration. If they match, it holds for 30 seconds. And it returns an empty string.

## Request type {#section_a2g_5cv_42b .section}

POST

## Request URL {#section_b2g_5cv_42b .section}

/diamond-server/config.co

## Request parameters {#section_f2y_wcv_42b .section}

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|Probe-Modify-Request|String|Yes|Listen to data messages. The format is `dataId^2group^2contentMD5^2tenant^1`.-   dataId: Configuration ID
-   group: Configuration group
-   contentMD5: MD5 of the configuration content
-   tenant: The tenant, corresponding to the namespace field of ACM

|

## Header parameters {#section_m34_xcv_42b .section}

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|longPullingTimeout|String|Yes|If the long pulling timeout is 30 seconds, then enter 30000 here.|
|Spas-AccessKey|String|Yes|The accessKey can be found in the ACM console.|
|timeStamp|String|Yes|The request time in milliseconds|
|Spas-Signature|String|Yes|`SpasSigner.sign(Tenant+ group+ timeStamp, secretKey)`. Sign "tenant + group + timestamp" with secret key. The signature algorithm is HmacSHA1. The timestamp signature prevents replay attacks. The signature is valid for 60 seconds.|

## Parameter description {#section_rv2_wdv_42b .section}

-   A delimiter to separate fields within a configuration: `^2 = Character.toString((char) 2`
-   A delimiter to separate configurations: `^1 = Character.toString((char) 1)`
-   contentMD5: MD5\(content\). This is an empty string because the first local cache is empty.

## Response parameters {#section_n3m_ycv_42b .section}

|Parameter type|Description|
|--------------|-----------|
|String|Configuration value|

## Error code {#section_qxw_hpr_lgb .section}

|Error code|Error message|Explanation|
|----------|-------------|-----------|
|400|Bad Request|Syntax error in client request|
|403|Forbidden|No permission|
|404|Not Found|Client error, not found|
|500|Internal Server Error|Internal errors of the server|
|200|OK|Normal|

## Examples {#section_jyd_1dv_42b .section}

-   Request example

    ```
    http://serverIp:8080/diamond-server/config.co
    POST request body data:
    Probe-Modify-Request=dataId^2group^2contentMD5^2tenant^1
    ```

-   Response example

    ```
    In case of any configuration changes
    dataId^2group^2tenant^1
    If no configuration changes: an empty string is returned
    ```


