# Release note {#concept_ndm_pkp_42b .concept}

## V4.5.0 {#section_zmq_4lp_42b .section}

**Released date:** 2018.06.22

**New features:**

-   Supports fine-granularity authorization with RAM: makes configuration management more secure by effectively avoiding configuration misoperations or data leakage. [\[Related documents\]](https://help.aliyun.com/document_detail/85118.html)

## V4.4.0 {#section_kgg_qkp_42b .section}

**Released date:** 2018.05.18

**New features:**

-   Support of ECS instance RAM roles: allows applications to access ACM configurations on ECS without setting any sensitive information \(for example AK/SK\), which solves cloud application security compliance issues. \[[Related document](https://help.aliyun.com/document_detail/72013.html)\]

**Optimization and improvements:**

-   Fixed the compatibility issue with EDAS.

## V4.3.0 {#section_ogg_qkp_42b .section}

**Released date:** 2018.04.08

**New features:**

-   Supports encrypted configuration: by integrating with KMS \(Key Management Service\), you can encrypt your configurations on ACM to make sure they are secure.
-   Supports RAM \(Resource Access Management\) policies: with RAM, you can authorize sub-accounts, service accounts, and other primary accounts to read your configurations.
-   Supports capacity management policies: based on capacity management policies of fine granularity, you can now write your own configurations directly with API or SDK.
-   Supports importing, exporting, and cloning of configurations: you can conveniently export your configurations from one region and namespace to a specified region and namespace.

**Optimization and improvements:**

-   Fixed the compatibility issue with EDAS.

## V4.2.0 {#section_rgg_qkp_42b .section}

**Released date:** 2018.02.01

**New features**

-   Supports adding tags and descriptions for configurations: makes it easier for you to manage configurations your own way and get more information about them at a glance.
-   Enhanced configuration editing capability: supports online editing of YAML, HTML, and other formats, and supports saving configuration formats.
-   Provides Open API and supports Shell language: now you can implement multi-language extension with ease or retrieve configurations in DevOps scenarios.
-   Supports retrieving the configuration list under a tenant.
-   Supports both Chinese and English.
-   Supports downloading standalone version.

**Optimization and improvements:**

-   Optimized namespace components.
-   Solved conflicts with EDAS.
-   Reduced the size of SDK to be sent to the public warehouse, and reduced the JAR conflicts.

## V4.1.0 {#section_ugg_qkp_42b .section}

**Released date:** 2017.12.10

**New features:**

-   Client supports Node.js and C++.
-   Supports internationalization, including Chinese and English versions.
-   Supports deleting namespaces and viewing their details.
-   Supports Group suggestion.
-   Shares tenant data with EDAS.

**Optimization and improvements:**

-   Optimized environment components, and solved the problem of occasional failure to asynchronously load user namespaces.
-   The configuration management page now displays the namespace IDs, allowing you to quickly identify the namespaces.
-   The namespace management displays namespace ID column by default, allowing you to view the relationship between the namespaces and the IDs conveniently.
-   Solved the problem that the updated configuration overwrites the ownership application field.
-   Added sample code for Node.js and CPP.

## V4.0.0 {#section_ygg_qkp_42b .section}

**Released date:** 2017.10.10

**New features:**

-   Supports release, modification, deletion, and gray release of configurations.
-   Supports query of the configuration's historical versions.
-   Supports query of the configuration push track.
-   Supports query of the configuration listening.
-   Client supports Java Native SDK and Java Spring Cloud SDK.

