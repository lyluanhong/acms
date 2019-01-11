# Query and rollback versions {#task_zdd_mmt_42b .task}

If you made a mistake when changing a configuration item, you need to roll back immediately. ACM supports querying configuration change history and rolling back configurations.

1.  Log on to the , and select a region as needed in the upper-left corner. 
2.  In the left-side navigation pane, select **Configurations** , enter the **Data ID** or **Group** of the configuration to be queried, and then click **Search**. Matching results appear in the list.
3.  In the search results, select **More** \> **Historical Versions** on the right side of the target configuration. 

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_historical_versions_en.png)

    The Historical Versions page is displayed.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_roll_back_en.png)

4.  Perform the following tasks as needed on the page. 

    -   To view the configuration content of a specific version, click **Details** in the **Actions** column.
    -   To roll back to a specific version, click **Roll Back** in the **Actions** column, and on the Configuration Rollback page, click **Roll Back**.
    **Note:** ACM currently saves change histories of up to 30 days.


