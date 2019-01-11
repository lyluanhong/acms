# Sub-Account Management {#concept_gyw_dwt_42b .concept}

The ACM system supports the Resource Access Management \(RAM\) account system of Alibaba Cloud. A primary account can create RAM sub-accounts, so that the account key is not shared with other users and only minimum permissions are assigned to these sub-accounts as necessary, thus enabling the enterprise to function efficiently.

## About RAM sub-accounts {#section_drx_fwt_42b .section}

When using ACM, a primary account can enable clearly defined roles and responsibilities by assigning different roles and resources to its sub-accounts. This primary and sub-account permission model works in a similar way to the system and normal user model in the Linux system, where system users can grant or revoke permissions from normal users.

Description of RAM sub-accounts:

-   RAM sub-accounts are created by a primary account in the RAM system. No legality verification is required provided that each sub-account under the same primary account has a unique name.
-   Unlike logons with an Alibaba Cloud account, RAM sub-accounts log on through a unique logon entrance, which can be found in the RAM console.

## Create a RAM sub-account {#section_frx_fwt_42b .section}

1.  Log on to the [RAM console](https://ram.console.aliyun.com/), and click **Users** in the left-side navigation pane.
2.  In the upper-right corner of the page, click **Create User**, and in the Create User dialog box, enter the login name and other information, and then click **OK**. The newly created user is displayed on the Users Management page.

    **Note:** The login name must be unique within the primary account.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_db_create_user_en.png)

3.  Click the user's **User Name/Display Name**. The User Details page is displayed.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_bt_enable_console_logon_en.png)

4.  In the **Web Console Logon Management** section, click **Enable Console Logon**.
5.  In the password setting dialog box, enter a **New Password** and **Confirm Password**, select the check box **"On your next logon you must reset the password."** as needed, and then click **OK**.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_db_enable_console_logon_en.png)


So far, a RAM user that can log on to the console is created.

## Log on to the ACM console with RAM sub-account {#section_mrx_fwt_42b .section}

1.  Log on to the [RAM console](https://ram.console.aliyun.com/), and in the left-side navigation pane, click **Dashboard**.
2.  Click the **RAM User Logon Link**. The Sub-account Logon page is displayed.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_sc_ram_user_login_en.png)

    **Note:** The RAM user's logon link varies with the primary account.

3.  Enter information as prompted on the page, and enter the RAM console of the RAM sub-account.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_pg_ram_user_login_en.png)

4.  In the RAM console, navigate to the **Products & Services** section, and in the Middleware area, click **Application Configuration Management** to enter the ACM console.

    ![](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_ram_console_product_acm_en.png) 


## Authorize a RAM sub-account {#section_srx_fwt_42b .section}

The authorization of RAM is done on the level of ACM service, which means a user with RAM authorization has the full access to ACM. You can only grant or revoke the RAM authorization in the RAM console.

Here are the steps to authorize a RAM sub-account:

1.  Log on to the [RAM console](https://ram.console.aliyun.com/), and click **Users** in the left-side navigation pane.
2.  On the Users Management page, select a user to be authorized, and in the **Actions** column on the right side of the user, click **Authorize**.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_pg_users_en.png)

3.  In the search box of the Edit User-Level Authorization dialog box, enter ACM, select **AliyunACMFullAccess** to add it to the **Selected Authorization Policy Name** on the right, and then click **OK** to grant this sub-account all access to ACM. In addition, to use the [encryption and decryption](reseller.en-US/User Guide/Create and use encrypted configuration.md#) functions of ACM, also add the **AliyunKMSCryptoAccess** authorization policy.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_edit_role_authorization_policy_en.png)

    After the authorization is complete, the sub-account can log on to the ACM console.


## Deauthorize a RAM sub-account {#section_x55_vv2_z2b .section}

Here are the steps to deauthorize a RAM sub-account:

1.  Log on to the [RAM console](https://ram.console.aliyun.com/), and in the left-side navigation pane, click **Users**.
2.  On the Users Management page, select a user to be deauthorized, and in the **Actions** column on the right side of the user, click **Authorize**.
3.  Move the **AliyunACMFullAccess** policy from the the right-side area to the left-side area, and click **OK**.

Once deauthorized, the RAM sub-account cannot log on to the ACM console.

## Unbind a RAM sub-account {#section_lsx_fwt_42b .section}

1.  Log on to the [RAM console](https://ram.console.aliyun.com/#/overview), and in the left-side navigation pane, click **Users**.
2.  On the Users Management page, select a user to be unbound, and in the **Actions** column on the right side of the user, click **Delete**.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/common/ram_pg_users_en.png)


