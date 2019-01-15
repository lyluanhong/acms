# How to request ACM services {#concept_wr5_t1v_42b .concept}

This topic explains how to request ACM services.

## Domain name of the address servers {#section_imn_4bv_42b .section}

|Region|Domain name of the address server|
|------|---------------------------------|
|Internet|acm.aliyun.com|
|China \(Hangzhou\)|addr-hz-internal.edas.aliyun.com|
|China \(Qingdao\)|addr-qd-internal.edas.aliyun.com|
|China \(Shanghai\)|addr-sh-internal.edas.aliyun.com|
|China \(Beijing\)|addr-bj-internal.edas.aliyun.com|
|China \(Shenzhen\)|addr-sz-internal.edas.aliyun.com|
|China \(Hong Kong\)|addr-hk-internal.edas.aliyuncs.com|
|Singapore|addr-singapore-internal.edas.aliyun.com|
|Australia \(Sydney\)|addr-ap-southeast-2-internal.edas.aliyun.com|
|US \(Silicon Valley\)|addr-us-west-1-internal.acm.aliyun.com|
|US \(Virginia\)|addr-us-east-1-internal.acm.aliyun.com|
|China \(Shanghai\) Financial Cloud|addr-cn-shanghai-finance-1-internal.edas.aliyun.com|

## Get ACM server list {#section_v4z_qbv_42b .section}

Retrieve the IP addresses of ACM server through Address Server, so that you can get configurations by sending requests to the server IP.

```
http://${Address_Server_Domain}:8080/diamond-server/diamond
```

For example:

```
curl http://acm.aliyun.com:8080/diamond-server/diamond
139.196.135.144
```

## Communication protocols {#section_hbf_vbv_42b .section}

Supports request communication using HTTP.

## Request method {#section_ibf_vbv_42b .section}

Allows sending HTTP GET or POST requests. In the HTTP GET request, the parameters must be included in the request URL.

## Request parameters {#section_jbf_vbv_42b .section}

Each request must contain the request parameters related to public authentication and signatures, as well as the specific operation-related parameters.

## Character encoding {#section_kbf_vbv_42b .section}

Both requests and returned results are encoded using the GBK character set.

## Signature mechanism {#section_etm_zbv_42b .section}

The ACM service performs authentication on each access request. Therefore, each request being sent over HTTP protocol must contain signature information. By using the AccessKey and SecretKey, the ACM performs symmetric encryption to authenticate the request sender.

The accessKey and the secretKey are issued to the visitor by ACM. The accessKey is used for authenticating the visitor, and the secretKey is the key that encrypts the signature string and then validates it on the server. Only you and ACM know them, and they must remain strictly confidential.

## Signature algorithm {#section_ftm_zbv_42b .section}

The HMACSHA1 algorithm is used for signing. The following describes the examples of a Java and Shell signature algorithms.

-   Example of a Java signature algorithm

    ```
    public static void main(String[] args) throws Exception {
        String tenant= "tenant";
        String group = "group";
        String timeStamp = String.valueOf(System.currentTimeMillis());
        String abc = HmacSHA1Encrypt(tenant+ "+" + group + "+" + timeStamp , "1234");
        System.out.println(abc);
    }
    public static String HmacSHA1Encrypt(String encryptText, String encryptKey) throws Exception {
        byte[] data = encryptKey.getBytes("UTF-8");
        // Construct a key based on the given byte array and specify the name of a key algorithm in the second parameter.
        SecretKey secretKey = new SecretKeySpec(data, "HmacSHA1");
        // Generate a Mac object for the specified Mac algorithm
        Mac mac = Mac.getInstance("HmacSHA1");
        // Initialize the Mac object with the given key
        mac.init(secretKey);
        byte[] text = encryptText.getBytes("UTF-8");
        byte[] textFinal = mac.doFinal(text);
        // Complete the Mac operation and Base64 encoding. Convert the byte array to a string.
        return new String(Base64.encodeBase64(textFinal));
    }
    ```

-   Shell signature algorithm

    ```
    ## config sign
    timestamp=`echo $[$(date +%s%N)/1000000]`
    signStr=$namespace+$group+$timestamp
    signContent=`echo -n $signStr | openssl dgst -hmac $sk -sha1 -binary | base64`
    echo $signContent
    ```


## Signature procedure {#section_aky_bcv_42b .section}

1.  Use request parameters to construct a canonicalized query string \(QueryParam\).
2.  Follow the subsequent rules to construct the string for signature calculation using the canonicalized query string constructed in the previous step.

    ```
    Signature=
     HMAC-SHA1(QueryParam)
    ```

    **Note:** The QueryParam varies with requests.

3.  Use the preceding signature sting to calculate the signature’s HMAC value based on RFC2104 definitions. Note: The key used for signature calculation is the Access Key Secret held by the user \(ASCII:38\), and the hash algorithm used is SHA1.
4.  According to Base64 encoding rules, encode the preceding HMAC value, which gives you the signature value.
5.  Add the obtained signature value to the request parameters as the “Signature” parameter, which completes the request signing process.

## Code examples {#section_zcq_kcv_42b .section}

This is an example of constructing an ACM request with Shell.

```
#! /bin/bash
## config param
dataId="xxx"
group="xxx"
namespace="xxx"
accessKey="xxx"
secretKey="xxx"
endpoint="xxx"
## config param end
## get serverIp from address server
serverIp=`curl $endpoint:8080/diamond-server/diamond -s | awk '{a[NR]=$0}END{srand();i=int(rand()*NR+1);print a[i]}'`
## config sign
timestamp=`echo $[$(date +%s%N)/1000000]`
signStr=$namespace+$group+$timestamp
signContent=`echo -n $signStr | openssl dgst -hmac $secretKey -sha1 -binary | base64`
## request to get a config
curl -H "Spas-AccessKey:"$accessKey -H "timeStamp:"$timestamp -H "Spas-Signature:"$signContent "http://"$serverIp":8080/diamond-server/config.co?dataId="$dataId"&group="$group"&tenant="$namespace -v
```

