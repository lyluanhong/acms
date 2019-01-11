# Managing configurations {#concept_tct_kkt_42b .concept}

After you create a configuration item, you can use the configuration in the code.  You may need to change configurations, so the ACM console is frequently used. You can search for, view, edit, and delete configurations in the ACM console.

## Search for configurations {#section_sy4_lkt_42b .section}

The ACM console supports searching for a configuration using the Data ID, Group ID, or a combination of both.

To search for a configuration, follow these steps:

1.  Log on to the , and select a **Region** as needed in the upper-left corner.
2.  Select **Configurations** in the left-side navigation pane of the console. Enter the information you want to search for, and click the **Search** button.
3.  After the target configuration is found, you can view the details of the configuration, or change or delete the configuration, as shown in the following figure.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/sc_actions_en.png)


## View configuration details {#section_yy4_lkt_42b .section}

Click **Details** in the search results to view detailed information about a specific configuration.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_config_details_en.png)

## Edit configurations {#section_az4_lkt_42b .section}

In the search result, click **Edit** to go to the editing page.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_edit_config_en.png)

ACM offers the following advanced features on the configuration editing page.

|Feature|Description|Usage|
|-------|-----------|-----|
|Format validation|Provides syntax validation for JSON or XML format. Select a format in the Format field to perform syntax validation for that specific format, which helps reduce issues caused by syntax errors.|Select the format of the text in the **Format** field before editing the configuration content.|
|Comparison of changes|ACM supports comparing changes when a changed configuration is submitted for publishing. Check the changes prior to publishing to reduce misoperations.|After the configuration is changed, click **Publish** to open the content comparison dialog box.|
|**Beta Publish**|An error in key configuration changes may cause a major fault, so it is recommended to publish the changed configurations to a few machines for verification first. By only pushing to all machines after the changes are proven to be safe, you can minimize the impact of errors in changes.|Check the **Beta Publish** checkbox, and enter the IP address of the machines for beta publish \(enter the public IP for a local test\).|

## Delete configurations {#section_hz4_lkt_42b .section}

Click **Delete** to delete a configuration.

