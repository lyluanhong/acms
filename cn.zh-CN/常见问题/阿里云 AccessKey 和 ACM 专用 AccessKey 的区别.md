# 阿里云 AccessKey 和 ACM 专用 AccessKey 的区别 {#concept_llh_k3c_p2b .concept}

目前，ACM 同时支持阿里云 AccessKey/SecretKey 和 ACM 专用 AccessKey/SecretKey。本文解释了为什么有两套身份标识，以及二者的区别。

## 为什么有两套身份标识系统 {#section_o2b_m3c_p2b .section}

-   阿里云最初不支持主子账号。阿里云主账号的权限特别大，泄漏风险高，因此不推荐使用主账号的 AccessKey/SecretKey 访问其他系统。
-   阿里云账号系统主要用于用户访问控制，能承受的 QPS 较小，不建议用于数据访问控制鉴权。

## 二者的区别 {#section_qrh_n3c_p2b .section}

|身份类型|阿里云 AccessKey/SecretKey|ACM 专用 AccessKey/SecretKey|
|----|-----------------------|--------------------------|
|权限|主账号具备所有权限。如果不对子账号授权，则子账号没有权限，授权后则具备所有权限（将来会实现子账号细粒度授权）|可以操作一个命名空间下的数据|
|使用场景|与其他云产品联动，例如整合 KMS 做数据加密|兼容老用户使用场景|

## 使用建议 {#section_emn_l3c_p2b .section}

由于阿里云目前支持子账号体系，并且接口性能大幅提升，使用 ACM 单独账号体系已无优势，因此建议您使用阿里云账号体系。

