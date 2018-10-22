# SDK introduction {#concept_lz3_5tv_42b .concept}

You can use ACM SDK to get and listen to configurations.

Available ACM SDKs include:

-   ACM Java Native SDK: A Java native SDK for listening to and updating ACM configurations.
-   Spring Cloud ACM: A Java SDK that is encapsulated based on the ACM Java Native SDK and compliant with the Spring Cloud Config API specifications.
-   ACM Node.js SDK: A Node.js native SDK for listening to and updating ACM configurations.
-   ACM C++ SDK: A C++ native SDK for listening to and updating ACM configurations.
-   ACM Python \(open-source\): A Python native SDK for listening to and updating ACM configurations.
-   ACM PHP \(open-source\): A PHP native SDK for listening to ACM configurations.

The functions and features of ACM SDKs are summarized in the following table:

|Function/Language|Java Native|Java Spring Cloud|Python|Node. js|C++|PHP|
|-----------------|-----------|-----------------|------|--------|---|---|
|Get particular configurations|Supported|Supported|Supported|Supported|Supported|Supported|
|Listen to particular configurations|Supported|Supported|Supported|Supported|Supported|Not supported|
|Write configurations|Supported|Not supported|Not supported|Not supported|Not supported|Supported|
|Enumerate configurations under particular tenants|Supported|Not supported|Not supported|Not supported|Not supported|Not supported|
|Support connecting server with Single IP solution|Supported|Not supported|Supported|Not supported|Not supported|Not supported|
|Support connecting server with multiple IP LB method\*|Supported|Supported|Supported|Supported|Supported|Supported|
|Support user authentication with HmacSHA1 algorithm|Supported|Supported|Supported|Supported|Supported|Supported|
|Support local cache backup|Supported|Supported|Supported|Supported|Supported|Not supported|
|Open-source address|Planned|Planned|[acm-sdk-python](https://github.com/alibaba/acm-sdk-python)|Planned|Planned|[ acm-sdk-php](https://github.com/alibaba/acm-sdk-php)|

-   \* Multiple IP LB method is a LoadBalance method based on multiple server IP addresses returned by ACM SDK from the address server, which boosts performance and achieves high availability.
-   \*\* With local cache backup, ACM SDK can read from the local cache backup file saved when getting configurations last time, which avoids client downtime.

