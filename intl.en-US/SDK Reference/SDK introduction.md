# SDK introduction {#concept_lz3_5tv_42b .concept}

You can use ACM SDK to get and listen to configurations.

Available ACM SDKs include:

-   ACM Java Native SDK: A Java native SDK for listening to and updating ACM configurations.
-   Spring Cloud ACM: Java SDK that supports the Spring Cloud Config interface specification. This SDK won't be maintained any more. Therefore, we recommend that you use [spring-cloud-starter-alibaba-nacos-config](reseller.en-US//Nacos Spring Cloud.md#) from Nacos instead.
-   ACM Node.js SDK: A Node.js native SDK for listening to and updating ACM configurations.
-   ACM C++ SDK: A C++ native SDK for listening to and updating ACM configurations.
-   ACM Python \(open-source\): A Python native SDK for listening to and updating ACM configurations.
-   ACM PHP \(open-source\): A PHP native SDK for listening to ACM configurations.
-   Nacos Client: A Java native SDK for listening to and updating ACM configurations.

The functions and features of ACM SDKs are summarized in the following table:

|Function/Language|Java Native|Java Spring Cloud|Python|Node.JS|C++|PHP|Nacos SDK|
|-----------------|-----------|-----------------|------|-------|---|---|---------|
|Get particular configurations|Supported|Supported|Supported|Supported|Supported|Supported|Supported|
|Listen to particular configurations|Supported|Supported|Supported|Supported|Supported|Unsupported|Supported|
|Write configurations|Supported|Unsupported|Unsupported|Unsupported|Unsupported|Supported|Supported|
|Enumerate configurations under particular tenants|Supported|Unsupported|Unsupported|Unsupported|Unsupported|Unsupported|Unsupported|
|Support connecting server with Single IP solution|Supported|Unsupported|Supported|Unsupported|Unsupported|Unsupported|Supported|
|Support connecting server with multiple IP LB method\*|Supported|Supported|Supported|Supported|Supported|Supported|Supported|
|Support user authentication with HmacSHA1 algorithm|Supported|Supported|Supported|Supported|Supported|Supported|Supported|
|Support local cache backup\*\*|Supported|Supported|Supported|Supported|Supported|Unsupported|Supported|
|[Support ECS instance RAM role authentication](../../../../../reseller.en-US/User Guide/Access ACM with instance RAM role.md#)|Supported|Supported|Supported|Unsupported|Unsupported|Unsupported|Supported|
|Open-source address|Planned|Planned|[acm-sdk-python](https://github.com/alibaba/acm-sdk-python)|Planned|Planned|[acm-sdk-php](https://github.com/alibaba/acm-sdk-php)|[Nacos](https://github.com/alibaba/nacos)|

-   \* Multiple IP LB method is a LoadBalance method based on multiple server IP addresses returned by ACM SDK from the address server, which boosts performance and achieves high availability.
-   \*\* With local cache backup, ACM SDK can read from the local cache backup file saved when getting configurations last time, which avoids client downtime.
-   \*\*\* `spring-cloud-starter-acm` won't be maintained any more. Therefore, we recommend that you use [spring-cloud-starter-alibaba-nacos-config](reseller.en-US//Nacos Spring Cloud.md#) from Nacos instead.

