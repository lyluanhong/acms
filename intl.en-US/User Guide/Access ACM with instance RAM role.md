# Access ACM with instance RAM role {#concept_efq_g55_42b .concept}

This topic explains how to access ACM with the RAM role of ECS instances.

## Overview {#section_zsv_h55_42b .section}

In the past, for an application deployed in an ECS instance to access ACM, the Access Key ID and Access Key Secret \(“AK”\) must be stored in the ECS instance as a configuration file or in other forms. However, this increases the complexity of AK management and the risk of leaking sensitive data.

Now, with the [RAM role of an instance](../../../../../reseller.en-US/User Guide/Instances/Instance RAM roles/What is the RAM role of an instance.md#), you can associate a RAM role with an ECS instance, and then inform ACM SDK \(Version 1.0.8 and later\) of the name of this RAM role, so that you can access ACM without configuring AK later. In addition, with [RAM \(Resource Access Management\)](../../../../../reseller.en-US/Product Introduction/What is RAM.md#), you can also have multiple instances with different authorizations for ACM by tweaking their roles and authorization policies. For example, if assigned a role with a read-only authorization policy, an ECS instance can read ACM configurations but can’t add or modify one.

## Prerequisites {#section_btv_h55_42b .section}

You’re running a VPC network.

## Step 1: Create a RAM role and configure the authorization policy {#section_kf3_j55_42b .section}

1.  Log on to the [RAM console](https://ram.console.aliyun.com/). Click **Roles** in the left-side navigation pane.
2.  Click **New** in the upper right corner of the page.
3.  In the Create Role dialog box, complete the following steps.

    1.  On the **Select Role Type** page, click **Service Role**.
    2.  On the **Enter Type** page, select **ECS Elastic Compute Service**.
    3.  On the **Configure Basic Information** page, enter a custom **Role Name** and optionally a **description**, and click **Create**.
    **Note:** A newly created role doesn’t have any authorizations.

4.  InRole managementPage, click to the right of the role**Operation**Of a column**Authorization**.
5.  In the Edit Role Authorization Policy dialog box, search for the authorization policy `AliyunACMFullAccess`, and click the **&gt;** button to move it to the right-side **Selected Authorization Policy Name** list, and then click **OK**. To use the configuration encryption and decryption features, add the `AliyunKMSCryptoAccess` authorization policy.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_edit_role_authorization_policy_en.png) 

    Now this role is granted all authorizations for ACM.


## Step 2: Attach this RAM role to the ECS instance {#section_mql_l55_42b .section}

1.  Login[ECS Console](https://ecs.console.aliyun.com/#/home), Click on**Instance**.
2.  Click the target ECs instance in the list**Operation**Of a column**More**, And select**Grant/recover Ram role**To grant this instance the role that was new in the previous step.

## Step 3: Inform ACM SDK of the name of this RAM role and access configurations {#section_fgx_m55_42b .section}

You can inform ACM SDK \(Version 1.0.8 and later\) of the name of this RAM role in one the following ways:

-   By setting a JVM parameter: `-Dram.role.name=$ramRoleName` \(For example `-Dram.role.name=ECS-RAM`\)
-   By passing parameters with code

**Note:** JVM parameter setting takes precedence over passing parameters with code.

This is how to pass parameters with code:

```
import java.util.Properties;
import com.alibaba.edas.acm.ConfigService;
import com.alibaba.edas.acm.exception.ConfigException;
// Sample code, for sample test only.
public class ACMTest {
    public static void main(String[] args) {
        try {
            Properties properties = new Properties();
            // Obtain the endpoint from "Namespace details" or "Sample code" in the ACM console
            properties.put("endpoint", "$endpoint");
            // Obtain the namespace from "Namespace details" or "Sample code" in the ACM console
            properties.put("namespace", "$namespace");
            // The name of the newly created RAM role associated with an ECS instance, for example "ECS-RAM"
            properties.put("ramRoleName", "$ramRoleName");
            ConfigService.init(properties);
            // Actively get the configuration.
            String content = ConfigService.getConfig("${dataId}", "${group}", 6000);
            System.out.println(content);
        } catch (ConfigException e) {
            e.printStackTrace();
        }
    }
}
```

## Related documents {#section_f11_s55_42b .section}

-   [RAM \(Resource Access Control\)](../../../../../reseller.en-US/Product Introduction/What is RAM.md#)
-   [What is the RAM role of an instance](../../../../../reseller.en-US/User Guide/Instances/Instance RAM roles/What is the RAM role of an instance.md#)
-   [Access other Cloud Product APIs by the Instance RAM Role](../../../../../reseller.en-US/Best Practices/Access other Cloud Product APIs by the Instance RAM Role.md#)
-   [Prerequisites](../../../../../reseller.en-US/SDK Reference/ACM Java Native SDK/Prerequisites.md#)

