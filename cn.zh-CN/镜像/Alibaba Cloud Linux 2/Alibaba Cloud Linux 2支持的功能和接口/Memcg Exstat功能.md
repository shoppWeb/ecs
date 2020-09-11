# Memcg Exstat功能

本文主要介绍Alibaba Cloud Linux 2在`4.19.91-18.al7`内核版本开始支持的Memcg Exstat（Extend/Extra）功能。

Alibaba Cloud Linux 2所支持的Memcg Exstat功能，相较于社区版内核额外提供了以下memcg统计项：

-   在cgroup v1接口支持了memory.events、memory.events.local及memory.stat接口。
-   增加memcg全局最低水位调整产生的延迟统计。
-   增加memcg后台异步回收产生的延迟统计。

## 功能概述

Alibaba Cloud Linux 2内核功能均基于内核接口来实现，本节主要介绍各个功能对应的接口实现方式。

|功能|说明|
|--|--|
|memory events统计|在社区版内核的cgroup v2接口中存在memory.events和memory.events.local两个接口，能够查询memcg内发生特定事件的次数统计信息。接口详情请参见内核文档[cgroup-v2.rst](https://www.kernel.org/doc/Documentation/admin-guide/cgroup-v2.rst)。

Alibaba Cloud Linux 2在cgroup v1接口新增了memory.events和memory.events.local接口。

**说明：** memcg根组目录下不存在对应的接口文件。 |
|memory workingset统计|在社区版内核的cgroup v2接口中存在memory.stat接口，该接口提供了`workingset refault`、`workingset activate`及`workingset nodereclaim`三个统计项，接口详情请参见内核文档[cgroup-v2.rst](https://www.kernel.org/doc/Documentation/admin-guide/cgroup-v2.rst)。

Alibaba Cloud Linux 2在cgroup v1接口新增了memory.stat接口，并支持查询`workingset refault`、`workingset activate`、`workingset nodereclaim`及`workingset restore`四个统计项。 新增的统计项`workingset restore`官方说明，请参见如下内容。

```
+    workingset_restore
+    Number of restored pages which have been detected as an active
+    workingset before they got reclaimed.
``` |
|memcg全局最低水位调整产生延迟统计|Alibaba Cloud Linux 2支持memcg全局最低水位线分级功能，详情请参见[Memcg全局最低水位线分级](/cn.zh-CN/镜像/Alibaba Cloud Linux 2/Alibaba Cloud Linux 2支持的功能和接口/Memcg全局最低水位线分级.md)。

Alibaba Cloud Linux 2在memcg.exstat接口中，提供了memcg超过偏移后的全局最低水位线导致的抑制时间统计，即统计项`wmark_min_throttled_ms`。

**说明：** 该统计项将递归到父组，且memcg根组目录下不存在该接口文件。

统计说明：

-   统计输出文件：memory.exstat
-   统计输出项：`wmark_min_throttled_ms`
-   统计输出单位：ms |
|memcg后台异步回收产生延迟统计|Alibaba Cloud Linux 2支持memcg后台异步回收功能，即memcg kswapd特性，详情请参见[Memcg后台异步回收](/cn.zh-CN/镜像/Alibaba Cloud Linux 2/Alibaba Cloud Linux 2支持的功能和接口/Memcg后台异步回收.md)。

Alibaba Cloud Linux 2在memcg.exstat接口中，提供了memcg后台异步回收产生的延迟统计（包含回收过程中的阻塞时间和实际工作时间），即统计项`wmark_reclaim_work_ms`。

**说明：** 该统计项将递归到父组，且memcg根组目录下不存在该接口文件。

统计说明：

-   统计输出文件：memory.exstat
-   统计输出项：`wmark_reclaim_work_ms`
-   统计输出单位：ms |

## 功能使用示例

本示例在memcg的挂载点（一般情况下为/sys/fs/cgroup/memory）创建测试文件，确认该memcg文件内包含memory.events、memory.events.local及memory.exstat三个控制接口，并确认memory.stat控制接口内包含`workingset`统计项。

1.  运行以下命令创建测试文件。

    ```
    mkdir /sys/fs/cgroup/memory/test
    ```

2.  分别查询memory.events、memory.events.local及memory.exstat接口。

    1.  运行以下命令查询memory.events接口。

        ```
        cat /sys/fs/cgroup/memory/test/memory.events
        ```

        查询结果示例如下所示。

        ```
        low 0
        high 0
        max 0
        oom 0
        oom_kill 0
        ```

    2.  运行以下命令查询memory.events.local接口。

        ```
        cat /sys/fs/cgroup/memory/test/memory.events.local
        ```

        查询结果示例如下所示。

        ```
        low 0
        high 0
        max 0
        oom 0
        oom_kill 0
        ```

    3.  运行以下命令查询memory.exstat接口。

        ```
        cat /sys/fs/cgroup/memory/test/memory.exstat
        ```

        查询结果示例如下所示。

        ```
        wmark_min_throttled_ms 0
        wmark_reclaim_work_ms 0
        ```

3.  运行以下命令确认memory.stat接口中是否包含`workingset`统计项。

    ```
    cat /sys/fs/cgroup/memory/test/memory.stat | grep workingset
    ```

    返回结果示例如下所，表示已包含`workingset`统计项。

    ```
    workingset_refault 0
    workingset_activate 0
    workingset_restore 0
    workingset_nodereclaim 0
    total_workingset_refault 0
    total_workingset_activate 0
    total_workingset_restore 0
    total_workingset_nodereclaim 0
    ```


