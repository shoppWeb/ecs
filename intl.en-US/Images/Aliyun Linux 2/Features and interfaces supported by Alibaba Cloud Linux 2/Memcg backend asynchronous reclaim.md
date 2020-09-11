# Memcg backend asynchronous reclaim

The memory control group \(memcg\) backend asynchronous reclaim feature is provided in Alibaba Cloud Linux 2 kernel version `4.19.81-17.al7` or later. This topic describes the interface files that implement the memcg backend asynchronous reclaim feature.

In community versions of the Linux kernel, the system allocates memory and triggers memcg-level direct memory reclaim when the memory reaches a limit set by a memcg. Direct memory reclaim is synchronous reclaim that occurs in the context of memory allocation and affects performance of the current process.

To resolve this problem, Alibaba Cloud Linux 2 provides the backend asynchronous reclaim feature for memcgs. This feature differs from the global kswapd kernel thread in that it uses the workqueue mechanism instead of a corresponding memcg kswapd kernel thread. Additionally, four memcg interface files are added in each of the cgroup v1 and cgroup v2 interfaces.

Precautions

-   Memory allocation of the current memcg may recursively trigger the backend asynchronous reclaim of the parent group.
-   When triggered, backend asynchronous reclaim starts from the memcg on which the feature is triggered and is performed in sequence down the hierarchy.
-   When memory.high is configured and the value of memory.high is smaller than that of memory.limit\_in\_bytes, the values of memory.wmark\_high and memory.wmark\_low are calculated based on memory.high instead of memory.limit\_in\_bytes.

## Interface files

|Interface file|Description|
|--------------|-----------|
|memory.wmark\_ratio|Specifies whether to enable the memcg backend asynchronous reclaim feature and sets the memcg memory watermark where asynchronous reclaim starts. Unit: percent of the memcg memory limit. Valid values: 0 to 100. -   The default value is 0, which indicates that the memcg backend asynchronous reclaim feature is disabled.
-   When the value is not 0, the memcg backend asynchronous reclaim feature is enabled and the corresponding watermark is set. |
|memory.wmark\_high|A read-only interface file. -   When the memcg memory usage exceeds the value of this interface file, backend asynchronous reclaim is started.
-   The value of this interface file is calculated based on the following formula: `(memory.limit_in_bytes × memory.wmark_ratio/100)`.
-   When the memcg backend asynchronous reclaim feature is disabled, the default value of memory.wmark\_high is the maximum value and the backend asynchronous reclaim feature is never triggered.
-   This interface file is not stored in the memcg root directory. |
|memory.wmark\_low|A read-only interface file. -   When the memcg memory usage is less than the value of this interface file, backend asynchronous reclaim is ended.
-   The value of this interface file is calculated based on the following formula: `memory.wmark_high - memory.limit_in_bytes × memory.wmark_scale_factor/10000`.
-   This interface file is not stored in the memcg root directory. |
|memory.wmark\_scale\_factor|Controls the gap between memory.wmark\_high and memory.wmark\_low. Unit: 0.01% of the memcg memory limit. Valid values: 1 to 1000. -   This interface file inherits the value of its parent group when the interface file is created. The value of this interface file is 50, which indicates 0.50% of the memcg memory limit. This is the default value.
-   This interface file is not stored in the memcg root directory. |

## Configuration examples

1.  Create a test file.

    ```
    mkdir /sys/fs/cgroup/memory/test/
    ```

2.  Specify the value of memory.limit\_in\_bytes.

    In this example, the value is 1 GiB.

    ```
    echo 1G > /sys/fs/cgroup/memory/test/memory.limit_in_bytes
    ```

3.  Configure memory.wmark\_ratio.

    In this example, the memcg memory watermark where asynchronous reclaim starts is set to 95% of the memcg memory limit.

    ```
    echo 95 > /sys/fs/cgroup/memory/test/memory.wmark_ratio
    ```

4.  Run the cat /sys/fs/cgroup/memory/test/memory.wmark\_scale\_factor command to query the value of memory.wmark\_scale\_factor.

    The default value is 0.50% of the memcg memory limit.

    ```
    50
    ```

5.  Query the values of memory.wmark\_high and memory.wmark\_low.

    If the following values are displayed for memory.wmark\_high and memory.wmark\_low in the command outputs, the configurations are correct.

    -   If the following value is displayed for memory.wmark\_high in the cat /sys/fs/cgroup/memory/test/memory.wmark\_high command output:

        ```
        1020051456
        ```

    -   If the following value is displayed for memory.wmark\_low in the cat /sys/fs/cgroup/memory/test/memory.wmark\_low command output:

        ```
        1014685696
        ```


