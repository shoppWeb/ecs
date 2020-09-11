# Memcg global minimum watermark rating

This topic describes the memcg global minimum watermark rating feature provided in Alibaba Cloud Linux 2 kernel version `4.19.91-18.al7` or later.

Global memory reclaim has a great impact on system performance of the Linux kernel. When latency-sensitive tasks and resource-consuming tasks are deployed together, resource-consuming tasks request a large amount of memory instantly, causing the free memory of the system to reach the global minimum watermark \(global wmark\_min\). As a result, direct memory reclaim is enabled for all system tasks, causing performance jitters for latency-sensitive tasks. Neither global kswapd backend reclaim nor memcg backend reclaim can resolve this problem.

In this context, Alibaba Cloud Linux 2 provides the memcg global minimum watermark rating feature. The global wmark\_min of resource-consuming tasks is increased to trigger direct memory reclaim. The global wmark\_min of latency-sensitive tasks is decreased to avoid direct memory reclaim. In this way, when a resource-consuming task requests a large amount of memory instantly, an increase in the global wmark\_min throttles the resources used for the task for a short period to avoid direct memory reclaim for latency-sensitive tasks. After a certain amount of memory is reclaimed through global kswapd backend reclaim, the throttling of the resource-consuming task is stopped.

## Interface files

The interface file that implements the memcg global minimum watermark rating feature is memory.wmark\_min\_adj. The value of this interface file indicates a percent of adjustment over the global minimum watermark \(global wmark\_min\). Valid values: -25 to 50.

-   This interface file inherits a value of 0 from its parent group when the interface file is created. The default value is 0.
-   A negative value is a percent of adjustment over the range `[0, WMARK_MIN]`, where `WMARK_MIN` is the value of global wmark\_min. Example:

    ```
    memory.wmark_min_adj=-25, memcg WMARK_MIN is "WMARK_MIN + (WMARK_MIN - 0) * (-25%)"
    ```

    **Note:** A negative value also indicates a decrease of global wmark\_min to increase the memcg QoS of latency-sensitive tasks.

-   A positive value is a percent of adjustment over the range `[WMARK_MIN, WMARK_LOW]`, where `WMARK_MIN` and `WMARK_LOW` are values of global wmark\_min and global wmark\_low. Example:

    ```
    memory.wmark_min_adj=50, memcg WMARK_MIN is "WMARK_MIN + (WMARK_LOW - WMARK_MIN) * 50%"
    ```

    **Note:** A positive value also indicates an increase of global wmark\_min to decrease the memcg QoS of resource-consuming tasks.

-   When the offset global wmark\_min is triggered, throttling is performed, and the throttling time is linearly proportional to the excessive memory usage. Valid values of throttling time: 1 to 1000. Unit: ms.

**Note:** This interface file is not stored in the memcg root directory.

## Precautions

A multi-level memcg includes `effective memory.wmark_min_adj`, which is the final effective value of memory.wmark\_min\_adj. The values of memory.wmark\_min\_adj at all levels are traversed to obtain the maximum value. Intermediate nodes with the default value 0 are excluded. The following hierarchy shows an example.

```
         root
         / \
        A   D
       / \
      B   C
     / \
    E   F
```

The following table describes the mapping between the values of memory.wmark\_min\_adj at each level and the final effective value.

|Level|Value|Final effective value|
|-----|-----|---------------------|
|A|-10|-10|
|B|-25|-10|
|C|0|0|
|D|50|50|
|E|-25|-10|
|F|50|50|

**Note:**

-   The value displayed in the output of the cat /sys/fs/cgroup/memory/<memcg path\>/memory.wmark\_min\_adj command is the final effective value. In the command, `<memcg path>` indicates the root path of the memcg.
-   We recommend that you use the global minimum watermark rating feature together with global wmark\_min. For example, you can set global wmark\_min to 2 GB or more in /proc/sys/vm/min\_free\_kbytes.

## Configuration examples

Example 1: Configure global minimum watermark rating for the memcg of latency-sensitive tasks.

1.  Run the mkdir /sys/fs/cgroup/memory/test-lc command to create a test file.

2.  Run the echo -25 \> /sys/fs/cgroup/memory/test-lc/memory.wmark\_min\_adj command to write the value `-25` to the interface file to increase the memcg QoS of latency-sensitive tasks.


Example 2: Configure global minimum watermark rating for the memcg of resource-consuming tasks.

1.  Run the mkdir /sys/fs/cgroup/memory/test-be command to create a test file.

2.  Run the echo 25 \> /sys/fs/cgroup/memory/test-be/memory.wmark\_min\_adj command to write the value `25` to the interface file to decrease the memcg QoS of resource-consuming tasks.


