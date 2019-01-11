# Configuration change risk management {#concept_tgf_t3c_p2b .concept}

This topic explains how to mitigate the underlying production risks of configuration modification with some best practices in configuration management.

## How to better manage the configurations {#section_fgm_w3c_p2b .section}

ACM provides different dimensions such as dataId, group, app, and namespace to help you manage all the configurations in your organization. By making proper use of these dimensions, you can reduce unintentional mistakes during the configuration management and ensure system stability.

-   Recommended configuration management methods
    -   dataId

        -   Indicates a set of configuration items in the key=value format.

        -   Name the dataId properly and give it a meaningful name, for example: com.company.trade.threadpool.params, trade.p1.props.

    -   group

        -   Generally uses the module name or resource name. If one application uses Nginx and SLB, the group name could be Nginx and SLB.
    -   app

        -   The application grouping, which is a simple business unit or microservice group. Generally, one application is developed and maintained by one small or medium team.
    -   namespace

        -   Isolates configurations of multiple applications, such as configurations in multiple environments.
-   Classification of the configuration influences

    Each configuration item may have different influences on the system. For example, an incorrect change of the log level can change the log volume in the system, and generally have no other negative effects. While changes to the connection pool, thread pool, threshold value, and host configuration often exert influences on a server level or an application service cluster level.

    The consequences of incorrect changes to global routing rules, load balancing policies, network configurations, and other key configurations of the distributed system can be significant. Therefore, prioritizing configurations to different impact levels \(such as P0 to P4\) by their influences is a good practice.

    Example:

    |Priority|Influence|Example|
    |--------|---------|-------|
    |P0|All|Global routing rules, network configurations, and load balancing configurations|
    |P1|Application clusters|Threshold values of the cluster and cluster service endpoints|
    |P2|Host-level and key configurations|Host resources configurations and thread pool size|
    |P3|Process-level and non-key configurations|Log level|
    |P4|Minor configurations|Version|

-   Minimize cumbersome configurations

    If you place all the configuration items in one configuration set, everyone can make changes on the same configuration set, This not only results in more frequent configuration modifications and configuration push, but also increases the risks of conflicts and misoperations, creating barriers to better configuration authorization and control of configuration priority change process.

-   Canary release is essential for key configurations

    When the configuration hierarchy is defined, it is important for you to mitigate the risks of modifications of key configurations \(such as P0 or P1 configurations\) with publishing policies like modification review and gray scale.


## How to ensure configuration security {#section_g41_jjc_p2b .section}

-   Do not store sensitive configuration information as plaintext in ACM

    ACM Server does not provide data encryption by default on the host and in the configuration storage. Sensitive configuration information, such as password, token, and AccessKey must be encrypted before being stored in ACM.

    ACM provides configuration encryption tools in APIs and in the console for you to encrypt the information before being stored in ACM.


