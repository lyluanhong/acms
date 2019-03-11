# 通过 ACM 动态推送配置 {#concept_gjm_x2t_42b .concept}

如需为部署在多台服务器的应用更改配置，在各台服务器上逐一更改显然效率太低，ACM 可以帮助您集中管理配置并将更新后的配置动态推送到所有服务器。本文以示例场景为例介绍实现方法。

## 前提条件 {#section_ctk_qft_42b .section}

-   已[开通 ACM 服务](intl.zh-CN/快速入门/开通 ACM 服务.md#)。
-   服务器上已安装 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 并设置环境变量 JAVA\_HOME。
-   服务器上已安装 Maven。关于如何安装和使用 Maven，请参考 [Maven 官方文档](https://maven.apache.org/)。

## 背景信息 {#section_ds5_qft_42b .section}

如果一个应用部署在多台服务器上，一旦需要更改配置，就不得不在所有服务器上逐一更改，显然效率很低。借助 ACM，您可以在 ACM 中为应用创建一个配置，并在应用中使用 ACM 的原生 API 监听此配置的变更。当您在 ACM 控制台更改此配置后，所有部署了该应用的服务器都会收到变更后的配置内容，应用状态也会随之刷新。

为说明以上流程，本文假设有以下示例场景：应用 myapp.jar 部署在生产环境的两台服务器上。该应用包含一个配置文件 app.cfg，该配置文件包含线程池大小（threadPoolSize）和日志级别（logLevel）这两个配置项。现在需要同时调整该应用在上述两台服务器上的配置，并动态刷新应用的状态。

 ![ACM 示例场景：通过 ACM 动态推送配置](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_dynamic_config_update_with_acm.png "示例场景：通过 ACM 动态推送配置") 

接下来介绍如何借助 ACM 满足上述需求。

## 步骤一：在 ACM 中创建配置 {#section_ljb_bgt_42b .section}

1.  登录 [ACM 控制台](https://acm.console.alibabacloud.com/)，并在页面左上角选择所需地域。
2.  在左侧导航栏中选择**配置列表**，然后在页面右上角单击 **+** 图标。

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/bt_create_configuration.png) 

3.  在新建配置页面上输入以下内容，并单击**发布**。

    -   Data ID：com.acm.myapp.app.cfg
    -   Group：myapp

        **说明：** 单击**显示高级选项**方可显示 **Group** 字段。

    -   配置内容：

        ```
        threadPoolSize=5
        logLevel=WARN
        ```

    如图所示：

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_pg_create_config_myapp.png)


## 步骤二：使用 ACM 原生 API 监听配置变更 {#section_jt3_cgt_42b .section}

1.  下载样例工程 [myapp.zip](https://acm-public.oss-cn-hangzhou.aliyuncs.com/sample/myapp.zip)，或者运行以下命令来创建 Maven 工程。

    ```
    mvn archetype:generate -DgroupId=com.acm.sample -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    创建的工程结构如下：

    ```
    myapp
    |-- pom.xml
    `-- src
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

2.  在 pom.xml 中添加对 ACM Client 原生 API 的依赖。

    ```
    <dependencies>
         <dependency>
             <groupId>com.alibaba.edas.acm</groupId>
             <artifactId>acm-sdk</artifactId>
             <version>1.0.8</version>
         </dependency>
         <!-- 如果已有日志实现，则可去除以下依赖 -->
         <dependency>
             <groupId>ch.qos.logback</groupId>
             <artifactId>logback-classic</artifactId>
             <version>1.1.7</version>
         </dependency>
     </dependencies>
    ```

3.  在 pom.xml 中添加 maven-assembly-plugin 打包插件。

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

4.  使用 ACM Client 原生 API 监听配置变更。

    **说明：** 以下代码中的 $endpoint、$namespace、$accesskey、$secretKey 等变量的值，可在 ACM 控制台的命名空间页面获取和查看，如下图所示。

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
    
             // 从命名空间详情对话框中拷贝各变量的值
             Properties properties = new Properties();
             properties.put("endpoint", "$endpoint");
             properties.put("namespace", "$namespace");
    
             // 如果通过AK/SK访问ACM
             properties.put("accessKey", "$accessKey");
             properties.put("secretKey", "$secretKey");
    
             // 如果通过ECS实例RAM角色访问ACM
             // properties.put("ramRoleName", "$ramRoleName");
    
             // 如果是加密配置，则添加以下两行进行自动解密
             // properties.put("openKMSFilter", true);
             // properties.put("regionId", "$regionId");
    
             ConfigService.init(properties);
    
             // 直接获取配置内容
             try {
                 String configInfo = ConfigService.getConfig(dataId, group, timeoutInMills);
                 appCfg.load(new StringReader(configInfo));
             } catch (ConfigException e1) {
                 e1.printStackTrace();
             } catch (IOException e) {
                 e.printStackTrace();
             }
    
             // 监听配置变化，获取最新推送值
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
    
             // 以下代码用于测试，作用是让主线程不退出。订阅配置是守护线程，如果主线程退出守护线程就会退出。
             while (true) {
                 try {
                     Thread.sleep(1000);
                 } catch (InterruptedException e) {
                 }
             }
         }
     }
    ```


## 步骤三：部署并启动应用 {#section_x55_qft_42b .section}

1.  在项目根目录执行以下打包命令，将应用打包成 JAR 文件，并拷贝到两台服务器上。

    ```
    mvn clean package
    ```

2.  在 Shell 中部署并启动应用。

    ```
    ${JAVA_HOME}/java -cp myapp.jar com.acm.sample.App
    ```


**说明：** 为了运行 Java 程序，服务器上必须安装 [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 并设置环境变量 JAVA\_HOME。

## 步骤四：在 ACM 控制台更改配置 {#section_z2v_3gt_42b .section}

1.  在 ACM 控制台的配置列表页面上，搜索[步骤一：在 ACM 中创建配置](#section_ljb_bgt_42b)中创建的配置，然后单击**操作**列中的**编辑**。
2.  在编辑配置页面上将**配置内容**更改为以下内容，并单击**发布**。

    ```
    threadPoolSize=15
    logLevel=DEBUG
    ```

3.  在内容比较对话框中，查看更改前后的对比，并单击**确认发布**。

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/db_content_comparison.png)


## 结果 {#section_fv5_qft_42b .section}

发布配置之后，部署了 myapp 应用的两台服务器均收到了更新后的配置，并在 Console 中打印了以下日志。

```screen
current thread pool size: 15
current log level: DEBUG
```

