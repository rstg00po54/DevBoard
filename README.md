# 嵌入式 Android / ARM / RISC-V 开发参考

> 本文档汇总 Android 系统版本、JDK、Ubuntu、工具链以及板子 CPU 对应信息，方便交叉编译和嵌入式开发。

---

## 目录

- [1. ARM / RISC-V 板子 CPU 与工具链](#1-arm--risc-v-板子-cpu-与工具链)
- [2. Android 系统版本与推荐编译环境](#2-android-系统版本与推荐编译环境)
- [3. Android JDK 与 Ubuntu 对应表](#3-android-jdk-与-ubuntu-对应表)
- [4. Android 历史版本与甜品代号](#4-android-历史版本与甜品代号)
- [5. 工具链 / GCC / glibc 对照表](#5-工具链--gcc--glibc-对照表)

---

## 1. ARM / RISC-V 板子 CPU 与工具链

<details>
<summary>展开 / 收起</summary>

| CPU / 核心               | 板子            | 架构                         | 内核 / Android           | 推荐工具链 (GCC 版本)                                         | 推荐 glibc                | 备注                                         |
|--------------------------|----------------|-----------------------------|-------------------------|----------------------------------------------------------------|--------------------------|----------------------------------------------|
| s5pv210 / Cortex-A8      | TQ210           | ARMv7-A 32bit               | 3.0.8 / 4.0.4           | arm-linux-gnueabihf GCC 7.5.0 或 9.3.0                        | 2.17~2.19                | 硬浮点优先，若板子只支持软浮点，可额外加一套 arm-linux-gnueabi |
| 4412 / Cortex-A9         | itop-4412       | ARMv7-A 32bit               | -                        | arm-linux-gnueabihf GCC 7.5.0 或 9.3.0                        | 2.17~2.19                | 硬浮点                                     |
| s5p6818 / Cortex-A53     | itop-6818       | ARMv8-A                      | 4.4 / 5.1 / 7.1          | arm-linux-gnueabihf GCC 7.5.0~9.3.0 或 aarch64-linux-gnu GCC 9.3.0~10.2.0 | 2.17~2.31                | 视编译 LVGL 9 选择 32bit/64bit 模式         |
| Allwinner A64 / Cortex-A53 | CQA64         | ARMv8-A 64bit               | 3.1 / 5.1 / 6 / 7.1      | aarch64-linux-gnu GCC 9.3.0 或 10.2.0                        | 2.19~2.31                | 一套工具链即可                               |
| Allwinner A50 / Cortex-A7  | QIHUA-x50     | ARMv7-A                      | 4.9                      | arm-linux-gnueabihf GCC 7.5.0 或 9.3.0                        | 2.17~2.19                | 如果确认是 ARMv7，选 32bit 工具链           |
| d1s / Xuantie C906       | K4B             | RISC-V 64bit                | 5.4                      | riscv64-linux-gnu GCC 9.2.0 或 10.2.0                          | 2.28~2.31                | 单核 RISC-V                                 |
| Allwinner A133 / Cortex-A53 | K5C          | ARMv8-A 64bit               | Android 10 / Ubuntu 22.04 | aarch64-linux-gnu GCC 9.3.0 或 10.2.0                         | 2.19~2.31                | 一套工具链即可                               |
| RK-3566 / Cortex-A55     | 泰山派           | ARMv8.2-A 64bit             | 4.19 / Android 11         | aarch64-linux-gnu GCC 9.3.0 或 10.2.0                         | 2.19~2.31                | 一套工具链即可                               |
| RK-3568 / Cortex-A55     | QIHUA-XR3568     | ARMv8.2-A 64bit             | 4.19 / Android 11         | aarch64-linux-gnu GCC 9.3.0 或 10.2.0                         | 2.19~2.31                | 一套工具链即可                               |
| A733 / Cortex-A55 + RISC-V | QIHUA-X733    | ARMv8.2-A 64bit + RISC-V 64bit | 5.15 / 6.6 / Android 15 | aarch64-linux-gnu GCC 9.3.0~10.2.0 + riscv64-linux-gnu GCC 9.2.0~10.2.0 | 2.19~2.31 + 2.28~2.31 | 混合板子需分别编译 ARM 和 RISC-V 程序      |

</details>

---

## 2. Android 系统版本与推荐编译环境

<details>
<summary>展开 / 收起</summary>

| Android 版本                  | 发布时间       | 推荐编译环境 (Ubuntu版本)         | 编译工具链                    | 备注 / 链接                                   |
|-------------------------------|---------------|---------------------------------|-------------------------------|---------------------------------------------|
| 4.0 ~ 4.3 (Ice Cream Sandwich ~ Jelly Bean) | 2011-2013     | Ubuntu 10.04 / 12.04             | GCC 4.6 ~ 4.8                 | 使用 make，repo 管理，clang 支持较弱; [GCC 镜像](http://mirrors.hust.edu.cn/gnu/gcc/) |
| 4.4 (KitKat)                  | 2013          | Ubuntu 12.04                     | GCC 4.8                       | 继续使用 make，repo 管理                      |
| 5.0 ~ 5.1 (Lollipop)          | 2014-2015     | Ubuntu 14.04                     | GCC 4.9 ~ clang 3.6           | 引入 clang 支持，make 为主                   |
| 6.0 (Marshmallow)             | 2015          | Ubuntu 14.04 / 16.04             | GCC 4.9 ~ clang 3.8           | 过渡期，clang 使用增加                        |
| 7.0 ~ 7.1 (Nougat)            | 2016-2017     | Ubuntu 16.04                     | Clang 3.8 ~ 3.9               | clang 使用成为主流; [LLVM](https://releases.llvm.org/) |
| 8.0 ~ 8.1 (Oreo)              | 2017-2018     | Ubuntu 16.04 / 18.04             | Clang 5.0                     | 引入 Soong 构建系统                           |
| 9.0 (Pie)                      | 2018          | Ubuntu 18.04                     | Clang 7.0                     | Soong 构建系统为主; [OpenJDK](https://mirrors.huaweicloud.com/openjdk/) |
| 10                             | 2019          | Ubuntu 18.04 / 20.04             | Clang 9.0 ~ 10.0              | 彻底弃用 gcc，bazel 辅助构建; [OpenJDK](https://repo.huaweicloud.com/java/jdk/) |
| 11                             | 2020          | Ubuntu 20.04                     | Clang 11                      | Soong 和 bazel 协作                           |
| 12                             | 2021          | Ubuntu 20.04                     | Clang 12                      | 继续优化构建，支持 AOSP 最新硬件             |
| 13 (Tiramisu)                  | 2022          | Ubuntu 20.04                     | Clang 14                      | Soong 主导，Bazel 渐进式引入                 |
| 14 (Upside Down Cake)          | 2023          | Ubuntu 22.04                     | Clang 17                      | Soong + Bazel 深度整合，CI/CD 优化            |
| 15 (Vanilla Ice Cream)         | 2024          | Ubuntu 22.04 / 24.04             | Clang 18                      | Bazel 支持进一步增强，Soong 继续兼容，面向 RISC-V 等新架构 |

</details>

---

## 3. Android JDK 与 Ubuntu 对应表

<details>
<summary>展开 / 收起</summary>

| Android 版本                  | API Level | JDK 要求                  | 推荐 Ubuntu 版本                     |
|-------------------------------|-----------|---------------------------|------------------------------------|
| 4.0 ~ 4.3 (ICS ~ JB)          | 14 ~ 18   | OpenJDK 6                 | Ubuntu 10.04 ~ 12.04               |
| 4.4 (KitKat)                  | 19        | OpenJDK 7                 | Ubuntu 12.04                        |
| 5.0 ~ 5.1 (Lollipop)          | 21 ~ 22   | OpenJDK 7 / 8             | Ubuntu 14.04；JDK 8 支持更好       |
| 6.0 (Marshmallow)             | 23        | OpenJDK 8                 | Ubuntu 14.04 ~ 16.04               |
| 7.0 ~ 7.1 (Nougat)            | 24 ~ 25   | OpenJDK 8                 | 编译脚本会检查版本                  |
| 8.0 ~ 8.1 (Oreo)              | 26 ~ 27   | OpenJDK 8                 | 推荐 Ubuntu 16.04                   |
| 9.0 (Pie)                     | 28        | OpenJDK 8                 | Ubuntu 18.04                        |
| 10 (Q)                        | 29        | OpenJDK 9 (预览) / OpenJDK 8 | JDK 8 仍可用，但建议用官方预构建环境 |
| 11 (R)                        | 30        | OpenJDK 11                | 官方 AOSP docker 使用 JDK 11        |
| 12 / 12L (S)                  | 31 ~ 32   | OpenJDK 11                | 推荐 Ubuntu 20.04                   |
| 13 (Tiramisu)                 | 33        | OpenJDK 11                | 官方推荐 Ubuntu 20.04               |
| 14 (Upside Down Cake)         | 34        | OpenJDK 17                | 官方已切换到 Ubuntu 22.04           |
| 15 (Vanilla Ice Cream)        | 35        | OpenJDK 17 / 21           | 推荐 Ubuntu 22.04 / 24.04，Google 内部已测试 JDK 21 |

</details>

---

## 4. Android 历史版本与甜品代号

<details>
<summary>展开 / 收起</summary>

| 版本号      | 正式名称        | 甜品代号                        |
|------------|----------------|--------------------------------|
| 1.5        | Android 1.5    | Cupcake（纸杯蛋糕）            |
| 1.6        | Android 1.6    | Donut（甜甜圈）                |
| 2.0 / 2.1  | Android Eclair | Eclair（闪电泡芙）             |
| 2.2        | Android Froyo  | Froyo（冻酸奶 Frozen Yogurt）  |
| 2.3        | Android Gingerbread | Gingerbread（姜饼）        |
| 3.0 / 3.1 / 3.2 | Android Honeycomb | Honeycomb（蜂巢，仅平板） |
| 4          | Android Ice Cream Sandwich | Ice Cream Sandwich（冰淇淋三明治） |
| 4.1 / 4.2 / 4.3 | Android Jelly Bean | Jelly Bean（软糖豆）      |
| 4.4        | Android KitKat | KitKat（奇巧巧克力）           |
| 5.0 / 5.1  | Android Lollipop | Lollipop（棒棒糖）           |
| 6          | Android Marshmallow | Marshmallow（棉花糖）     |
| 7.0 / 7.1  | Android Nougat | Nougat（牛轧糖）              |
| 8.0 / 8.1  | Android Oreo   | Oreo（奥利奥）                 |
| 9          | Android Pie    | Pie（派）                       |
| 10         | Android 10     | Quince Tart（榅桲馅饼，内部代号） |
| 11         | Android 11     | Red Velvet Cake（红丝绒蛋糕，内部代号） |
| 12         | Android 12     | Snow Cone（刨冰，内部代号）    |
| 13         | Android 13     | Tiramisu（提拉米苏，内部代号） |
| 14         | Android 14     | Upside Down Cake（倒置蛋糕，内部代号） |
| 15         | Android 15     | Vanilla Ice Cream（香草冰淇淋，内部代号） |

</details>

---

## 5. 工具链 / GCC / glibc 对照表

<details>
<summary>展开 / 收起</summary>

| 工具链 / 编译器             | GCC 版本       | glibc 版本       | 最低内核版本    | 备注 / 板子示例                     | C++17 支持          |
|----------------------------|---------------|----------------|----------------|-------------------------------------|-------------------|
| Linaro 2010 / Sourcery 2009 | 4.4 – 4.5     | 2.7 – 2.8      | 2.4 – 2.6.14   | i.MX / S5P6818 / ARM9/11            | ❌ 只支持 C++03/C++11 |
| Linaro 2012                 | 4.6 – 4.7     | 2.8 – 2.9      | 2.6.14 – 2.6.16 | Cortex-A8 / i.MX6                    | ❌ C++11           |
| Linaro 2013                 | 4.7 – 4.8     | 2.10 – 2.11    | 2.6.18 – 2.6.32 | Cortex-A7/A9 / RK3188                | ❌ 部分 C++11       |
| Linaro 2015                 | 4.9           | 2.17           | 2.6.32         | Cortex-A7/A9 / S5P6818               | ⚠️ 基本 C++11      |
| Linaro 2016 – 2017          | 5.x           | 2.19 – 2.23    | 2.6.32         | ARMv7 / Cortex-A53                     | ⚠️ 基本 C++14      |
| Linaro 2018                 | 7.x           | 2.27 – 2.28    | 3.2            | ARMv8 32/64-bit / RK3399               | ✅ 大部分 C++17    |
| Linaro 2019 – 2021          | 8.x – 9.x     | 2.28 – 2.31    | 3.2            | ARMv8 64-bit / 新板子                  | ✅ C++17           |
| Linaro 2022+                | 10.x – 12.x   | 2.32 – 2.39    | 3.2+           | 最新 ARM64 / SDK                        | ✅ C++17 全部特性  |

</details>
