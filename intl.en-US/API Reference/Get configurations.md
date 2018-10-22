# Get configurations {#concept_yhn_tcv_42b .concept}

## Description {#section_zdg_5cv_42b .section}

It gets configurations in ACM.

## Request type {#section_a2g_5cv_42b .section}

GET

## Request URL {#section_b2g_5cv_42b .section}

/Diamond-server/config. co

## Request parameters {#section_f2y_wcv_42b .section}

|名称|类型|是否必须|描述|
|--|--|----|--|
|tenant|string|是|租户信息，对应 ACM 的命名空间字段。|
|dataId|string|是|配置 ID。|
|group|string|是|配置分组。|

## Header parameters {#section_fpn_wlv_42b .section}

|名称|类型|是否必须|描述|
|--|--|----|--|
|Spas-AccessKey|string|是|accessKey 从 ACM 控制台获取。|
|timeStamp|string|是|请求时间毫秒字符串。|
|Spas-Signature|string|是|SpasSigner.sign\( Tenant+ group+ timeStamp, secretKey\) // 用秘钥对“租户+分组+时间戳”进行签名，签名算法为 HmacSHA1。加时间戳签名防止重放攻击，该签名有效期 60 秒。|

## Return parameters {#section_n3m_ycv_42b .section}

|Parameter type|Description|
|--------------|-----------|
|string|Configuration Value|

## 错误编码 {#section_amp_4tv_42b .section}

|错误代码|描述|语义|
|----|--|--|
|400|Bad Request|客户端请求中的语法错误|
|403|Forbidden|没有权限|
|404|Not Found|客户端错误，未找到|
|500|Internal Server Error|服务器内部错误|
|200|OK|正常|

## Example {#section_jyd_1dv_42b .section}

-   Request example

    ```
    http:serverIp:8080/diamond-server/config.co? dataId=dataIdparam&group=groupParam&tenant=tenantParam
    ```

-   Response example

    ```
    contentTest
    ```


