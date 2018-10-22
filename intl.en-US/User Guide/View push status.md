# View push status {#task_kcz_n4t_42b .task}

After changing a configuration, you might want to check whether the changed configuration has been pushed to the machine which is listening for this configuration. This query is only effective for clients that use the configuration listening API to listen for configurations.

1.  Log on to the , and select a region as needed in the upper-left corner. 
2.  In the left-side navigation pane of the console, select **Configurations**, enter the **DataID** or **Group** of the configuration to be queried, and then click **Search**. Matching results appear in the list.
3.  In the search results, select **More** \> **Configuration Listening Query** on the right side of the target configuration. 

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_historical_versions.png)

    The Listening Query page is displayed.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_listening_query.png)

4.  Enter the **Dimension**, **DataID**, or **Group** on the page, and then click **Search**. 

    **Note:** 

    -   If the dimension is set to **Configuration**: search the machines to which this configuration is pushed and the push status.
    -   If the dimension is set to **IP**: search the list of configurations that this machine listens for.

