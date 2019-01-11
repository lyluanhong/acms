# Build standard PaaS service configuration from the configuration center {#concept_irk_xkc_p2b .concept}

MQ \(Message Queue\) is a common asynchronous RPC technology. This article explains how to conduct traffic control settings with standard configuration naming format, by taking traffic control over MQ as an example.

## How configuration convention issues arise {#section_y31_zkc_p2b .section}

For single attribute configuration of a single application, you can directly use the following configuration file, without any configuration convention issues.

```
//Configuration directory structure
--app
   |--src
      |--config
         |--application.properties
// Configuration content
RCV_INTERVAL_TIME=20
```

However, when writing distributed rules for distributed PaaS services, the PaaS service provider \(rather than the application party\) often encounters many problems when designing the configuration. Some of the issues in the MQ traffic control scenario are:

-   How to distinguish global configuration from local application configuration: For example, how can a PaaS service provider conduct global rule configuration and application-specific configuration when managing services provided by the platform.
-   How to distinguish MQ services of different clusters: For example, how to distinguish configuration of MQ1 Cluster from MQ2 Cluster when the configuration naming is consistent?
-   How to use one configuration center to isolate different environments such as Dev, Test, Staging, and Prod?

The preceding MQ traffic control scenario is illustrated as follows:

![](https://img.alicdn.com/tfs/TB1HeM3ox6I8KJjy0FgXXXXzVXa-1135-500.png)

Obviously, improper configuration naming rules will affect the above configuration's ease of use.

The following is a detailed description of how to set the flow limit in a standardized configuration naming format. Before talking about the configuration naming conventions, it’s necessary to understand the configuration structure organizing capability of the configuration center.

## Configuration structure functions of the configuration center {#section_ej1_zkc_p2b .section}

In addition to centralized configuration management and subscription push, the configuration center also has the configuration structuring capability to help the administrator greatly simplify configuration management in various complex application scenarios.

1. Description of the configuration center's configuration structuring capability

-   Tenant isolation: The configuration center has the capability to isolate configuration based on users and scenarios. With tenant isolation, different configurations in different tenants can have the same name but different authentication mechanisms.
-   Minimum configuration set: Several configurations combine a configuration collection by configuring a central group. Use the minimum configuration collection to change and publish different configurations for unified publishing and processing. Configuration path, similar to file path or network domain name, decides the hierarchical relationship between different configuration sets.
-   Key-Value form of a specific configuration: Users can set specific configuration content in the configuration center.

2. Comparison among different configuration centers with configuration structuring capabilities

To give you a more straightforward impression, we compared several configuration center products:

-   Alibaba Cloud ACM: Alibaba Cloud Application Configuration Management, formerly known as Diamond, is the earliest configuration center product in China. Alibaba Cloud ACM currently has different open-source versions on Git. It also has an enterprise version available from Alibaba Cloud.
-   Spring Cloud Config: Spring Cloud official configuration center tool, mainly used in the Java Spring industry.
-   ZooKeeper: With partial configuration center capabilities, ZooKeeper is positioned in distributed coordination information management, and can only be used as a configuration center for a moderate application scale. Considering its wide application scope, we included it in the comparison.

Comparison details are as follows:

|Function|ACM|Spring Cloud Config|ZooKeeper|
|--------|---|-------------------|---------|
|Tenant-based isolation|Namespace and Group isolation. Different Namespaces require different AK/SK authentications, which are not required for Group.|A Git project is a tenant.|No readily available tenant isolation technology.|
|Minimal configuration set|The configuration set marked with DataID is the minimal configuration set. The configuration set doesn’t have the concept of path, but can be queried by using wildcard characters if the configuration set is properly designed. In this way, the similar effect of a configuration path can be achieved.|A configuration file in a Git project is the minimal configuration set, and doesn’t have the concept of configuration path.|Znode is the minimal configuration set, and has the concept of configuration path.|
|Key-Value configuration|KV content is assembled by the user at will under DataID in any formats, such as properties, Json, XML, and so on. Validation function is provided on the management page.|Set KV by using the properties configuration file.|Set KV by setting the Value of Znode, and the content format is not restricted.|

To sum up, ACM has a relatively higher degree of flexibility in both tenant isolation and minimal configuration set. The next part covers how to use ACM's Namespace, Group, DataID and other configuration functions to design a suitable configuration structure to implement QoS traffic control policies.

## Best practices for distributed service configuration design based on the configuration center {#section_gj1_zkc_p2b .section}

1. Configuration structure

To meet functional needs of MQ configuration, we can use the following configuration methods in combination with the ACM features.

-   Isolate MQ configurations for different environments with different namespaces. For example:

    -   Use ProdEnv namespace for the production environment, TestEnv for the test environment, and DevENV for the development environment.
    -   Different environments are automatically isolated with AK/SK, which further enhances the security.
-   MQ services provided by different clusters are distinguished with Group to isolate configurations and simplify the access methods.

    -   For example, for MQ clusters that specifically serve the subordinate departments and core transaction departments, as well as MQ clusters that specifically serve the subordinate departments and transaction departments, we can use Group to distinguish different global configurations. By doing this, all applications use the same company's/subsidiary's AK/SK \(or similar authentication system key\) in the production system, which simplifies the deployment, effectively isolates the configurations of different clusters, and reduces the configuration complexity.

        ```
        DataID: mq.global.qos
        Group: Trading
        
        DataID: mq.global.qos
        Group: ProductCategory
        ```

-   Global configurations are stored in the form of configuration items with unified DataIDs.

    -   Wherein, the configuration ID begins with `mq`. `global` indicates global configuration, `qos` indicates qos configuration, and default value can be used for Group.

        ```
        DataID: mq.global.qos
        Group: Default_Group
        ```

-   Application local configuration IDs are named with the same prefix qos.\*

    -   Configuration Ids begin with `mq`. `app.[appname]` stands for the app configuration item to be reloaded, and default value can be used for Group.

        ```
        DataID: mq.app.app1.qos
        Group: Default_Group
        DataID: mq.app.app2.qos
        Group: Default_Group
        ```


3. Settings of KVs for specific configurations

For many configuration center products, such as Apollo, ACM System Manager Parameter Store, a specific configuration is the configuration center's smallest management unit. You need to set KVs of configurations one by one at a certain level. Two common practices are:

-   Similar to the preceding configuration center, save each key to a unique DataID. For example:
    -   Set mq.global.qos.RCV\_INTERVAL\_TIME to 50
    -   Set mq.global.qos.MAX\_THREAD to 20
-   Combine common configurations into the same DataID, and save them in the same configuration file \(supported formats are Properties, JSON, XML, and so on\)

    -   For example, setting of mq.global.qos is as follows:

        ```
        //MQ traffic control QoS settings
        RCV_INTERVAL_TIME = 50
        MAX_THREAD = 20
        ```


In practice, the second method is found not only to have greater flexibility, multiple configurations are also supported for simultaneous release in one change, which reduces performance overhead, in theory, it has also achieved the atomic action effect of changing mass change.

4. Configuration structure diagram

Configuration structure diagram of the preceding design is shown as follows:

![Diagram - ACM-MQ Scheme](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_acm_mq_scheme.png)

5. Advantages of the scheme

-   Isolating configurations for different environments with Namespaces allows MQ configuration items to use the same names in different Namespaces. Different namespaces are isolated by the administrator, program AK/SK, and permission settings, allowing you to centrally manage configuration items without affecting other environments.

-   Isolating different clusters in the same environment by using Groups ensures the configuration consistency of different clusters \(for example the configuration name doesn’t change\), simplifies code, and logically isolates different cluster configurations.

-   Standard naming settings of the minimal configuration set DataIDs allow MQ clients to conveniently find both the MQ default global configurations, and their own application-specific configurations. In addition, on the Configurations page, administrators can add an asterisk \(\*\) to each end of the keyword to easily filter out all MQ rules. For example:

    ![Example - Configuration Page - Fuzzy Search](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_pg_config_list_fuzzy_search.png)


## References {#section_dk1_zkc_p2b .section}

-   [Configuration change risk management](reseller.en-US/Best Practices/Configuration change risk management.md#)

