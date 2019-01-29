# 通过 ECS 实例 RAM 角色访问 ACM {#concept_efq_g55_42b .concept}

如果借助 ECS 实例 RAM 角色，则无需配置 AccessKey（AK）即可访问 ACM，从而提高安全性。

## 背景信息 {#section_zsv_h55_42b .section}

以往，如果部署在 ECS 实例中的应用程序需要访问 ACM，则必须将 [Access Key（AK）](../../../../../intl.zh-CN/通用参考/创建AccessKey.md#)以配置文件或其他形式保存在 ECS 实例中，这在一定程度上增加了 AK 管理的复杂性，并且降低了 AK 的保密性。

现在，借助 [ECS 实例 RAM 角色](../../../../../intl.zh-CN/用户指南/实例/实例RAM角色/什么是实例 RAM 角色.md#)，您可以将 RAM 角色 和 ECS 实例关联起来，然后将 RAM 角色名称告知 ACM SDK （版本 1.0.8 及以上），此后无需配置 AK 即可访问 ACM。另外，借助 [RAM（访问控制）](../../../../../intl.zh-CN/产品简介/什么是 RAM？.md#)，您可以通过角色和授权策略实现不同实例对 ACM 具有不同访问权限的目的。例如，如果配置只读策略，关联了该角色的 ECS 就只能读取 ACM 的配置，而无法新增或修改 ACM 配置。

## 前提条件 {#section_btv_h55_42b .section}

ECS 实例的网络环境为专有网络（VPC）。

## 步骤一：创建 RAM 角色并配置授权策略 {#section_q5l_55j_pgb .section}

1.  登录 [RAM 控制台](https://ram.console.aliyun.com/)，在左侧导航栏中选择**RAM 角色管理**。
2.  在**RAM 角色管理**页面单击**新建角色**。
3.  在新建 RAM 角色对话框中完成以下步骤后单击**确定**。

    1.  选择**可信实体类型**为**阿里云账号**。
    2.  选择**受信云账号 ID**。
        -   若创建的角色是给您自己名下的 RAM 用户使用（比如授权移动 app 客户端直接操作 OSS 资源），请选择**当前云账号**为受信云账号。
        -   若创建的角色是给其他云账号名下的 RAM 用户使用（比如跨账号的资源授权），请选择**其他云账号**，并填写其他云账号的 ID。
    3.  编辑 **RAM 角色名称**和**备注**。
    **说明：** 此时成功创建角色后，角色没有任何权限。

4.  返回RAM 角色管理页面，单击新建的角色右侧**操作**栏的**添加权限**。
5.  在添加权限对话框中，通过关键词搜索授权策略 `AliyunACMFullAccess`，并单击该授权策略将其添加至右侧的**已选授**列表，然后单击**确定**。

    **说明：** 如果需要用到加解密配置功能，则还要添加`AliyunKMSCryptoAccess`授权策略。

    ![](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/db_add_AliyunACMFullAccess.png)

    此时，该角色已具备 ACM 的所有操作权限。


## 步骤二：为 ECS 实例授予该 RAM 角色 {#section_u4x_lck_pgb .section}

1.  登录[ECS控制台](https://ecs.console.aliyun.com/#/home)，单击左侧导航栏的**实例**。
2.  单击实例列表中目标 ECS 实例**操作**栏的**更多** \> **实例设置** \> **授予/收回 RAM 角色**。

    ![](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_ECS_instance.png)

3.  在授予/收回 RAM 角色对话框中选择 **RAM 角色**为[步骤一](#section_q5l_55j_pgb)创建的 RAM 角色。

    **说明：** 若您还未创建 RAM 角色，请单击**创建 RAM 角色**进行创建。

    ![](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_grant_ECS_permission.png)


## 步骤三：将 RAM 角色名称告知 ACM SDK 并访问配置 {#section_fgx_m55_42b .section}

将 RAM 角色名称告知 ACM SDK（版本 1.0.8 及以上）方法有两种：通过 JVM 参数设置和通过代码传参设置。

**说明：** JVM 参数设置方式优先级高于代码传参方式。

-   通过 JVM 参数设置：

    -   格式：`-Dram.role.name=$ramRoleName`

    -   示例：`-Dram.role.name=ECS-RAM`

-   通过代码传参设置：

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

-   [Access Key（AK）](../../../../../intl.zh-CN/通用参考/创建AccessKey.md#)
-   [RAM（访问控制）](../../../../../intl.zh-CN/产品简介/什么是 RAM？.md#)
-   [什么是实例 RAM 角色](../../../../../intl.zh-CN/用户指南/实例/实例RAM角色/什么是实例 RAM 角色.md#)
-   [管理 RAM 角色](../../../../../intl.zh-CN/用户指南/身份管理/RAM 角色身份/管理 RAM 角色.md#)
-   [RAM 授权](../../../../../intl.zh-CN/用户指南/权限管理/授权管理/RAM 授权.md#)
-   [借助于实例 RAM 角色访问其他云产品](../../../../../intl.zh-CN/最佳实践/借助于实例 RAM 角色访问其他云产品.md#)
-   [环境准备](../../../../../intl.zh-CN/SDK 参考/ACM Java Native SDK/环境准备.md#)

