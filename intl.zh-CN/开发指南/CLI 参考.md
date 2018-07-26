# CLI 参考 {#concept_pdp_rz5_42b .concept}

ACM 提供了命令行工具，方便您快速获取、推送、批量导入和导出配置数据。

## 安装方法 {#section_lv4_sz5_42b .section}

命令行工具随 ACM Python SDK 一起安装。安装方法请参考 [ACM Python SDK 用户指南](https://github.com/alibaba/acm-sdk-python)。

安装完毕后，可使用 `acm -h` 命令获得帮助。

## 命令参考：管理类命令 {#section_afp_tz5_42b .section}

-   `acm add`

    添加一个命名空间，参数格式为 `endpoint:namespace_id`。默认 endpoint 为 acm.aliyun.com，默认端口为 8080。可使用 -ak、-sk、-n 参数来分别指定该命名空间的 ak、sk 和别名。

    **说明：** 若命名空间已存在，则 `add` 命令将被视为更新该命名空间。

-   `acm use`

    切换当前命名空间。可使用 `endpoint:namespace_id` 或别名。

-   `acm current`

    查看当前命名空间。

-   `acm show`

    查看所有命名空间。


## 命令参考：数据操作类命令 {#section_rv4_sz5_42b .section}

以下命令默认作用于**当前命名空间**。也可使用 `-n` 参数传入 `endpoint:namespace_id` 或`别名`来指定要操作的命名空间。

-   `acm pull`

    获取一个配置项并打印到控制台，参数为 `group/data_id`（对于默认 `group` 可只填写 `data_id`）。

-   `acm push`

    推送一个配置项，参数为 `group/data_id`（对于默认 `group` 可只填写 `data_id`）。默认将从标准输入流中读取内容（可使用管道命令），也可以使用 `-f` 参数指定输入文件。文件的优先级高，不可同时为空。

-   `acm export`

    将命名空间下的所有配置导出到本地，导出的文件遵循 `group/data_id` 的目录结构，默认导出到名为 `endpoint-namespace_id.zip` 的压缩文件中。

    -   可使用 `-f` 指定导出文件名，使用 `-d` 指定导出目录。在使用目录的条件下，可使用 `--delete` 参数来删除本地的多余配置文件（隐藏文件和目录会被忽略），**目录优先级高于文件**。
    -   如果本地配置文件多于服务器配置，并指定了 `--delete` 参数，则导出前会询问是否删除文件，可使用 `--force` 跳过询问。
-   `acm import`

    将本地配置文件导入到命名空间，导入的文件需要遵循 `group/data_id` 的目录结构，默认将从名为 `endpoint-namespace_id.zip` 的压缩文件中读取数据。

    -   可使用 `-f` 指定源文件名，使用 `-d` 指定源目录，使用 `--delete` 参数来删除服务端的多余配置文件（隐藏文件和目录会被忽略），**目录优先级高于文件**。
    -   如果服务器配置项多于本地文件，并指定了 `--delete` 参数，则导入前会询问是否删除服务器端配置，可使用 `--force` 跳过询问。

