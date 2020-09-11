# Memcg QoS feature of the cgroup v1 interface

The memory control group \(memcg\) quality of service \(QoS\) can be used to control locks and limits on memory usage in a memcg. In community versions of the Linux kernel, this feature is supported only in the `cgroup v2` interface, whereas in Alibaba Cloud Linux 2 kernel version `4.19.91-18.al7` or later, this feature is also supported in the `cgroup v1` interface.

## Background information

In the Alibaba Cloud Linux 2 kernel, the `memcg QoS` feature is enabled in the `cgroup v1` interface by default. For more information about `memcg QoS`, visit `Documentation/admin-guide/cgroup-v2.rst`. You can obtain the kernel document from the Debuginfo package and the source code package of Alibaba Cloud Linux 2. For more information, see [Use Alibaba Cloud Linux 2](/intl.en-US/Images/Aliyun Linux 2/Overview of Aliyun Linux 2.md).

## Precautions

When you use the memcg QoS feature of the `cgroup v1` interface, we recommend that you place your tasks in a memcg leaf node such as /sys/fs/cgroup/memory/<intermediate node\>/<leaf node\>/tasks.

## Interface files

This section describes the interface files that implement the memcg QoS feature of the `cgroup v1` interface in the Alibaba Cloud Linux 2 kernel.

|Interface file|Description|
|--------------|-----------|
|memory.min|Absolutely locks the memory. Memory locked by this interface file is not reclaimed even if the system has no memory to reclaim. You can perform the following read and write operations on this file: -   Read the size of locked memory from this interface file.
-   Write the size of locked memory to this interface file. |
|memory.low|Relatively locks the memory. Memory locked by this interface file may be partially reclaimed if the system has no other memory to reclaim. You can perform the following read and write operations on this file: -   Read the size of locked memory from this interface file.
-   Write the size of locked memory to this interface file. |
|memory.high|Limits the memory usage. You can perform the following read and write operations on this file: -   Read the usage limits on the memcg from this interface file.
-   Write the usage limits on the memcg to this interface file. |

## Examples

Create a test memcg at the memcg mount point such as /sys/fs/cgroup/memory/, and ensure that the memcg includes the memory.min, memory.low, and memory.high interface files.

The following sample commands are provided for your reference:

```
mkdir /sys/fs/cgroup/memory/test
ls /sys/fs/cgroup/memory/test | grep -E "memory.(min|low|high)"
```

Sample command outputs:

```
memory.high
memory.low
memory.min
```

