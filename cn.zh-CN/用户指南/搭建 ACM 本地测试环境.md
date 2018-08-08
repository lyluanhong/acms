# 搭建 ACM 本地测试环境 {#concept_em5_mx5_42b .concept}

ACM 包提供了本地测试 ACM-SDK 的能力，应用无需连上远程 ACM 即可在本地获取配置进行测试。

目前 ACM 包支持 Mac、Linux 和 Windows 平台，JDK 1.8 及以上版本。ACM 本地包和 ACM 官网的功能对比如下。

|对比项|ACM 官网|ACM 本地|
|---|------|------|
|配置操作|支持|支持|
|历史版本|支持|不支持|
|监听查询|支持|不支持|
|推送轨迹|支持|不支持|
|命名空间|支持|不支持|

## ACM 部署 {#section_njh_ly5_42b .section}

1.  下载 acm-server 包到本地（链接：[acm-server.tar.gz](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/66462/cn_zh/1523934799094/acm-server.tar.gz?spm=0.0.0.0.IhU2Tv&file=acm-server.tar.gz)）。
2.  解压文件，当前目录下会生成 acm-server 目录。

    ```
    tar xzvf acm-server.tar.gz
    ```

3.  进入 acm-server 目录，执行启动脚本，运行 acm-server。

    ```
    cd acm-server/bin
    sudo sh catalina.sh run
    ```

4.  在浏览器中访问 localhost:8080/diamond-server。

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/66462/cn_zh/1519974589078/%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87ACM1.png) 

    出现以上登录界面则说明 acm-server 已经启动。


## 在本地控制台创建配置 {#section_wks_px5_42b .section}

1.  在浏览器输入localhost:8080/diamond-server，进入页面。
2.  在配置管理页面单击**新建配置**。

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/66462/cn_zh/1519974673918/%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87ACM2.png) 

3.  输入配置的 Data ID、Group ID 和内容，并单击**发布**，即可创建新的配置。

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/66462/cn_zh/1519974839563/%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87ACM3.png) 


## 在本地控制台查询配置 {#section_jbl_py5_42b .section}

1.  在**配置管理**页面输入要查询的 Data ID 和 Group ID，单击**查询**。

    **说明：** 如果只输入 Data ID，则查询具有该 Data ID 的所有配置。如果不输入任何查询条件，则查询所有配置。

2.  在查询结果列表的**操作**栏中，单击**详情**。

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/66462/cn_zh/1519975078240/%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87ACM5.png) 

3.  在**配置内容**文本框中查看配置内容。

## 在本地控制台修改配置 {#section_g2d_qy5_42b .section}

1.  在查询结果列表的**操作**栏中，单击**编辑**。

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/66462/cn_zh/1519975221247/%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87ACM7.png) 

2.  在**配置内容**文本框中修改配置内容，并单击**发布**。

## 删除配置 {#section_k22_ry5_42b .section}

1.  在查询结果列表的**操作**栏中，单击**删除**。

     ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/66462/cn_zh/1519975344556/%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87ACM9.png) 

2.  在弹出对话框中单击**确定**。

## ACM-SDK 测试 {#section_ft1_sy5_42b .section}

1.  绑定 acm.aliyun.com 域名。

    由于 ACM-SDK 使用 acm.aliyun.com 域名连接 ACM，为 ACM-SDK 能访问本地，需要修改 host 文件、将 acm.aliyun.com 域名绑定到本地 IP 127.0.0.1。

    ```
    vim /etc/hosts
    添加  127.0.0.1 acm.aliyun.com
    ```

2.  编写 ACM-SDK 测试用例。

    ACM-SDK 中填入创建的配置 dataId 和 group，如果能成功获取配置，则说明 ACM 本地测试环境部署成功。

    ```
    public class ACMTest {
     public static void main(String[] args) throws ConfigException, InterruptedException {
         // 初始化配置服务，控制台通过示例代码自动获取下面参数。入参分别为endpoint,namespace,accessKey,secretKey(为acm私有秘钥，不能用阿里云的)
         ConfigService.init("acm.aliyun.com", "", "", "");
         String content = ConfigService.getConfig("acm.test", "DEFAULT_GROUP", 6000);
         System.out.println(content);
         // 初始化的时候，给配置添加监听,配置变更会回调通知
         ConfigService.addListener("acm.test", "DEFAULT_GROUP", new ConfigChangeListener() {
             public void receiveConfigInfo(String configInfo) {
                 // 当配置更新后，通过该回调函数将最新值吐给用户。
                 // 注意回调函数中不要做阻塞操作，否则阻塞通知线程。
                 System.out.println(configInfo);
             }
         });
         // sleep不让主线程
         Thread.sleep(100000);
     }
    }
    ```


