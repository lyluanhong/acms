# Get the configurations within a namespace {#concept_abb_3dv_42b .concept}

This topic explains how to use API to get configurations from ACM.

## Description {#section_zdg_5cv_42b .section}

It gets the configurations within a namespace from ACM.

## Request type {#section_a2g_5cv_42b .section}

GET

## Request URL {#section_b2g_5cv_42b .section}

/diamond-server/basestone.do? method=getAllConfigByTenant

## Request parameters {#section_f2y_wcv_42b .section}

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|tenant|String|Yes|The tenant, corresponding to the namespace field of ACM|
|pageNo|int|Yes|Page number|
|pageSize|int|Yes|Page size|

## Header parameters {#section_m34_xcv_42b .section}

|Name|Type|Required|Description|
|----|----|--------|-----------|
|Spas-AccessKey|String|Yes|The accessKey can be found in the ACM console.|
|timeStamp|String|Yes|The request time in milliseconds|
|Spas-Signature|String|Yes|`SpasSigner.sign(Tenant+ group+ timeStamp, secretKey)`. Sign "tenant + group + timestamp" with secret key. The signature algorithm is HmacSHA1. The timestamp signature prevents replay attacks. The signature is valid for 60 seconds.|
|Spas-SecurityToken|String|No|SecurityToken is obtained from STS temporary credential. STS temporary credential is obtained from instance metadata URL. For more information, see:
|

## Response parameters {#section_n3m_ycv_42b .section}

|Parameter type|Description|
|--------------|-----------|
|totalCount|The total number of configurations|
|pageNumber|Page number|
|pagesAvailable|The number of available pages|
|pageItems|Configuration items|

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
    http:serverIp:8080/diamond-server/basestone.do? method=getAllConfigByTenant&amp;tenant=tenantParam&amp;pageNumber=pageNumberParam&amp;pageSize=pageSizeParam
    ```

-   Response example

    ```
    contentTest
    ```


