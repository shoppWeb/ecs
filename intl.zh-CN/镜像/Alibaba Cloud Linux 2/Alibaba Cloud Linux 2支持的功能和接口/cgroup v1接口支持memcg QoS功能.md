# cgroup v1接口支持memcg QoS功能

内存子系统服务质量（memcg QoS）可以用来控制内存子系统（memcg）的内存使用量的保证（锁定）与限制，在社区版内核中只有`cgroup v2`接口支持该功能。Alibaba Cloud Linux 2在`4.19.91-18.al7`内核版本，新增`cgroup v1`接口支持memcg QoS的相关功能。

## 背景信息

在Alibaba Cloud Linux 2内核中，`cgroup v1`接口中默认开启`memcg QoS`功能。关于`memcg QoS`的更多信息，您可以参见内核文档`Documentation/admin-guide/cgroup-v2.rst`。内核文档通过Alibaba Cloud Linux 2的Debuginfo包和源码包获取，如何获取请参见[使用Alibaba Cloud Linux 2](/intl.zh-CN/镜像/Alibaba Cloud Linux 2/Aliyun Linux 2概述.md)。

## 注意事项

使用`cgroup v1`接口的memcg QoS功能时，建议将任务放置在memcg的叶子节点中，例如，/sys/fs/cgroup/memory/<intermediate node\>/<leaf node\>/tasks路径下。

## 接口说明

本节介绍Alibaba Cloud Linux 2内核中`cgroup v1`接口实现memcg QoS功能的相关接口。

|接口|描述|
|--|--|
|memory.min|绝对锁定内存，即使系统没有可回收的内存，也不会回收该接口锁定的内存。读写说明如下： -   读该接口可以查看memcg锁定的内存大小。
-   写该接口可以设置memcg锁定的内存大小。 |
|memory.low|相对锁定内存，如果系统没有其他可回收的内存，该接口锁定的内存也会被回收一部分。读写说明如下： -   读该接口可以查看memcg锁定的内存大小。
-   写该接口可以设置memcg锁定的内存大小。 |
|memory.high|限制memcg的内存使用，读写说明如下： -   读该接口可以查看memcg的使用限制。
-   写该接口可以设置memcg的使用限制。 |

## 接口示例

在memcg的挂载目录下，例如/sys/fs/cgroup/memory/路径下，创建测试memcg，确认该memcg内包含memory.minmemory.lowmemory.high三个接口。

示例命令如下。

```
mkdir /sys/fs/cgroup/memory/test
ls /sys/fs/cgroup/memory/test | grep -E "memory.(min|low|high)"
```

返回结果示例。

```
memory.high
memory.low
memory.min
```

