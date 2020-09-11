# 启用cgroup writeback功能

Alibaba Cloud Linux 2在内核版本4.19.36-12.al7中，对内核接口cgroup v1新增了控制群组回写（cgroup writeback）功能。该功能使您在使用内核接口cgroup v1时，可以对缓存异步I/O \(Buffered I/O\) 进行限速。

控制群组（control group）简称为cgroup，分为v1和v2两个版本，详情请参见[什么是控制群组](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html-single/resource_management_guide/index#sec-What_are_Control_Groups)。本文介绍如何启用cgroup v1的cgroup writeback功能，并对进程进行Buffered I/O限速。

## 使用限制

在启用cgroup writeback功能之后，您可以先确认内存子系统（memcg）和IO子系统（blkcg）的映射关系是否符合下文所述的规则，再对进程进行Buffered I/O限速。

cgroup writeback功能需要memcg和blkcg协同工作，完成Buffered I/O的限速，但是内核接口cgroup v1的各个控制子系统间默认不协同工作。因此需要通过一定的规则把memcg和blkcg连接起来，规则为：通过任意一个memcg必须可以找到与之唯一对应的blkcg。即memcg和blkcg的映射关系可以是一对一或多对一，不可以是一对多或多对多。

例如，存在进程A和B，对它们进行Buffered I/O限速，需要遵循以下约束。

-   如果A和B分属不同的memcg，它们可以映射到不同的blkcg，只需各自一一对应。例如：A属于`memcg1`，`blkcg1`；B属于`memcg2`，`blkcg0`。
-   如果A和B分属不同的memcg，它们也可以映射到同一个blkcg。例如：A属于`memcg1`，B属于`memcg2`，A和B都属于`blkcg2`。
-   如果A和B属于相同的memcg，那么它们只能映射到同一个blkcg。例如：A和B均属于`memcg0`，它们同时属于`blkcg3`。

为了避免出现意外情况，建议您在启用cgroup writeback功能后，对进程进行Buffered I/O限速前，优先设置blkcg的`cgroup.procs`接口，向该接口写入一个进程ID来保证blkcg映射的唯一性。同时您也可以通过工具查看memcg和blkcg的映射关系，详情请参见[确认memcg和blkcg的映射关系](#section_dm0_iub_dvr)。

在实际运维中，可能出现进程移动到其它cgroup的情况。根据上述规则，如果进程在memcg之间移动，不会出现问题，但如果进程在blkcg之间移动，将会出现异常情况。为了避免产生异常，该功能的代码中定义了规则：一旦工作中的blkcg内的进程发生blkcg间的移动，则将映射关系直接指向root blkcg。由于一般情况是不在root blkcg设置限流阈值，所以当映射关系直接指向root blkcg时，限速功能会失效。

**说明：** 内核代码虽定义了规则避免出现意外，但您需要在实际操作中尽量避免将进程在blkcg间移动。

## 开启cgroup writeback功能

cgroup v1接口中的cgroup writeback功能默认是关闭的，按照以下步骤开启该功能。

1.  通过命令`grubby`内添加`cgwb_v1`字段开启该功能。

    本示例中内核版本为`4.19.36-12.al7.x86_64`，您在操作中需要更换为实际的内核版本，内核版本的查看命令为`uname -a`。

    ```
    sudo grubby --update-kernel="/boot/vmlinuz-4.19.36-12.al7.x86_64" --args="cgwb_v1"
    ```

2.  重启系统使功能生效。

    ```
    sudo reboot
    ```

3.  使用以下命令读取内核文件`/proc/cmdline`，确认内核命令行参数中带有`cgwb_v1`字段。此时，blkcg下的`blkio.throttle.write_bps_device`及`blkio.throttle.write_iops_device`接口能够对Buffered I/O进行限速。

    ```
    cat /proc/cmdline | grep cgwb_v1
    ```


## 确认memcg和blkcg的映射关系

当您对进程进行Buffered I/O限速之前，您可以使用以下任意一种方式诊断memcg和blkcg的映射关系是否为一对一或多对一。

-   查看memcg与blkcg映射关系。

    ```
    sudo cat /sys/kernel/debug/bdi/bdi_wb_link
    ```

    返回结果示例如下，该示例表示memcg和blkcg符合一对一的映射规则。

    ```
    memory     <--->     blkio
    memcg1:   35 <---> blkcg1:   48
    ```

-   使用ftrace内核监测工具。
    1.  开启ftrace工具。

        ```
        sudo bash -c "echo 1 > /sys/kernel/debug/tracing/events/writeback/insert_memcg_blkcg_link/enable"
        ```

    2.  查看信息输出接口。

        ```
        sudo cat /sys/kernel/debug/tracing/trace_pipe
        ```

        输出内容示例如下，其中`memcg_ino=35 blkcg_ino=48`表示memcg和blkcg符合一对一的映射规则。

        ```
        <...>-1537  [006] ....    99.511327: insert_memcg_blkcg_link: memcg_ino=35 blkcg_ino=48 old_blkcg_ino=0
        ```


## 验证cgroup writeback是否生效

本示例将模拟出两个产生I/O的进程，用于验证cgroup writeback功能是否有效。

**说明：**

-   由于`dd`命令的反馈速度较快，结果使用`iostat`命令查看。
-   由于`dd`命令为顺序写入，顺序IO回刷时，会生成1 MB数据再回刷，因此设置阈值时，`blkio.throttle.write_bps_device`不得小于1 MB\(1048576\)。如果设置值小于1 MB，可能会引发IO hang。

1.  模拟出两个产生I/O的进程，并按照限制条件优先设置blkcg的`cgroup.procs`接口。

    ```
    sudo mkdir /sys/fs/cgroup/blkio/blkcg1
    sudo mkdir /sys/fs/cgroup/memory/memcg1
    sudo bash -c "echo $$ > /sys/fs/cgroup/blkio/blkcg1/cgroup.procs"    # $$为您的进程ID
    sudo bash -c "echo $$ > /sys/fs/cgroup/memory/memcg1/cgroup.procs"    # $$为您的进程ID
    ```

2.  使用blkcg下的`blkio.throttle.write_bps_device`接口对Buffered I/O进行限速。

    ```
    sudo bash -c "echo 254:48 10485760 > /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.write_bps_device"    # 通过设备号配置磁盘的回写限流bps为10 M
    ```

3.  使用不带参数`oflag=sync`的`dd`命令产生缓存异步I/O。

    ```
    sudo dd if=/dev/zero of=/mnt/vdd/testfile bs=4k count=10000
    ```

4.  使用iostat工具查询结果。查看输出列`wMB/s`，如果被限制到10 MB/s，则表示cgroup writeback功能已生效。

    ```
    iostat -xdm 1 vdd
    ```


