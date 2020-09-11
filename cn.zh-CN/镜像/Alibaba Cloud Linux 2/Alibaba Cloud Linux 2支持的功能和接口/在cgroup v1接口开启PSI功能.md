# 在cgroup v1接口开启PSI功能

在Linux的内核中PSI功能只支持cgroup v2接口。为了您在使用cgroup v1接口时，也可以通过PSI功能监控CPU、内存及IO性能异常等信息。Alibaba Cloud Linux 2在内核版本4.19.81-17.al7中为cgroup v1接口提供了PSI功能。本文主要介绍如何在cgroup v1接口开启PSI功能并查询相关信息。

PSI（Pressure Stall Information）是一个可以监控CPU、内存及IO性能异常的内核功能。有关PSI功能的详细信息，您可以通过内核文档`Documentation/accounting/psi.txt`了解，内核文档包含在Alibaba Cloud Linux 2的Debuginfo包和源码包内，下载Debuginfo包和源码包请参见[使用Alibaba Cloud Linux 2](/cn.zh-CN/镜像/Alibaba Cloud Linux 2/Aliyun Linux 2概述.md)。

## 为cgroup v1接口开启PSI功能

默认情况下cgroup v1接口的PSI功能为关闭状态。按照以下步骤开启PSI功能。

1.  运行`grubby`命令，修改启动参数。

    参数`args`中默认为`"psi=1"`，表示cgroup v2启用PSI功能。将参数修改为`"psi=1 psi_v1=1"`，表示Alibaba Cloud Linux 2为cgroup v1接口开启PSI功能。 本示例中内核版本为`4.19.81-17.al7.x86_64`，您在操作中需要更换为实际的内核版本，内核版本的查看命令为`uname -a`。

    ```
    sudo grubby --update-kernel="/boot/vmlinuz-4.19.81-17.al7.x86_64" --args="psi=1 psi_v1=1"
    ```

2.  重启系统使该功能生效。

    ```
    sudo reboot
    ```


## 确认cgroup v1接口的PSI功能已启用

系统重启后，您可以执行命令，确认内核`/proc/cmdline`中已启用cgroup v1接口的PSI功能。

```
cat /proc/cmdline | grep "psi=1 psi_v1=1"
```

## 查询CPU、内存及IO的监控数据

当您开启cgroup v1接口的PSI功能时，CPU、内存及IO的PSI监控数据均会输出到cpuacct控制器下，您可以通过以下命令查看详细的监控数据。

```
cat /sys/fs/cgroup/cpuacct/cpu.pressure
cat /sys/fs/cgroup/cpuacct/memory.pressure
cat /sys/fs/cgroup/cpuacct/io.pressure
```

