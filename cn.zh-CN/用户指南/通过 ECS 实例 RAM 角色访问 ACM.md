# 通过 ECS 实例 RAM 角色访问 ACM {#concept_efq_g55_42b .concept}

本文介绍了如何通过 ECS 实例 RAM 角色来访问 ACM。

## 概述 {#section_zsv_h55_42b .section}

以往，如果部署在 ECS 实例中的应用程序需要访问 ACM，则必须将 Access Key ID 和 Access Key Secret（以下简称 AK）以配置文件或其他形式保存在 ECS 实例中，这在一定程度上增加了 AK 管理的复杂性，并且降低了 AK 的保密性。

现在，借助 [ECS 实例 RAM 角色](../../../../intl.zh-CN/用户指南/实例/实例RAM角色/什么是实例 RAM 角色.md#)，您可以将 RAM 角色 和 ECS 实例关联起来，然后将 RAM 角色名称告知 ACM SDK （版本 1.0.8 以上），此后无需配置 AK 即可访问 ACM。另外，借助 [RAM（访问控制）](../../../../intl.zh-CN/产品简介/什么是RAM？.md#)，您可以通过角色和授权策略实现不同实例对 ACM 具有不同访问权限的目的。例如，如果配置只读策略，关联了该角色的 ECS 就只能读取 ACM 的配置，而无法新增或修改 ACM 配置。

## 前提条件 {#section_btv_h55_42b .section}

网络环境为 VPC。

## 第 1 步：创建 RAM 角色并配置授权策略 {#section_kf3_j55_42b .section}

1.  登录 [RAM 控制台](https://ram.console.aliyun.com/)，在左侧导航栏单击**角色管理**。
2.  单击页面右上角的**新建角色**。
3.  在创建角色对话框中完成以下步骤。

    1.  在**选择角色类型**子页，单击**服务角色**。
    2.  在**填写类型信息**子页，选择 **ECS 云服务器**。
    3.  在**配置角色基本信息**子页，输入自定义的**角色名称**和**备注**，并单击**创建**。
    **说明：** 此时成功创建角色后，角色没有任何权限。

4.  在角色管理页面，单击该角色右侧**操作**栏的**授权**。
5.  在编辑角色授权策略对话框中，通过关键词搜索授权策略 `AliyunACMFullAccess`，并单击 **\>** 按钮将该授权策略移至右侧的**已选授权策略名称**列表，然后单击**确定**。如果需要用到加解密配置功能，则还要添加`AliyunKMSCryptoAccess`授权策略。

    ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/dg_edit_role_authorization_policy.png) 

    此时，该角色已具备 ACM 的所有操作权限。


## 第 2 步：为 ECS 实例授予该 RAM 角色 {#section_mql_l55_42b .section}

1.  登录[ECS控制台](https://ecs.console.aliyun.com/#/home)，单击左侧导航栏的**实例**。
2.  单击列表中目标 ECS 实例**操作**栏的**更多**，并选择**授予/收回 RAM 角色**，为该实例授予上一步新建的角色。

## 第 3 步：将 RAM 角色名称告知 ACM SDK 并访问配置 {#section_fgx_m55_42b .section}

将 RAM 角色名称告知 ACM SDK（版本 1.0.8 以上）的方法有两种：

-   通过 JVM 参数设置：`-Dram.role.name=$ramRoleName`（例如 `-Dram.role.name=ECS-RAM`）
-   通过代码传参设置

**说明：** JVM 参数设置方式优先级高于代码传参方式。

代码传参方式如下：

```
import java.util.Properties;
import com.alibaba.edas.acm.ConfigService;
import com.alibaba.edas.acm.exception.ConfigException;
// 示例代码，仅用于示例测试
public class ACMTest {
    public static void main(String[] args) {
        try {
            Properties properties = new Properties();
            // endpoint 可以从 ACM 控制台“命名空间详情”或“示例代码”中获取
            properties.put("endpoint", "$endpoint");
            // namespace 可以从 ACM 控制台“命名空间详情”或“示例代码”中获取
            properties.put("namespace", "$namespace");
            // 刚刚新建并绑定到 ECS 实例的 RAM 角色名称，如“ECS-RAM”
            properties.put("ramRoleName", "$ramRoleName");
            ConfigService.init(properties);
            // 主动获取配置
            String content = ConfigService.getConfig("${dataId}", "${group}", 6000);
            System.out.println(content);
        } catch (ConfigException e) {
            e.printStackTrace();
        }
    }
}
```

## 相关文档 {#section_f11_s55_42b .section}

-   [RAM（访问控制）](../../../../intl.zh-CN/产品简介/什么是RAM？.md#)
-   [什么是实例 RAM 角色](../../../../intl.zh-CN/用户指南/实例/实例RAM角色/什么是实例 RAM 角色.md#)
-   [借助于实例 RAM 角色访问其他云产品](../../../../intl.zh-CN/最佳实践/借助于实例 RAM 角色访问其他云产品.md#)
-   [环境准备](../../../../intl.zh-CN/SDK 参考/ACM Java Native SDK/环境准备.md#)

