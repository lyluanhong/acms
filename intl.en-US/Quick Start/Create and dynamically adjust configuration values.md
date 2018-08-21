# Create and dynamically adjust configuration values {#concept_gjm_x2t_42b .concept}

This topic explains how to create and dynamically adjust configuration values in ACM with an example.

## Prerequisites {#section_ctk_qft_42b .section}

-   You have [Activate ACM](reseller.en-US/Quick Start/Activate ACM.md#).
-   [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) has been installed on the machine, and the environment variable JAVA\_HOME has been set.

## Background information {#section_ds5_qft_42b .section}

The business application “myapp.jar”  is deployed onto two production servers.  This application contains a configuration file  “app.cfg”, which consists of two configuration items: thread pool size and log level.  Now, you need to adjust the configuration of the application on these two servers simultaneously and refresh the state of the application dynamically. The scenario is shown in the following figure:

![](http://acm-public.oss-cn-hangzhou.aliyuncs.com/quick_start_pic1.png)

Configuration details are as follows:

```
## app.cfg ##
threadPoolSize=5
logLevel=WARN
```

## Step 1: Create configuration in ACM {#section_ljb_bgt_42b .section}

1.  Log on to the .
2.  In the left-side navigation pane, select **Configurations**, and then click the **+** button in the upper-right corner.   The Create Configuration page is displayed.![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_create_configuration_en.png)
3.  Enter the following on the page, and click **Publish**.

    -   Data ID: `com.acm.myapp.app.cfg`
    -   Group: `myapp`
    -   Content:

        ```
        threadPoolSize=5
        logLevel=WARN
        ```

     See the figure below:

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_pg_create_config_myapp_en.png)


## Step 2: Create a Maven project {#section_jt3_cgt_42b .section}

1.  Run the following command to create a Maven project, or download the sample project [myapp.tar](http://acm-public.oss-cn-hangzhou.aliyuncs.com/myapp.tar).

    **Note:** For instructions on how to install and use Maven, see [Maven documentation](https://maven.apache.org/).

    ```
    mvn archetype:generate -DgroupId=com.acm.sample -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    The created project structure is as follows:

    ```
    myapp
    |---pom.xml
     `--  src
        | -- main
        | '-- java
        | '-- com
        | '-- acm
        | '-- sample
        | '-- App. Java
        '-- test
            '-- java
                `-- com
                    `-- mycompany
                        `-- app
                            `-- AppTest.java
    ```

2.  Add the ACM Client Native API dependency to Pom.  

    ```
    <dependencies>
         <dependency>
             <groupId>com.alibaba.edas.acm	
    </groupId>
             <artifactId>acm-sdk	
    </artifactId>
             <version>1.0.6	
    </version>
         </dependency>
         <! -- Remove the following if logging implementation is available. -->
         <dependency>
             <groupId>ch.qos.logback	
    </groupId>
             <artifactId>logback-classic	
    </artifactId>
             <version>1.1.7	
    </version>
         </dependency>
     </dependencies>
    ```

3.  Listens for configuration changes with an API.

    ```
    // -- App. Java
         package com.acm.sample;
         import java.io.IOException;
         import java.io.StringReader;
         import java.util.Properties;
         import com.alibaba.edas.acm.listener.ConfigChangeListener;
         import com.alibaba.edas.acm.ConfigService;
         import com.alibaba.edas.acm.exception.ConfigException;
         public class App {
         Private Static Properties appcfg = new properties ();
         public static void initAndWatchConfig() {
             final String dataId = "com.acm.myapp.app.cfg";
             final String group = "myapp";
             final long timeoutInMills = 3000;
             // Copy the corresponding values from the "Namespace Management" in the console.
             Properties properties = new Properties();
             Properties. put ("endpoint", "$ endpoint ");
             Properties. put ("namespace", "$ namespace ");
             Properties. put ("accessKey", "$ accessKey ");
             Properties. put ("SecretsKey", "$ secretsKey ");
             // If it is an encrypted configuration, then add the following two lines for automatic decryption.
     //properties.put("openKMSFilter", true);
     //properties.put("regionId", "$regionId");
             ConfigService.init(properties);
             // Start a use-once scenario to get the configuration values directly.
             try {
                 String configInfo = ConfigService.getConfig(dataId, group, timeoutInMills);
                 appCfg.load(new StringReader(configInfo));
             } catch (ConfigException e1) {
                 e1.printStackTrace();
             } catch (IOException e) {
                 e.printStackTrace();
             }
             // Listen for configuration changes to get the latest values.
             ConfigService.addListener(dataId, group, new ConfigChangeListener() {
                 public void receiveConfigInfo(String configInfo) {
                     try {
                         appCfg.load(new StringReader(configInfo));
                     } catch (Exception e) {
                         // Process exception
                     }
                     refreshApp();
                 }
             });
         }
         public static void refreshApp() {
             System.out.println("current thread pool size: " + appCfg.getProperty("threadPoolSize"));
             System.out.println("current log level: " + appCfg.getProperty("logLevel"));
             System.out.println("");
         }
         public static void main(String[] args) {
             initAndWatchConfig();
             // Make sure the main thread does not exit.
             while (true) {
                 try {
                     Thread.sleep(1000);
                 } catch (InterruptedException e) {
                 }
             }
         }
     }
    ```


## Step 3: Deploy and start the application {#section_x55_qft_42b .section}

Copy the JAR package to one or two servers to deploy and then start the application.

The deployment can be performed by running the following command in shell:

```
${JAVA_HOME}/java -cp myapp.jar com.acm.sample.App
```

**Note:** To run Java programs, install  [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)on the server and set the JAVA\_HOME environment variable.

## Step 4: Search and change the configuration in the ACM console {#section_z2v_3gt_42b .section}

1.   Log on to the ACM console.
2.  On the Configurations page, search for the configuration created in [Step 1: Create configuration in ACM](#section_ljb_bgt_42b).
3.  Click **Details** in the **Actions** column. The Configuration Details page is displayed.
4.  On this page, change the configuration content to the following and click **Publish**.

    ```
    threadPoolSize=5
    logLevel=DEBUG
    ```

    See the figure below:

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_pg_edit_config_myap_en.png)


## Step 5: Verify results {#section_fv5_qft_42b .section}

After the configuration is released, we can see that the configuration changes are received simultaneously on both servers on which the application is deployed, and the following information is printed.

```screen
current thread pool size: 15
current log level: DEBUG
```

## Conclusion {#section_hv5_qft_42b .section}

In this example, a configuration is created for the application “myapp” on ACM and the Native API of ACM is used in the program to listen for changes to this configuration. When we changed the configurations in ACM, Every server to which this application is deployed receives the changed configurations, and the application status changes accordingly.

