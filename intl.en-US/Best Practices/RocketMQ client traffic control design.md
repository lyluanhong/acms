# RocketMQ client traffic control design {#concept_zns_mjc_p2b .concept}

RocketMQ is a commonly-used asynchronous RPC technology. This topic takes RocketMQ as an example to explain how to use ACM to implement traffic control over RocketMQ.

## Brief introduction to RocketMQ {#section_fbm_qjc_p2b .section}

For RocketMQ calling, a typical traffic control method is to control traffic at the subscription end. Two traffic control methods are supported:

-   Concurrent traffic control over message subscribers
-   Consumption delay traffic control over message subscribers

The basic principle for consumption delay traffic control over message subscribers is to control the consumption speed by adding a delay upon each consumption at the client end. Under this circumstance, the fastest theoretical concurrent consumption speed is:

```
MaxRate = 1 / ConsumInterval * ConcurrentThreadNumber
```

For example, if the concurrent thread number \(ConcurrentThreadNumber\) is 20, and the consumption delay \(ConsumInterval\) is 100 ms, then according to the preceding formula:

```
200 = 1 / 0.1 * 20
```

Theoretically, we can limit concurrent consumption traffic within 200.

In comparison with the concurrent thread number traffic control, consumption delay traffic control has some advantages, such as that it’s easier to implement, it’s less dependent on RocketMQ client packages, and it doesn’t need the client to provide the dynamic adjustment API that controls the concurrent thread number.

When using the above traffic control methods, if you want to implement dynamic global control under a distributed architecture, you can simply distribute traffic control parameters from the configuration center.

The following part elaborates how to implement dynamic global traffic control for asynchronous message consumption from the configuration center. configuration center. The example involves Alibaba Cloud’s RocketMQ and ACM \(Application Configuration Management\), and is based on Java.

**Note:** The reason why we take RocketMQ as an example is that RocketMQ Consumer Client SDK currently doesn’t support dynamic adjustment of the existing concurrent thread number, and we can solve the problem of dynamic RocketMQ consumption traffic control by dynamically adjusting consumption delay with ACM.

## Basic principles of consumption-delay-based traffic control {#section_jbm_qjc_p2b .section}

As shown in the following diagram, the administrator or application publishes the consumption interval configuration \(RCV\_INTERVAL\_TIME\) through the ACM console, which is subscribed to by all RocketMQ consumption applications. Theoretically, it takes no more than 1 second for this configuration to be published and distributed to all clients \(depends on network latency\).

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_rcv_interval_en.png)

## Sample code {#section_lbm_qjc_p2b .section}

This section provides the example code of dynamic global traffic control for asynchronous message consumption from the configuration center. For more information about SDK, see official documentation of and [ACM](../../../../../reseller.en-US/SDK Reference/SDK introduction.md#).

## Create ACM configuration {#section_mbm_qjc_p2b .section}

Create consumption delay parameters on ACM.

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_configuration_rcv_interval.png) 

## Set global consumption delay variable {#section_hdr_2kc_p2b .section}

1.  Set global variable for consumption receipt delay.

    ```
    // Initialize delay parameter for message receipt in milliseconds
     static int RCV_INTERVAL_TIME = 10000;
     // Initialize configuration service, and then the console will automatically retrieve the following parameters with the example code
     ConfigService.init("acm.aliyun.com", /*Tenant ID*/"xxx", /*AK*/"xxx", /*SK*/"yyy");    
     // Actively retrieve configuration
     String content = ConfigService.getConfig("app.mq.qos", "DEFAULT_GROUP", 6000);
     Properties p = new Properties();
     try {
         p.load(new StringReader(content));
         RCV_INTERVAL_TIME = Integer.valueOf(p.getProperty("RCV_INTERVAL_TIME"));
     } catch (IOException e) {
         e.printStackTrace();
     }
    ```

