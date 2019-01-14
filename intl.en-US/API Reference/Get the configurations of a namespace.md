# Get the configurations of a namespace {#concept_abb_3dv_42b .concept}

## Description {#section_zdg_5cv_42b .section}

Get the configurations within a namespace from ACM.

## Request Type {#section_a2g_5cv_42b .section}

GET

## Request URL {#section_b2g_5cv_42b .section}

/diamond-server/basestone.do? method=getAllConfigByTenant

## Request parameters {#section_f2y_wcv_42b .section}

|Name|Type|Required|Description|
|----|----|--------|-----------|
|tenant|String|Yes|The tenant, corresponding to the namespace field of ACM|
|pageNo|int|Yes|Page number|
|pageSize|int|Yes|Page size|

## Header parameters {#section_m34_xcv_42b .section}

|Name|Type|Required|Description|
|----|----|--------|-----------|
|Spas-AccessKey|String|Yes|The accessKey can be found in the ACM console.|
|timeStamp|String|Yes|The request time in milliseconds|
|Spas-Signature|String|Yes|SpasSigner.sign \(Tenant+ group+ timeStamp, secretKey\) // Sign "tenant + group + timestamp" with secret key. The signature algorithm is HmacSHA1. The timestamp signature prevents replay attacks. The signature is valid for 60 seconds.|
|Spas-SecurityToken|String|No|SecurityToken is obtained from STS temporary credential. STS temporary credential is obtained from instance metadata URL.|

## Response parameters {#section_n3m_ycv_42b .section}

|Parameter Type|Description|
|--------------|-----------|
|totalCount|The total number of configurations|
|pageNumber|Page number|
|pagesAvailable|The number of available pages|
|pageItems|Configuration items|

## Error code {#sc_error_code .section}

|Error code|Description|Explanation|
|----------|-----------|-----------|
|400|Bad Request|Syntax error in client request|
|403|Forbidden|No permission|
|404|Not Found|Client error, not found|
|500|Internal Server Error|Internal errors of the server|
|200|OK|Normal|

## Examples {#section_jyd_1dv_42b .section}

-   Request example

    ```
    http:serverIp: 8080/diamond-server/basestone.do? method=getAllConfigByTenant&amp;tenant=tenantParam&amp;pageNumber=pageNumberParam&amp;pageSize=pageSizeParam
    ```

-   Response example

    ```
    contentTest
    ```


