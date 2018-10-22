# CLI Reference {#concept_pdp_rz5_42b .concept}

ACM provides a command line tool for you to retrieve, push, batch-import, and batch-export configuration data.

## How to install {#section_lv4_sz5_42b .section}

The command line tool is installed with ACM Python SDK.  For instructions on installation, see [ACM Python SDK User Guide](https://github.com/alibaba/acm-sdk-python).

After the installation, you can run  `acm -h`  to get help.

## Commands for administration {#section_afp_tz5_42b .section}

-   `acm add`

    This command creates a namespace, with a parameter in the format of `endpoint:namespace_id`.  The default endpoint is acm.aliyun.com, and the default port is 8080.  You can use parameter -ak, -sk, -n to specify the ak、sk and alias of the namespace.

    **Note:** If the namespace already exists, then the `add` command updates this namespace.

-   `acm use`

    This command switches the current namespace.  You can use `endpoint:namespace_id` or an alias.

-   `acm current`

    This command shows the current namespace.

-   `acm show`

    This command shows all namespaces.


## Commands for data operation {#section_rv4_sz5_42b .section}

By default, this command applies to the **current namespace**. Alternatively, you can use parameter `-n` to pass the `endpoint:namespace_id` or `alias` to specify a target namespace.

-   `acm pull`

    This command retrieves a configuration item and prints it to the console. The parameter is `group/data_id` \(for default `group`, only `data_id` is required\).

-   `acm push`

    This command pushes a configuration item. The parameter is `group/data_id` \(for default  `group`, only `data_id` is required\). By default, it reads content from the standard input stream \(pipeline command is allowed\). You can also specify the input file with parameter `-f`.  Files take precedence. They can’t be blank at the same time.

-   `acm export`

    This command exports all configurations in the namespace to local drive in a directory with the structure of  `group/data_id`. By default, it exports to a compressed file named `endpoint-namespace_id.zip`.

    -   You can specify the export file name with `-f`, or the export directory name with `-d`.  If directories are used, you can delete unwanted local configuration files with `--delete` \(hidden files and directories are ignored\). **Directories take precedence over files**.
    -   If you have more configuration files locally then on the server, and parameter `--delete` is specified, you’re prompted to delete the files or not before exporting. You can skip the prompt with `--force`.
-   `acm import`

    This command imports local configurations to the namespace. The local configurations must be organized in a directory with the structure of `group/data_id` . By default, it reads data from a compressed file named `endpoint-namespace_id.zip` .

    -   You can specify source file name with `-f` and source directory with `-d`. You can delete unwanted configuration files on the server with `--delete` \(hidden files and directories are ignored\). **Directories take precedence over files.**.
    -   If you have more configuration items on the server than locally, and parameter `--delete` is specified, you’re prompted to delete the configurations on the server or not. You can skip the prompt with `--force`.

