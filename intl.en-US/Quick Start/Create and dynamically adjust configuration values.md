# Create and dynamically adjust configuration values {#concept_gjm_x2t_42b .concept}

If an application is deployed on multiple servers, once you need to change the configuration, you'll have to make the same changes on all servers, which apparently is inefficient. With ACM, you can create a configuration for your application, and use the native API of ACM to listen for changes to this configuration in the program. Once you change the configuration in the ACM console, every server to which this application is deployed receives the changed configurations, and the application status changes accordingly.

## Prerequisites {#section_ctk_qft_42b .section}

-   You finished the following task: [Activate ACM](reseller.en-US/Quick Start/Activate ACM.md#).
-   [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) has been installed on the server, and the environment variable JAVA\_HOME has been set.

## Context {#section_ds5_qft_42b .section}

The business application myapp.jar is deployed to two servers in the production environment. This application has a configuration file app.cfg, which contains two configuration items: threadPoolSize and logLevel. Now, you need to adjust the configuration of the application on these two servers simultaneously and refresh the status of the application dynamically. The scenario is shown in the following figure:

![](http://acm-public.oss-cn-hangzhou.aliyuncs.com/quick_start_pic1.png) 

The configuration body:

```
## app.cfg ##
threadPoolSize=5
logLevel=WARN
```

In this example, first we create a configuration for the application myapp on ACM, and then listens for changes to this configuration with the native API of ACM. Once we change this configuration in the ACM console, every server to which this application is deployed receives the changed configurations, and the application status changes accordingly.

## Step 1: Create the configuration in ACM {#section_ljb_bgt_42b .section}

1.  Log on to the .
2.  In the left-side navigation pane, select **Configurations**, and then click the **+** button in the upper-right corner.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_create_configuration.png) 

3.  Enter the following data on the Create Configuration page, and click **Publish**.

    -   DataID: com.acm.myapp.app.cfg
    -   Group: myapp
    -   Configuration body:

        ```
        threadPoolSize=5
        logLevel=WARN
        ```

    See the figure below:

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_pg_create_config_myapp.png)


## Step 2: Use the API to listen for configuration changes {#section_jt3_cgt_42b .section}

1.  Run the following command to create a Maven project, or download the sample project [myapp.zip](https://acm-public.oss-cn-hangzhou.aliyuncs.com/sample/myapp.zip).

    **Note:** For instructions on how to install and use Maven, see [Maven documentation](https://maven.apache.org/).

    ```
    mvn archetype:generate -DgroupId=com.acm.sample -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    The created project structure is as follows:

    ```
    myapp
    |---pom.xml
    `--  src
        |-- main
        |   `-- java
        |       `-- com
        |           `-- acm
        |               `-- sample
        |                   `-- App.java
        `-- test
            `-- java
                `-- com
                    `-- mycompany
                        `-- app
                            `-- AppTest.java
    ```

2.  Add ACM client native API dependencies in POM. xml.

    ```
    <dependencies>
         <dependency>
             <groupId>com.alibaba.edas.acm</groupId>
             <artifactId>acm-sdk</artifactId>
             <version>1.0.8</version>
         </dependency>
         <! -- Remove the following if logging implementation is available. -->
         <dependency>
             <groupId>ch.qos.logback</groupId>
             <artifactId>logback-classic</artifactId>
             <version>1.1.7</version>
         </dependency>
     </dependencies>
    ```

3.  Add the raven-assembly-plugin packaging plug-in pom.xml.

    ```
    <plugin>
       <artifactId>maven-assembly-plugin</artifactId>
       <version>2.4</version>
       <configuration>
          <finalName>myapp</finalName>
          <descriptorRefs>
             <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
          <appendAssemblyId>false</appendAssemblyId>
          <archive>
             <manifest>
                <mainClass>com.acm.sample.App</mainClass>
             </manifest>
          </archive>
       </configuration>
       <executions>
          <execution>
             <id>make-assembly</id>
             <phase>package</phase>
             <goals>
                <goal>single</goal>
             </goals>
          </execution>
       </executions>
    </plugin>
    ```

4.  Listen for configuration changes with API.

    **Note:** The user variables in the following code, such as $endpoint, $namespace, and $accesskey can be found on the Namespace page of the ACM console, as shown in the following figure.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_db_namespace_details.png)

    ```
    //-- App.java
    package com.acm.sample;
    
    import java.io.IOException;
    import java.io.StringReader;
    import java.util.Properties;
    import com.alibaba.edas.acm.listener.ConfigChangeListener;
    import com.alibaba.edas.acm.ConfigService;
    import com.alibaba.edas.acm.exception.ConfigException;
    
    public class App {
    
         private static Properties appCfg = new Properties();
    
         public static void initAndWatchConfig() {
             final String dataId = "com.acm.myapp.app.cfg";
             final String group = "myapp";
             final long timeoutInMills = 3000;
    
             // Copy the corresponding values from the namespace page of the console.
             Properties properties = new Properties();
             properties.put("endpoint", "$endpoint");
             properties.put("namespace", "$namespace");
             properties.put("accessKey", "$accessKey");
             properties.put("secretKey", "$secretKey");
    
             // If it is an encrypted configuration, then add the following two lines for automatic decryption.
             // properties.put("openKMSFilter", true);
             // properties.put("regionId", "$regionId");
    
             ConfigService.init(properties);
    
             // Get configuration body directly.
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
                         // process exception
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


## Step 3: Deploy and launch the application {#section_x55_qft_42b .section}

1.  Package your application into a JAR file and copy it to both servers. Execute the following packaging command under the root directory of the project:

    ```
    mvn clean package
    ```

2.  Deploy and start the application in Shell.

    ```
    ${JAVA_HOME}/java -cp myapp.jar com.acm.sample.App
    ```


**Note:** To run Java programs, you must install [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)on the server and set environment variable JAVA\_HOME.

## Step 4: Search for and change the configuration in the ACM console {#section_z2v_3gt_42b .section}

1.  On the Configurations page of the ACM console, search for the configuration created in [Step 1: Create the configuration in ACM](#section_ljb_bgt_42b).
2.  In the **Actions** column, click **Edit**.
3.  On the Edit Configuration page, change the configuration body as follows and click **Publish**.

    ```
    threadPoolSize=15
    logLevel=DEBUG
    ```

4.  In the Content Comparison dialog box, verify that the configuration changes are correct, and click**Publish**.

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/db_content_comparison.png)


## Verify the result {#section_fv5_qft_42b .section}

After the configuration is published, we can see that the configuration changes are received simultaneously on both servers on which the application is deployed, and the following information is printed.

```screen
current thread pool size: 15
current log level: DEBUG
```

