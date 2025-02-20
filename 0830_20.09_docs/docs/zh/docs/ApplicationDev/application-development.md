# 应用开发指南

本文档简要介绍应用程序开发需要的常用工具，以指导用户使用openEuler并基于openEuler进行应用程序开发。

[[toc]]

## 概述

本文档主要介绍如下四部分内容，以指导用户使用openEuler并基于openEuler进行代码开发。

-   在openEuler系统中安装和使用GCC编译器，并完成一个简单代码的开发、编译和执行。
-   在openEuler系统中使用JDK自带工具完成代码的编译和执行。
-   在openEuler系统中安装IntelliJ IDEA进行Java开发。
-   在本地或使用OBS（Open Build Service）创建RPM（The RPM Package Manager）软件包的方法。

## 读者对象

本文档适用于所有使用openEuler操作系统进行代码开发的用户。用户需要具备如下经验或能力：

-   具备Linux操作系统基础知识
-   了解Linux命令行的基本使用方法

## 符号约定

在本文中可能出现下列标志，它们所代表的含义如下。

|  符号   |说明  |
|:---  |:----  |
| ![](./figures/zh-cn_image_0229243712.png)|用于传递设备或环境安全警示信息，若不避免，可能会导致设备损坏、数据丢失、设备性能降低或其它不可预知的结果。<br/>“注意”不涉及人身伤害。|
| ![](./figures/zh-cn_image_0229243671.png)|用于突出重要/关键信息、最佳实践和小窍门等。<br/>“说明”不是安全警示信息，不涉及人身、设备及环境伤害。|

## 命令行格式约定

**表 1**  命令行格式的约定

|  格式   |含义  |
|:---  |:----  |
| **粗体**| 命令行关键字，即命令中保持不变、必须照输的部分，采用加粗字体表示。 |
|  *斜体*| 命令行参数，即命令中必须由实际值进行替代的部分，采用斜体表示。 |
| [ ] | 用“[ ]”括起来的部分表示在命令配置时是可选的。 |
| { x \| y \| ... } | 表示从两个或多个选项中选取一个。 |
| [ x \| y \| ... ] | 表示从两个或多个选项中选取一个或者不选。 |
|  { x \| y \| ... }\* | 表示从两个或多个选项中选取多个，最少选取一个，最多选取所有选项。 |
|  [ x \| y \| ... ]\* | 表示从两个或多个选项中选取一个、多个或者不选。 |
