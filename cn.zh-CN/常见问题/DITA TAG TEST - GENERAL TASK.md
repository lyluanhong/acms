# DITA TAG TEST - GENERAL TASK {#task_eh5_tqn_hfb .task}

我是 context 前面的 prereq。

我是 context。

我是 context 后面的 prereq。

## 本 Section 测试 image {#section_znw_tmn_hfb .section}

本段落内嵌了一个图像。本段落内嵌了一个图像。本段落内嵌了一个图像。本段落内嵌了一个图像。本段落内嵌了一个图像。本段落内嵌了一个图像。![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/test/docker.png)

本段下方有一个不包含 fig 的图像。本段下方有一个不包含 fig 的图像。本段下方有一个不包含 fig 的图像。本段下方有一个不包含 fig 的图像。\(placement = break\)

![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/arms/pg_bm_overview.png)

本段下方有一个包含 fig 的图像。本段下方有一个包含 fig 的图像。本段下方有一个包含 fig 的图像。本段下方有一个包含 fig 的图像。

 ![](http://aliware-images.oss-cn-hangzhou.aliyuncs.com/arms/pg_bm_overview.png "我是有 fig 的图像 placement = inline") 

接下来测试 note 类型支持。

**注意：** Attention

警告：

Caution

禁止

Danger

**说明：** Fastpath

**注意：** Important

**说明：** Note

**说明：** Notice

**说明：** Remember

**注意：** Restriction

**说明：** Tip

**警告：** Troubleshooting

**警告：** Warning

测试 Term。大括号里的“术语 Terminology”是一个Term。\{假文术语 Terminology假文\}

## 本 section 测试 sectiondiv {#section_cxr_vmn_hfb .section}

我是 sectiondiv 内容。

## 本section测试definition list {#section_vth_bsn_hfb .section}

 term1
 :   term1 definition

  term2
 :   term2 definition

  term3
 :   term3 definition

  term4
 :   term4 definition

 ## 本 section 测试 inline tags {#section_tdh_3nn_hfb .section}

我是apiname。

我是`codeph`。

我是cmdname。

我是parmname。我有好几个options。

我是varname。

我是filepath。

我是userinput。

我是wintitle。

我是**uicontrol**。

我是**menucascade 1** \> **menucascade 2**。

我是

```screen
sreen
```

。

我是systemoutput。

## 本 section 测试 importance attribute {#section_kpp_k4n_hfb .section}

-   ul item
-   ul item - default
-   ul item - deprecated
-   ul item - high
-   ul item - low
-   ul item - normal
-   ul item - obsolete
-   ul item - optional
-   ul item - recommended
-   ul item - required
-   ul item - urgent
-   ul item

填充段落。

1.  ol item
2.  ol item - default
3.  ol item - deprecated
4.  ol item - high
5.  ol item - low
6.  ol item - normal
7.  ol item - obsolete
8.  ol item - optional
9.  ol item - recommended
10. ol item - required
11. ol item - urgent
12. ul item

## Steps只能有一组，且后面不能放Section {#section_gth_msn_hfb .section}

1.  步骤一：我是第1个stepsection
2.  我是第1步。 我是第1步的info。
3.  我是第2步（optional）。 
4.  我是第3步。 

    **说明：** 我是info里的note。

5.  我是第4步（required）。 
6.  我是第5步。 

    **说明：** 

    -   我是info里的note1。
    -   我是info里的note2。
7.  步骤二：我是第2个stepsection
8.  我是第6步。 
    1.  我是6.1步。 
    2.  我是6.2步。 
    3.  我是6.3步。 
9.  我是第7步。 
    -   我是choice 1
    -   我是choice 2
    -   我是choice 3
10. 我是第8步。 
11. 步骤三：我是第3个stepsection
12. 我是第9步。 我是stepresult。
13. 我是第10步。 例如，我是stepxmp。
14. 我是第11步。 

Result 必须在 postreq 之前。

## 我是example的title { .example}

我是example正文。example不论在哪里插入，似乎都会自动出现在result之后，postreq之前。

我是 postreq。

我是第2个postreq。

