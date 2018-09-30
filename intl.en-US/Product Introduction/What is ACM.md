# What is ACM {#concept_y4m_zgv_m2b .concept}

Application Configuration Management \(ACM\), formerly known as Taobao's internal configuration center Diamond, is an application configuration center that enables you to centralize the management of application configurations, and accomplish real-time configuration push in a distributed environment. With ACM, you can greatly reduce the workload of configuration management and enhance service capabilities in scenarios such as microservices, DevOps, and big data.

In application lifecycle management, developers usually extract some of the configuration items or metadata of the application from the code and manage them in a separate configuration file. These separately managed content is called application configuration, one of the common ways to manage application changes. After the application is published, the maintenance personnel or the end user can change the configuration to adjust application behaviors and adapt to environment changes.

ACM serves as the configuration center in a distributed system. It offers a series of functions such as configuration modification, configuration push, historical version management, gray release, and configuration modification audit. With these features, ACM helps you centralize the management of configurations in every application, reduce the cost of configuration management in distributed systems, and lower the risks of availability issues or even failures caused by incorrect configuration changes.

## Configuration management in traditional architecture {#section_y4k_bhv_m2b .section}

In the traditional architecture, for any configuration changes, you often need to log on the specific server and manually modify the configurations for them to take effect, as shown in the following figure.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_config_mgmt_traditional_en.png)

## Configuration management with ACM {#section_apk_bhv_m2b .section}

With ACM, you only need to change the configuration in ACM console and the configuration information is automatically pushed to each server and takes effect with a latency of mere seconds. ACM mainly consists of three components: the client, the server, and the console for configuration management.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_config_mgmt_acm_en.png)

## Why ACM {#section_cpk_bhv_m2b .section}

Managing configurations using ACM brings the following benefits to IT operations and maintenance:

-   Updated configurations are automatically delivered to each machine in seconds, which greatly reduces the workload of manual configuration distribution;
-   By using the ACM configuration listening API, configurations on each application can take effect immediately without restarting the application;
-   Information about configuration listening, changes, and versions is automatically recorded, enhancing the capabilities in terms of audit, version management, and diagnosis.

## Learning Path {#section_lpy_vwn_42b .section}

With the , you can quickly get to know how to use the basic configuration management functions of ACM, and one-click rollback, push tracks, namespaces, access control, and more advanced features. You can also understand how to meet your specific needs with a range of APIs and SDKs.

