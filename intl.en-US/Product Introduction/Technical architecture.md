# Technical architecture {#concept_r4j_sfd_n2b .concept}

This topic explains the technical architecture of ACM.

The architecture of ACM is as follows.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_acm_architecture_en-US.png "ACM technical architecture")

## ACM server {#section_xlw_tfd_n2b .section}

An ACM distributed service node contains the following:

-   Service protocol layer: performs protocol conversion and authentication.
-   Consistency management layer: performs configuration consistency management and configuration push.
-   Configuration cache layer: enhances configuration query and push efficiency with distributed cache.
    -   Storage layer: refers to backend distributed storage, which is used to store configurations and features high performance and high scalability.
    -   Console: refers to the ACM console for configuration management.

## ACM client {#section_amw_tfd_n2b .section}

Based on RESTful API, ACM client enables cross-language access.  ACM provides Java Native APIs and configuration read APIs based on Spring Cloud Config, so as to simplify your development process.

In some cases, ACM provides an agent for dynamic replacement of the configuration files on the host machine. In this case, you must manually specify the mapping relationship between ACM configuration items and configuration files.

