# 消息队列 RocketMQ 客户端流控设计 {#concept_zns_mjc_p2b .concept}

消息队列 RocketMQ 是一种常用的异步 RPC 技术。本文以阿里云消息队列 RocketMQ 为例，介绍了如何使用 ACM 对消息队列 RocketMQ 实现流量控制。

## 消息队列 RocketMQ 流量控制简述 {#section_fbm_qjc_p2b .section}

对于消息队列 RocketMQ 调用，常用的限流方式是在订阅端限流。限流方式有两种：

-   针对消息订阅者的并发流控
-   针对消息订阅者的消费延时流控

针对消息订阅者的消费延时流控的基本原理是，每次消费时在客户端增加一个延时来控制消费速度，此时理论上消费并发最快速度为：

```
MaxRate = 1 / ConsumInterval * ConcurrentThreadNumber
```

如果消息并发消费线程（ConcurrentThreadNumber）为 20，延时（ConsumInterval）为 100 ms，代入上述公式可得：

```
200 = 1 / 0.1 * 20
```

由上可知，理论上可以将并发消费控制在 200 以下。

与并发线程数流控相比，消费延时流控的优点在于其实现相对简单，对消息队列 RocketMQ 类客户端包依赖较少，而且不需要客户端提供控制并发线程数的动态调整接口。

若使用以上的流量控制方法在分布式架构下做到全局动态控制，可通过配置中心下发流控参数来实现。

下文详细介绍了如何基于配置中心来实现异步消息消费的全局动态流控。示例中使用了阿里云的消息队列 RocketMQ（消息队列）和 ACM（应用配置管理）产品，语言为 Java。

**说明：** 以消息队列 RocketMQ 为例是因为：截至目前，消息队列 RocketMQ Consumer Client SDK 暂不支持动态调整现成并发数，而通过 ACM 来动态调整消费延迟的方法可以解决消息队列 RocketMQ 消费流控动态的问题。

## 基于消费延时流控的基本原理 {#section_jbm_qjc_p2b .section}

如图所示，管理员或应用程序通过 ACM 控制台发布消费延时配置（RCV\_INTERVAL\_TIME），所有消息队列 RocketMQ 消费程序订阅该配置。理论上，该配置从发布到下发至所有客户端，可以在 1 秒内完成（取决于网络延时）。

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_rcv_interval_en.png)

## 代码示例 {#section_lbm_qjc_p2b .section}

以下章节给出了基于配置中心来实现异步消息消费的全局动态流控的代码示例。关于 SDK 的详细介绍，参见 [消息队列 RocketMQ](https://www.alibabacloud.com/help/doc-detail/29530.htm) 和[应用配置管理 ACM](../../../../../intl.zh-CN/SDK 参考/SDK 简介.md#) 产品官方文档。

## 创建 ACM 配置 {#section_mbm_qjc_p2b .section}

在 ACM 上创建消费延时的参数。

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_configuration_rcv_interval.png) 

## 设置全局消费延时变量 {#section_hdr_2kc_p2b .section}

1.  设置消费接收延时的全局变量。

    ```
    // 初始化消息接收延时参数，单位为millisecond
     static int RCV_INTERVAL_TIME = 10000;
     // 初始化配置服务，控制台通过示例代码自动获取下面参数
     ConfigService.init("acm.aliyun.com", /*租户ID*/"xxx", /*AK*/"xxx", /*SK*/"yyy");    
     // 主动获取配置
     String content = ConfigService.getConfig("app.mq.qos", "DEFAULT_GROUP", 6000);
     Properties p = new Properties();
     try {
         p.load(new StringReader(content));
         RCV_INTERVAL_TIME = Integer.valueOf(p.getProperty("RCV_INTERVAL_TIME"));
     } catch (IOException e) {
         e.printStackTrace();
     }
    ```

2.  设置 ACM listener，确保当配置被修改时，RCV\_INTERVAL\_TIME 参数即时更新。

    ```
    // 初始化的时候，给配置添加监听,配置变更会回调通知
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


## 设置消息队列 RocketMQ 消费延时逻辑 {#section_ybm_qjc_p2b .section}

完整实例如下。

**说明：** 

-   本例中 RCV\_INTERVAL\_TIME 参数的访问刻意没有加锁，原因不做赘述。
-   Aliyun ONS Client 不提供动态线程并发数，默认并发为 20。因此本例正好使用消费延时参数来动态调节 QoS。

```
//以下代码可直接贴在Main()函数里
Properties properties = new Properties();
properties.put(PropertyKeyConst.ConsumerId, "CID_consumer_group");
properties.put(PropertyKeyConst.AccessKey,"xxx");
properties.put(PropertyKeyConst.SecretKey, "yyy");
properties.setProperty(PropertyKeyConst.SendMsgTimeoutMillis, "3000");
// 设置 TCP 接入域名（此处以公共云生产环境为例）
properties.put(PropertyKeyConst.ONSAddr,
  "http://onsaddr-internet.aliyun.com/rocketmq/nsaddr4client-internet");
Consumer consumer = ONSFactory.createConsumer(properties);
consumer.subscribe(/*Topic*/"topic-name", /*Tag*/null, new MessageListener() 
{
    public Action consume(Message message, ConsumeContext context) {
        //消息队列 RocketMQ Subscribe QoS logical start, 
        // Each consuming process will sleep for RCV_INTERVAL_TIME seconds with 100 ms sleeping cycle.
        // Within each cycle, the thread will check RCV_INTERVAL_TIME in case it's set to a smaller value. 
        // RCV_INTERVAL_TIME <= 0 means no sleeping.
        int rcvIntervalTimeLeft = RCV_INTERVAL_TIME;
        while (rcvIntervalTimeLeft > 0) {
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
        //消息队列 RocketMQ Subscribe interval logical ends
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

## 运行结果 {#section_tcm_qjc_p2b .section}

在队列内的消息无限多的情况下，单机运行 Consumer 进行消费，分三段测试，分别运行约 5 分钟，通过 ACM 配置推送来达到以下效果。

-   RCV\_INTERVAL\_TIME = 100 ms
-   RCV\_INTERVAL\_TIME = 5000 ms
-   RCV\_INTERVAL\_TIME = 1000 ms

在单消息队列 RocketMQ 消费业务处理耗时约 100 ms 情况下、单机并发 20 线程的测试结果如下。

-   RCV\_INTERVAL\_TIME = 100 ms：平均消费性能约为 9000 tpm 左右
-   RCV\_INTERVAL\_TIME = 5000 ms：平均消费性能被限制到 200 tpm 左右
-   RCV\_INTERVAL\_TIME = 1000 ms：平均消费性能回升到 1100 tpm 左右

从以上结果中可以得出：消费和 tpm 成反比，整个过程中应用不中断，流控推送结果对分布式集群秒级生效。与预期结果相符。单机性能结果如下所示。

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/ex_rcv_qos.png) 

