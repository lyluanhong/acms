# Terms {#reference_ywp_1rv_m2b .reference}

## Configuration {#section_rdx_vnh_p2b .section}

During system development, developers usually extract some parameters or variables that need to be changed from the code and manage them in a separate configuration file.  This enables the static system artifacts or deliverables \(such as WAR and JAR packages\) to fit with the physical operating environment in a better way.  Configuration management is generally a part of the system deployment process, which is completed by the system administrator or maintenance personnel.  Configuration modification is one of the most effective methods to adjust the behavior of a running system.

## Dynamic configuration and static configuration {#section_sdx_vnh_p2b .section}

System configuration can be static or dynamic.  Static configurations means that configurations are defined when the system is built and deployed and cannot be changed unless there is a version upgrade of the system. Dynamic configurations, however, are those that can be changed from time to time while the system is running.  For example, configuration of build-version: 1.0.0 is bounded with the software version and is a static configuration; the configuration of the thread pool size can be changed multiple times when the system is running, and thus is a dynamic configuration.

## Configuration management {#section_tdx_vnh_p2b .section}

In the data center, all configuration-related activities such as editing, storage, distribution, change management, history version management, and change audit are collectively referred to as configuration management.

## Configuration push {#section_udx_vnh_p2b .section}

During configuration management, the configuration management system often needs to distribute the configuration changes to relevant systems, and the process from configuration distribion to coniguration validation is called configuration push.

## Push track {#section_vdx_vnh_p2b .section}

The entire track from configuration change, configuration push to configuration validation is called push track.  By looking at the push track of a configuration, you can see on which applications or machines, or at what time a configuration change is made and what impact it has.

## Configuration listening {#section_wdx_vnh_p2b .section}

Configuration listening means that the ACM allows the system to register the listener by SDK to listen for and consume the configuration changes.

## Configuration item {#section_xdx_vnh_p2b .section}

It is a specific configurable parameter with its value range, usually in the form of param-key=param-value. For example, the log output level \(logLevel=INFO|WARN|ERROR\) of a system is regarded as a configuration item.

## Configuration set {#section_ydx_vnh_p2b .section}

A collection of related or unrelated configuration items is called a configuration set.  Usually a configuration file in the system is a configuration set which contains configurations of all aspects of the system. For example, a configuration set may contain configuration items such as data sources, thread pools, and log levels.

## Data ID {#section_zdx_vnh_p2b .section}

The ID of a configuration set in ACM. It is one of the dimensions according to which configurations are organized. Data ID is generally used to organize the system configuration sets. A system or application can contain multiple configuration sets, each of which can be identified by a meaningful name.  The Data ID usually uses the naming rule similar to Java packages \(for example, com.taobao.tc.refund.log.level\) to ensure global uniqueness. This naming rule is not mandatory.

## Group {#section_a2x_vnh_p2b .section}

The group of configuration sets in ACM. It is one of the dimensions according to which configurations are organized.  The configuration sets are always grouped by a meaningful string such as Buy or Trade to differentiate the configuration sets with the same Data ID. When you create a configuration on ACM, the group name is replaced by DEFAULT\_GROUP by default if not specified.  A typical scenario of Group is when the same configuration type is used for different applications or components, such as database\_url configuration and MQ\_topic configuration.

## Namespace {#section_b2x_vnh_p2b .section}

Namespace in ACM is used for the isolation of configurations by tenants. Different namespaces may have configurations with the same Group or Data ID. One of the common scenarios of namespace is to differentiate and isolate configurations in different environments, such as development and test environment or production environment.

## Configuration snapshot {#section_c2x_vnh_p2b .section}

The ACM client SDK can generate snapshots of configurations on local machines.  Snapshots can be used to indicate the overall disaster recovery capabilities of the system when the client cannot connect to the ACM server.  Configuration snapshot is similar to local commit in Git, or cache, which is updated at the appropriate time, but does not have the notion of expiration as in cache.

