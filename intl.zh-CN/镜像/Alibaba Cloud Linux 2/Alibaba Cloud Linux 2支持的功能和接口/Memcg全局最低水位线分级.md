# Memcg全局最低水位线分级

本文介绍Alibaba Cloud Linux 2在`4.19.91-18.al7`内核版本新增的memcg全局最低水位线分级功能。

在Linux内核中，全局内存回收对系统性能影响很大。当时延敏感型业务和资源消耗型任务共同部署时，资源消耗型任务时常会瞬间申请大量的内存，使得系统的空闲内存触及全局最低水位线（global wmark\_min），引发系统所有任务进入直接内存回收的慢速路径，引发时延敏感型业务的性能抖动。在此场景下，无论是全局kswapd后台回收还是memcg后台回收，都将无法处理该问题。

基于上述场景下的问题，Alibaba Cloud Linux 2新增了memcg全局最低水位线分级功能。在global wmark\_min的基础上，将资源消耗型任务的global wmark\_min上移，使其提前进入直接内存回收。将时延敏感型业务的global wmark\_min下移，使其尽量避免直接内存回收。这样当资源消耗型任务瞬间申请大量内存的时候，会通过上移的global wmark\_min将其短时间抑制，避免时延敏感型业务发生直接内存回收。等待全局kswapd回收一定量的内存后，再解除资源消耗型任务的短时间抑制。

## 功能接口说明

实现memcg全局最低水位线分级功能的接口为memory.wmark\_min\_adj。该接口的值，表示基于全局最低水位线（global wmark\_min）所作出的调整（adjustment）百分比。取值范围：-25 ~ 50，取值范围说明如下：

-   该接口创建时，继承父组的值（值为0），即默认值为0。
-   取值范围中的负值是基于调整范围`[0, WMARK_MIN]`的百分比，其中`WMARK_MIN`表示global wmark\_min的值，例如：

    ```
    memory.wmark_min_adj=-25, memcg WMARK_MIN is "WMARK_MIN + (WMARK_MIN - 0) * (-25%)"
    ```

    **说明：** 负值也表示global wmark\_min下移，即提高时延敏感型业务的内存子系统服务质量（memcg QoS）。

-   取值范围中的正值是基于调整范围`[WMARK_MIN, WMARK_LOW]`的百分比，其中`WMARK_MIN`和`WMARK_LOW`分别表示global wmark\_min和global wmark\_low的值，例如：

    ```
    memory.wmark_min_adj=50, memcg WMARK_MIN is "WMARK_MIN + (WMARK_LOW - WMARK_MIN) * 50%"
    ```

    **说明：** 正值也表示global wmark\_min上移，即降低资源消耗型任务的内存子系统服务质量（memcg QoS）。

-   当偏移后的global wmark\_min被触发后，会执行抑制操作，抑制操作的时间和超出的内存使用为线性比例关系。抑制时间的取值范围：1ms ~ 1000ms。

**说明：** memcg根组目录下不存在该接口文件。

## 接口注意事项

在多层级目录的memcg中，有一个`effective memory.wmark_min_adj`的概念，即最终生效的memory.wmark\_min\_adj值。具体规则是在memcg层级路径上遍历取最大值（中间节点的默认值0除外）。例如，有以下层级关系示例。

```
         root
         / \
        A   D
       / \
      B   C
     / \
    E   F
```

则各层级设置的接口值与最终生效的接口值，对应关系如下所示。

|层级|各层级设置的接口值|最终生效的接口值|
|--|---------|--------|
|A|-10|-10|
|B|-25|-10|
|C|0|0|
|D|50|50|
|E|-25|-10|
|F|50|50|

**说明：**

-   运行命令`cat /sys/fs/cgroup/memory/<memcg path>/memory.wmark_min_adj`输出的值为最终生效的值，其中变量`<memcg path>`是memcg的根路径。
-   本功能建议配合较高的全局最低水位线（global wmark\_min）来使用，例如将global wmark\_min值设置为2 GB或更高，您可以通过/proc/sys/vm/min\_free\_kbytes进行设置。

## 功能配置示例

示例一：为时延敏感型业务所在memcg配置全局最低水位线分级。

1.  运行命令`mkdir /sys/fs/cgroup/memory/test-lc`创建测试文件。

2.  运行命令`echo -25 > /sys/fs/cgroup/memory/test-lc/memory.wmark_min_adj`向接口写入值`-25`，提高时延敏感型业务的memcg QoS。


示例二：为资源消耗型任务所在memcg配置全局最低水位线分级。

1.  运行命令`mkdir /sys/fs/cgroup/memory/test-be`创建测试文件。

2.  运行命令`echo 25 > /sys/fs/cgroup/memory/test-be/memory.wmark_min_adj`向接口写入值`25`，降低资源消耗型任务的memcg QoS。


