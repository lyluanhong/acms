# Differences between Alibaba Cloud AccessKey and ACM-specific AccessKey {#concept_llh_k3c_p2b .concept}

﻿Currently, ACM supports both Alibaba Cloud AccessKey/SecretKey and ACM-specific AccessKey/SecretKey. This topic explains why two sets of identification are in place, and how they are different.

## Why two sets of identification are in place {#section_o2b_m3c_p2b .section}

-   Alibaba Cloud doesn’t support primary account at the first place. Alibaba Cloud primary accounts have a lot of permissions, and pose high risks once leaked. Therefore, you’re not encouraged to access other systems with the AccessKey/SecretKey of primary accounts.
-   Alibaba Cloud account system is mainly used for user access control with a limited QPS tolerance. Therefore, you’re not encourage to use it for authentication of data access control.

## Their differences {#section_qrh_n3c_p2b .section}

|Identification type|Alibaba Cloud AccessKey/SecretKey|ACM-specific AccessKey/SecretKey|
|-------------------|---------------------------------|--------------------------------|
|Permission|A primary account has all permissions. An unauthorized sub-account doesn’t have any permissions. An authorized sub-account has all permissions. \(Authorization of finer granularity for sub-accounts to be implemented\)|Can operate on any data in a namespace|
|Usage|Used in combination with other cloud products, such as implementing data encryption by integrating with KMS|Compatible with old usage|

## Suggestions on usage {#section_emn_l3c_p2b .section}

Alibaba Cloud now supports sub-account system, and provides interfaces with considerably higher performance, so ACM-specific account system no longer gives you an edge. Instead, we recommend that you use Alibaba Cloud account system.

