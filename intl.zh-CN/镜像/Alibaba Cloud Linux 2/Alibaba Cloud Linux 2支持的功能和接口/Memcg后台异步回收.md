# Memcg后台异步回收

Alibaba Cloud Linux 2在`4.19.81-17.al7`内核版本增加了内存子系统（memcg）后台异步回收功能。本文介绍实现memcg后台异步回收功能的接口。

在社区内核系统中，系统分配内存并在相应memcg中的统计达到memcg设定的内存上限时，会触发memcg级别的直接内存回收。直接内存回收是发生在内存分配上下文的同步回收，因此会影响当前进程的性能。

为了解决这个问题，Alibaba Cloud Linux 2增加了memcg粒度的后台异步回收功能。该功能的实现不同于全局kswapd内核线程的实现，并没有创建对应的memcg kswapd内核线程，而是采用了workqueue机制来实现，并在cgroup v1和cgroup v2两个接口中，均新增了4个memcg控制接口。

注意事项：

-   当前memcg的内存分配，可能会递归触发父组的后台异步回收。
-   触发memcg后台异步回收时，会从当前被触发的memcg开始，自上而下做层级回收。
-   当memory.high接口被配置，并且memory.high的值比memory.limit\_in\_bytes接口的值小的时候，接口memory.wmark\_high和memory.wmark\_low水位线的计算将基于memory.high而不是memory.limit\_in\_bytes。

## memcg后台异步回收功能接口说明

|接口|说明|
|--|--|
|memory.wmark\_ratio|该接口用于设置是否启用memcg后台异步回收功能，以及设置异步回收功能开始工作的memcg内存水位线。单位是相对于memcg limit的百分之几。取值范围：0~100 -   默认值为0，该值也表示禁用memcg后台异步回收功能。
-   取值为非0时，表示开启memcg后台异步回收功能并设置对应的水位线。 |
|memory.wmark\_high|只读接口，说明如下： -   当memcg内存使用超过该接口的值时，后台异步回收功能启动。
-   该接口的值由`(memory.limit_in_bytes * memory.wmark_ratio / 100)`计算获得。
-   memcg后台异步回收功能被禁用时，memory.wmark\_high默认为一个极大值，从而达到永不触发后台异步回收功能的目的。
-   memcg根组目录下不存在该接口文件。 |
|memory.wmark\_low|只读接口，说明如下： -   当memcg内存使用低于该接口的值时，后台异步回收结束。
-   该接口的值由`memory.wmark_high - memory.limit_in_bytes * memory.wmark_scale_factor / 10000`计算得出。
-   memcg根组目录下不存在该接口文件。 |
|memory.wmark\_scale\_factor|该接口用于控制memory.wmark\_high和memory.wmark\_low之间的间隔。单位是相对于memcg limit的万分之几。取值范围：1~1000 -   该接口在创建时，会继承父组的值（该值为50），该值也是默认值，即memcg limit的千分之五。
-   memcg根组目录不存在该接口文件。 |

## 接口配置示例

1.  创建测试文件。

    ```
    mkdir /sys/fs/cgroup/memory/test/
    ```

2.  为内存使用量限制接口memory.limit\_in\_bytes设置值。

    本示例限制为1 G。

    ```
    echo 1G > /sys/fs/cgroup/memory/test/memory.limit_in_bytes
    ```

3.  配置memory.wmark\_ratio接口。

    本示例设置异步回收功能开始工作的memcg内存水位线为memcg limit的百分之九十五。

    ```
    echo 95 > /sys/fs/cgroup/memory/test/memory.wmark_ratio
    ```

4.  运行以下命令查看memory.wmark\_scale\_factor接口的值。

    ```
    cat /sys/fs/cgroup/memory/test/memory.wmark_scale_factor
    ```

    默认值为memcg limit的千分之五。 接口值返回示例：`50`。

5.  此时分别查看memory.wmark\_high和memory.wmark\_low接口的值。

    对应的查看结果为如下所示时，即为配置正确。

    -   运行以下命令：

        ```
        cat /sys/fs/cgroup/memory/test/memory.wmark_high
        ```

        得到的接口值反馈示例：`1020051456`。

    -   运行以下命令：

        ```
        cat /sys/fs/cgroup/memory/test/memory.wmark_low
        ```

        得到的接口值返回示例：`1014685696`。