2.  Set ACM listener, and ensure the RCV\_INTERVAL\_TIME parameter is promptly updated upon modification of the configuration.

    ```
    // Add a listener to the configuration during initialization, which will send a callback notice upon configuration change.
     ConfigService.addListener("app.mq.qos", "DEFAULT_GROUP", new ConfigChangeListener() {
         public void receiveConfigInfo(String configInfo) {
             Properties p = new Properties();
             try {
                 p.load(new StringReader(configInfo));
                 RCV_INTERVAL_TIME = Integer.valueOf(p.getProperty("RCV_INTERVAL_TIME"));
             } catch (IOException e) {
                 e.printStackTrace();
             }
         }
     });
    ```


## Set RocketMQ consumption delay logic {#section_ybm_qjc_p2b .section}

The complete example is as follows:

**Note:** 

-   In this example, the access of the RCV\_INTERVAL\_TIME parameter is intentionally kept unlocked, and the reason will not be elaborated here.
-   Aliyun ONS Client doesn’t provide a dynamic concurrent thread number. The default concurrency is 20 by default. Therefore, in this example, we will use the consumption delay parameter to dynamically adjust QoS.

```
//The following code can be directly pasted into the Main() parameter
Properties properties = new Properties();
properties.put(PropertyKeyConst.ConsumerId, "CID_consumer_group");
properties.put(PropertyKeyConst.AccessKey,"xxx");
properties.put(PropertyKeyConst.SecretKey, "yyy");
properties.setProperty(PropertyKeyConst.SendMsgTimeoutMillis, "3000");
// Set TCP access domain name (in this example, we use a public cloud production environment).
properties.put(PropertyKeyConst.ONSAddr,
  "http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet");
Consumer consumer = ONSFactory.createConsumer(properties);
consumer.subscribe(/*Topic*/"topic-name", /*Tag*/null, new MessageListener() 
{
    public Action consume(Message message, ConsumeContext context) {
        // RocketMQ Subscribe QoS logical start, 
        // Each consuming process will sleep for RCV_INTERVAL_TIME seconds with 100 ms sleeping cycle.
        // Within each cycle, the thread will check RCV_INTERVAL_TIME in case it's set to a smaller value. 
        // RCV_INTERVAL_TIME &lt;= 0 means no sleeping.
        int rcvIntervalTimeLeft = RCV_INTERVAL_TIME;
        While (rcvintervaltimeleft> 0 ){
            if (rcvIntervalTimeLeft > RCV_INTERVAL_TIME) {
                rcvIntervalTimeLeft = RCV_INTERVAL_TIME;
            }
            try {
                if (rcvIntervalTimeLeft >= 100) {
                    rcvIntervalTimeLeft -= 100;
                    Thread.sleep(100);
                } else {
                    Thread.sleep(rcvIntervalTimeLeft);
                    rcvIntervalTimeLeft = 0;
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        // RocketMQ  subscribe interval logical ends
        System.out.println("Receive: " + message);
        /*
         * Put your business logic here.
         */
        doSomething();
        return Action.CommitMessage;
    }
});
consumer.start();
```

## Running result {#section_tcm_qjc_p2b .section}

Run the standalone Consumer for consumption. Assuming the message queue always has more messages than you can consume, the testing will be conducted in three stages, each lasting for five minutes. We can achieve the following results by using ACM configuration push service.

-   RCV\_INTERVAL\_TIME = 100 ms
-   RCV\_INTERVAL\_TIME = 5000 ms
-   RCV\_INTERVAL\_TIME = 1000 ms

For a testing case with a standalone RocketMQ consumption business processing time of approximately 100 ms and a standalone concurrent thread number of 20, the testing results are as follows:

-   RCV\_INTERVAL\_TIME = 100 ms: The average consumption performance is approximately 9000 tpm
-   RCV\_INTERVAL\_TIME = 5000 ms: The average consumption performance is restricted to approximately 200 tpm
-   RCV\_INTERVAL\_TIME = 1000 ms: The average consumption performance rises to approximately 1100 tpm

As expected, these results indicate that the consumption is inversely proportional to tpm. Most importantly, the application is not interrupted during the whole process. The traffic control push takes effect on distributed clusters in less than one second. The standalone performance result is as follows:

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_rcv_qos.png) 

