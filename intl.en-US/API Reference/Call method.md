# Call method {#concept_wr5_t1v_42b .concept}

This section describes how to request the ACM service.

## Address server domain {#section_imn_4bv_42b .section}

|Region|Address server domain|
|------|---------------------|
|Public network \(test\)|acm.aliyun.com|
|China East 1 \(Hangzhou\)|addr-hz-internal.edas.aliyun.com|
|North China 1 \(Qingdao\)|addr-qd-internal.edas.aliyun.com|
|China East 2 \(Shanghai\)|addr-sh-internal.edas.aliyun.com|
|North China 2 \(Beijing\)|addr-bj-internal.edas.aliyun.com|
|South China 1 \(Shenzhen\)|addr-sz-internal.edas.aliyun.com|

## Get ACM server list {#section_v4z_qbv_42b .section}

Retrieve the IP addresses of ACM server through Address Server, so that you can get configurations by sending requests to the server IP.

```
http://${Address_Server_Domain}:8080/diamond-server/diamond
```

Example:

```
curl http://acm.aliyun.com:8080/diamond-server/diamond
139.196.135.144
139.196.135.144
```

## Communication protocol {#section_hbf_vbv_42b .section}

Supports request communication using HTTP.

## Request methods {#section_ibf_vbv_42b .section}

Allows sending HTTP GET or POST requests. In the HTTP GET request, the parameters must be included in the request URL.

## Request parameters {#section_jbf_vbv_42b .section}

Each request must contain the common request parameters related to authentication and signatures, and specific request parameters of the specified operations.

## Character encoding {#section_kbf_vbv_42b .section}

Both requests and returned results are encoded using the GBK character set.

## Signature mechanism {#section_etm_zbv_42b .section}

The ACM service performs authentication on each access request. Therefore, each request being sent over HTTP protocol must contain signature information. By using the AccessKey and SecretKey, the ACM performs symmetric encryption to verify the request sender.

The AccessKey and SecretKey are issued to visitors by ACM. The AccessKey indicates the identity of the visitor. The SecretKey is the key used to encrypt and verify the signature string on the server. It must be kept strictly confidential and only be known to the ACM and the user.

## Signature algorithm {#section_ftm_zbv_42b .section}

The HMACSHA1 algorithm is used to generate signatures. The following are examples of Java and Shell signature algorithms.

-   Example of a Java signature algorithm

    ```
    public static void main(String[] args) throws Exception {
        String tenant = "tenant ";
        String group = "group ";
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
    Signature =
     HMAC-SHA1(QueryParam)
    ```

    QueryParam varies by different requests.

3.  As defined in RFC 2104, the preceding signature string is used to calculate the signature’s HMAC value. Note: The key used for signature calculation is the Access Key  Secret \(ASCII: 38\), the hash algorithm used is sha1.
4.  Encode the preceding HMAC value into a string based on Base64 encoding rules and you can get the signature value \(Signature\).
5.  Add this signature value to the request parameters as the Signature parameter to sign the request.

## Example {#section_zcq_kcv_42b .section}

This is an exmaple of constructing an ACM request with Shell.

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

