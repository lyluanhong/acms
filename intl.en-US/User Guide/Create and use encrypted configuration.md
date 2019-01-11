# Create and use encrypted configuration {#concept_dvk_15j_n2b .concept}

ACM provides encrypted configuration to meet the requirement for sensitive configurations \(data sources, tokens, usernames, passwords, and so on\), and to lower the risk of leaking user configurations. An encrypted configuration is a configuration stored in an encrypted way. This topic explains how to create and use an encrypted configuration.

## Create encrypted configuration {#section_a4y_b5j_n2b .section}

1.  Log on to the .
2.  In the left-side navigation pane of the console, click**Configurations**, and on the right side of the page, click the **+** icon.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_create_configuration_en.png)

3.  On the Create Configuration page, switch on **Data Encryption**.

    **Note:** When you use the data encryption function for the first time, the Activate Data Encryption Services dialog box appears. You must activate the key management service and authorize ACM to encrypt and decrypt with your key management service before you can use this function, because ACM data encryption function relies on key management service to encrypt configurations.

4.  In the Activate Data Encryption Services dialog box, perform the following steps.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_data_encryption_en.png)

    1.  Click**Activate Now**. On the Enable Service page, select **I agree with Key Management Service Agreement of Service**, and then click **Enable Now**.

        ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_enable_kms_en.png)

    2.  Click **Authorize Now**. On the Cloud Resource Access Authorization page, select the target permission, and click **Confirm Authorization Policy**.

        ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_authorize_kms_en.png)

5.  On the Create Configuration page, enter the configuration content, and then click **Publish**.

    **Note:** To make it easier for you to manage the configurations, everything is displayed in plain text in the console, although the configurations are actually encrypted.


## Use encrypted configuration {#section_wxd_c5j_n2b .section}

1.  Log on to the .
2.  In the **Actions** column on the right side of the table, click **Code Example** to get sample code.

    **Note:** Java SDK and Python SDK has incorporated KMS-SDK, so you can add decryption filters for automatic decryption. For more information about decryption of other languages, see [Decrypt](https://www.alibabacloud.com/help/zh/doc-detail/28950.html).

3.  Click **Details** above the Search button, and click **Obtain** in the Namespace Details dialog box to obtain the initialization parameters.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/db_namespace_details_en.png)

    **Note:** Although you can directly obtain the configuration data with the AccessKey/SecretKey of the primary account, we strongly recommend that you use the AccessKey/SecretKey of sub-accounts for the sake of security. If you use the AccessKey/SecretKey of sub-accounts, then you must grant the sub-account the AliyunACMFullAccess and AliyunKMSCryptoAccess permission. For more information, see [Sub-Account Management](reseller.en-US/User Guide/Sub-Account Management.md#).


