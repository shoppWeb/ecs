# Alibaba Cloud Linux 2已知问题

本文介绍了Alibaba Cloud Linux 2镜像的已知故障、故障涉及范围以及解决方法。

## 开启内核选项CONFIG\_PARAVIRT\_SPINLOCK可能导致性能问题

-   问题描述：开启内核选项CONFIG\_PARAVIRT\_SPINLOCK后，当ECS实例vCPU数量较多，且应用中有大量锁竞争操作时，应用性能会受到较大影响（例如， Nginx应用的短连接处理能力会因此大幅下降），您可能会在应用中观察到性能下降的问题。
-   修复方案：内核选项CONFIG\_PARAVIRT\_SPINLOCK在Alibaba Cloud Linux 2上默认处于关闭状态。如果您不确定如何处理内核问题，请勿开启CONFIG\_PARAVIRT\_SPINLOCK。

## 内核特性透明大页THP开关置为always可能会导致系统不稳定或性能下降

-   问题描述：在您的生产环境系统中，将透明大页THP（Transparent Huge Page）开关置为always，可能会引发系统不稳定和性能下降等问题。
-   修复方案：关于透明大页THP相关的性能调优方法，请参见[Alibaba Cloud Linux 2系统中与透明大页THP相关的性能调优方法](https://www.alibabacloud.com/help/doc-detail/161963.htm)。

## NFS v4.0版本中委托（Delegation）功能可能存在问题

-   问题描述：NFS委托（Delegation）功能在v4.0版本中可能存在问题。详情请参见[NFS委托功能v4.0版本](https://docs.oracle.com/cd/E19253-01/816-4555/rfsrefer-140/index.html)。
-   修复方案：使用NFS v4.0版本时，建议您不要开启Delegation功能。如需从服务器端关闭该功能，请参见[社区文档](https://docs.oracle.com/cd/E19253-01/816-4555/rfsadmin-965/index.html)。

## NFS v4.1/4.2版本中存在缺陷可能导致应用程序无法退出

-   问题描述：在NFS的v4.1和v4.2版本中，如果您在程序中使用异步I/O（AIO）方式下发请求，且在所有I/O返回之前关闭对应的文件描述符，有一定几率触发活锁，导致对应进程无法退出。
-   修复方案：该问题已在内核4.19.30-10.al7及以上版本中修复。由于该问题出现概率极低，您可根据实际需要决定是否升级内核修复该问题。如需修复，请在终端执行sudo yum update kernel -y命令，升级完成后重启系统即可。

    **说明：**

    -   升级内核版本可能会导致系统无法开机等风险，请谨慎操作。
    -   升级内核前，请确保您已创建快照或自定义镜像备份数据。具体操作，请参见[创建普通快照](/intl.zh-CN/快照/使用快照/创建普通快照.md)或[使用实例创建自定义镜像](/intl.zh-CN/镜像/自定义镜像/创建自定义镜像/使用实例创建自定义镜像.md)。

## Meltdown/Spectre漏洞修复会影响系统性能

-   问题描述：Alibaba Cloud Linux 2内核中，默认打开了针对处理器硬件高危安全漏洞Meltdown和Spectre的修复功能。由于此修复功能会影响系统性能，因此在常见的性能基准套件测试中，可能会观察到不同程度的性能下降现象。
-   修复方案：Meltdown和Spectre是英特尔芯片中发现的两个高危漏洞，攻击者可通过这两个漏洞来访问核心内存，从而窃取应用程序中的敏感信息，因此，建议您不要在系统中关闭此类高危漏洞的修复功能。但如果您对系统性能有极高要求，可以关闭该修复功能，具体操作请参见[Alibaba Cloud Linux 2系统中如何关闭CPU漏洞修复](https://www.alibabacloud.com/help/doc-detail/154567.htm)。

