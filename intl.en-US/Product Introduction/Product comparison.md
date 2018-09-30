# Product comparison {#concept_wbn_3sq_m2b .concept}

This document compares several mainstream application configuration management products.

## Similar Products {#section_yck_qsq_m2b .section}

-   ZooKeeper

    [ZooKeeper](https://zookeeper.apache.org/) is an open-source implementation of Google Chubby, which provides distributed application coordination service. ZooKeeper provides consistency management services for distributed applications, including configuration maintenance, domain name services, distributed synchronization, group services, and so on. In scenarios such as a Hadoop cluster, ZooKeeper also performs application configuration management.  However, as a CP \(Consistency, Partition Tolerance\) application, its availability and performance can be affected.

-   etcd

    Like ZooKeeper, [etcd](https://github.com/coreos/etcd) is a key value storage system with high availability designed for configuration sharing and service discovery. edcd is developed and maintained by CoreOS, inspired by ZooKeeper and Doozer.  It is written in Go and processes log replication using the Raft consistency algorithm to ensure strong consistency. Similar to ZooKeeper, etcd can also be used for application configuration management.  However, as a management application aimed to ensure strong consistency, its availability and performance can be compromised in some scenarios.

-   Spring Cloud Config Server

    Similar to ACM, [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/) provides external configuration support for both the server and the client in distributed systems. The config server provides a centralized configuration service for every application in any environment.  Yet unlike ACM, the Spring Cloud Config Server uses Git for configuration information storage by default, and its features like configuration storage, version management, and configuration publishing are all based on Git or other peripheral systems. In addition, ACM and Spring Cloud Config are quite different in terms of configuration management functionalities.


## Product comparison {#section_wks_htq_m2b .section}

The following table details the differences among ACM, etcd, ZooKeeper, and Spring Cloud Config in terms of application configuration management.

|Product|ACM|Spring Cloud Config Server|ZooKeeper|etcd|
|-------|---|--------------------------|---------|----|
|Configuration modification|Done on ACM console directly|Done on Git repository|Done by calling the ZK API|Done by calling the etcd API|
|Automatic configuration push|Modified configurations are automatically pushed to listening clients|The client can only load the configurations when it is started|Modified configurations are automatically pushed to the listening client|Modified configurations are automatically pushed to the listening client|
|API|Based on RESTful API, and supports Java Native API, Spring Cloud API, and API of other languages|Based on RESTful API and Spring Cloud specifications, and supports clients using other languages|Supports Java Native API|Based on the RESTful API|
|Version Management|Automatic version recording of each change|Indirect version management by Git|Without any version control|Without any version control|
|Configure push Tracing|Supports queries of configuration push status and tracks for all clients|Unable to query configuration push history|Unable to query configuration push history|Unable to query configuration push history|

**Note:** Unlike the CP applications such as etcd and ZK, ACM is not positioned for strict transactional configuration services such as distributed locks.

