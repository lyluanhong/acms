# Access control {#concept_xgb_h25_42b .concept}

This topic explains how to use the access control functionality of ACM with an example of authorize a RAM user to use a namespace.

## Background information {#section_lr3_325_42b .section}

Previously, once granted the AliyunACMFullAccess authorization, a RAM user immediately has the full access to ACM, including the read and write access to all configurations and all namespaces. Given that the configurations of different RAM users are not isolated, misoperations or malicious operations can cause significant losses and severe consequences. More importantly, sensitive configurations such as database accounts and passwords are facing the risk of leakage due to their visibility to all authorized users.

Now, ACM provides access control of finer granularity, so that you can grant minimal access to users, and grant different users \(or roles\) different resource operation permission. Mirroring RAM’s authorization policy, access can be granted in terms of Action or Resource.

Action

-   Read: can read all configurations in the scope specified by Resource, and read the basic information of namespaces. The corresponding RAM authorization policy Action is `acms: R`.
-   Write: can add, delete, or modify all configurations in the scope specified by Resource, but cannot add, delete, or modify namespaces. The corresponding RAM authorization policy Action is `acms: W`.
-   Full access: can read and write all configurations in the scope specified by Resource, and read the basic information of namespaces. Also can add, delete, or modify namespaces if Resource is `*`. The corresponding RAM authorization policy Action is `acms:*`.

Resource

-   All resources: the corresponding Ram Authorization Policy Resource is `*`.
-   A single namespace: For example, if the namespace is `1ca01ca0-11b0-1e01-0df1-d1010101bc10,` then the RAM authorization policy Resource is `*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10`.
-   A group within a single namespace: For example, if the namespace is `1ca01ca0-11b0-1e01-0df1-d1010101bc10`, and the group is `DEFAULT_GROUP`, then the RAM authorization policy Resource is `*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10/DEFAULT_GROUP`.

## Step 1: Create a custom RAM authorization policy {#section_nzg_n25_42b .section}

1.  Log on to the [RAM console](https://ram.console.aliyun.com/).
2.  In the left-side navigation pane, click **Policies**.
3.  In the upper-right corner of the Policy Management page, click **Create Authorization Policy**.
4.  On the **Select an authorization policy** page of the Create Authorization Policy dialog box, click **Blank Template**.
5.  On the **Edit permissions and submit** page, enter your custom authorization policy name, description, and policy content, and then click **Create Authorization Policy**.

    For example, to configure the read and write access for namespace `1ca01ca0-11b0-1e01-0df1-d1010101bc10`, please enter the following content in the **Policy Content** textbox:

    ```
    {
    "Version": "1",
    "Statement": [
     {
       "Action": [
         "acms:*"
       ],
       "Resource": "*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10",
       "Effect": "Allow"
     }
    ]
    }
    ```

    **Note:** For instructions on how to create a custom RAM authorization policy, see [Create a custom policy \(optional\)](../../../../reseller.en-US/Quick Start/Create a custom policy (optional).md#).


## Step 2: Create a RAM user {#section_stc_mr5_42b .section}

1.  Return to the [RAM console](https://ram.console.aliyun.com/).
2.  In the left-side navigation pane, click **Users**.
3.  Click **Create User** in the upper-right corner of the User Management page, and enter the user name and other information in the Create User dialog box.

    **Note:** To generate an AccessKey, select **Automatically generate an Access key for this user**. Newly created users don’t have any permissions. You must authorize them first.

4.  In the table on the User Management page, click the name of the user created at the previous step.
5.  In the **Web Console Logon Management** area of the User Details page, click **Enable Console Logon** on the right side, and set the password for the user.
6.  In the left-side navigation pane, click **User Authorization Policies**, and click **Edit Authorization Policy** in the upper-right corner of the page.
7.  In the Edit User-Level Authorization dialog box, search for the authorization policy created at the previous step with keywords, click the **\>** button to move it to the **Selected Authorization Policy Name** list on the right, and then click **OK**.

**Note:** 

For instructions on how to create and authorize RAM users, see [Create a RAM user](../../../../reseller.en-US/Quick Start/Create a RAM user.md#) and [Attach policies to a RAM user](../../../../reseller.en-US/Quick Start/Attach policies to a RAM user.md#).

## Step 3: Log on with the RAM user and verify the access {#section_dy1_pr5_42b .section}

1.  Return to the [RAM console](https://ram.console.aliyun.com/).
2.  On the Dashboard page, click the RAM User Logon Link, and log on with your newly created user.
3.  Go to the ACM console, and verify if only the namespace specified in the authorization policy can be manipulated.

## More examples {#section_czx_qr5_42b .section}

1.  Grant the read-only access to a single namespace \(for example `1ca01ca0-11b0-1e01-0df1-d1010101bc10`\)

    ```
    {
    "Version": "1",
    "Statement": [
     {
       "Action": [
         "acms:R"
       ],
       "Resource": "*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10",
       "Effect": "Allow"
     }
    ]
    }
    ```

2.  Grant the read and write access to a single group \(for example `DEFAULT_GROUP`\) within a single namespace \(for example `1ca01ca0-11b0-1e01-0df1-d1010101bc10`\)

    ```
    {
    "Version": "1",
    "Statement": [
     {
       "Action": [
         "acms:*"
       ],
       "Resource": "*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10/DEFAULT_GROUP",
       "Effect": "Allow"
     }
    ]
    }
    ```

3.  Grant the read-only access to multiple groups \(for example `DEFAULT_GROUP` and `DEFAULT_GROUP_1`\) within a single namespace \(for example `1ca01ca0-11b0-1e01-0df1-d1010101bc10`\)

    ```
    {
    "Version": "1",
    "Statement": [
     {
       "Action": [
         "acms:R"
       ],
       "Resource": [
               "*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10/DEFAULT_GROUP",
               "*:*:*:*:cfg/1ca01ca0-11b0-1e01-0df1-d1010101bc10/DEFAULT_GROUP_1"
           ],
       "Effect": "Allow"
     }
    ]
    }
    ```

4.  Grant the read and write access to a single group \(for example DEFAULT\_GROUP\) within all namespaces

    ```
    {
    "Version": "1",
    "Statement": [
     {
       "Action": [
         "acms:*"
       ],
       "Resource": [
               "*:*:*:*:cfg/*/DEFAULT_GROUP"
           ],
       "Effect": "Allow"
     }
    ]
    }
    ```


## Note {#section_all_tr5_42b .section}

-   Only when the authorization policy Action is `acms:*`, and the Resource is `*`, users \(or roles\) granted this policy can add, delete, or modify namespaces.
-   Due to the cache system, it usually takes about 10 seconds for added and modified authorization policies to be effective.
-   [Access ACM with instance RAM role](reseller.en-US/User Guide/Access ACM with instance RAM role.md#) : you can also achieve access control of finer granularity by granting the aforementioned authorization policy.

## Related documents {#section_ugy_5r5_42b .section}

-   [What is RAM](../../../../reseller.en-US/Product Introduction/What is RAM.md#)
-   [Create a custom policy \(optional\)](../../../../reseller.en-US/Quick Start/Create a custom policy (optional).md#)
-   [Create a RAM user](../../../../reseller.en-US/Quick Start/Create a RAM user.md#)
-   [Attach policies to a RAM user](../../../../reseller.en-US/Quick Start/Attach policies to a RAM user.md#)
-   [Policy syntax structure](../../../../reseller.en-US/User Guide/Policy Language/Policy syntax structure.md#)
-   [Access ACM with instance RAM role](reseller.en-US/User Guide/Access ACM with instance RAM role.md#)

