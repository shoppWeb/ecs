# Enable the cgroup writeback feature

Alibaba Cloud Linux 2 supports the cgroup writeback feature for the cgroup v1 kernel interface in the kernel version 4.19.36-12.al7. This feature allows you to limit buffered I/O when you use the cgroup v1 kernel interface.

cgroup refers to control group and consists of v1 and v2. For more information, visit [What are Control Groups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/resource_management_guide/index#sec-What_are_Control_Groups). This topic describes how to enable the cgroup writeback feature for cgroup v1 to limit buffered I/O of processes.

## Limits

After you enable cgroup writeback, check whether the mapping between the memory subsystem \(memcg\) and the I/O subsystem \(blkcg\) conforms to the following rule. If yes, limit buffered I/O of processes.

memcg and blkcg must work together to enable the cgroup writeback feature. Then the cgroup writeback feature limits buffered I/O. However, by default, the control subsystems of the cgroup v1 kernel interface do not work together. Therefore, memcg and blkcg must be associated together through a certain rule. The rule is: each memcg must map a unique blkcg. The mapping between memcg and blkcg can be one-to-one or many-to-one, but can never be one-to-many or many-to-many.

For example, to limit buffered I/O of Processes A and B, you must take note of the following items:

-   If A and B belong to two different memcg subsystems, the two memcg subsystems can each be mapped to different blkcg subsystems. For example, A belongs to `memcg1` and `blkcg1`. B belongs to `memcg2` and `blkcg0`.
-   If A and B belong to two different memcg subsystems, the two memcg subsystems can also be mapped to the same blkcg subsystem. For example, A belongs to `memcg1` and B belongs to `memcg2`. Both A and B can be mapped to `blkcg2`.
-   If A and B belong to the same memcg, the memcg can only be mapped to the same blkcg. For example, assume both A and B belong to `memcg0` and are mapped to `blkcg3`.

After you enable the cgroup writeback feature and before you limit buffered I/O of a process, we recommend that you configure the `cgroup.procs` interface of blkcg by writing a process ID to this interface to avoid exceptions and ensure that the memcg maps to a unique blkcg. You can also use a tool to view the mapping between memcg and blkcg. For more information, see [Verify the mapping between memcg and blkcg](#section_dm0_iub_dvr).

During O&M, a process may move to another cgroup. Based on the preceding rule, if the process moves between two memcg subsystems, no issue occurs. If the process moves between two blkcg subsystems, an exception occurs. To avoid exceptions, the code of the cgroup writeback feature defines the following rule: If a process in a running blkcg moves between two blkcg subsystems, the original memcg maps to the root blkcg. Typically, no throttling threshold is set for the root blkcg. When the original memcg maps to the root blkcg, the throttling does not take effect.

**Note:** Although the kernel code defines the rule to avoid exceptions, we recommend that you prevent processes from moving between two blkcg subsystems.

## Enable cgroup writeback

The cgroup writeback feature in the cgroup v1 interface is disabled by default. To enable this feature, complete the following steps:

1.  Add the `cgwb_v1` field to the `grubby` command to enable the cgroup writeback feature.

    In this example, the kernel version is `4.19.36-12.al7.x86_64`. Enter your actual kernel version during this operation. To query your kernel version, run the `uname -a` command.

    ```
    sudo grubby --update-kernel="/boot/vmlinuz-4.19.36-12.al7.x86_64" --args="cgwb_v1"
    ```

2.  Restart the system to allow the cgroup writeback feature to take effect.

    ```
    sudo reboot
    ```

3.  Run the following command to read the `/proc/cmdline` kernel file. You can see that the command line parameter of the kernel contains the `cgwb_v1` field. This indicates that the `blkio.throttle.write_bps_device` and `blkio.throttle.write_iops_device` interfaces in blkcg can limit buffered I/O.

    ```
    cat /proc/cmdline | grep cgwb_v1
    ```


## Verify the mapping between memcg and blkcg

Before you limit buffered I/O of a process, you can use one of the following methods to check whether the mapping between memcg and blkcg is one-to-one or many-to-one.

-   Run the following command to view the mapping between memcg and blkcg.

    ```
    sudo cat /sys/kernel/debug/bdi/bdi_wb_link
    ```

    The following sample response shows that the mapping between the memcg and blkcg conforms to the one-to-one mapping rule.

    ```
    memory     <--->     blkio
    memcg1:   35 <---> blkcg1:   48
    ```

-   Use the ftrace kernel monitoring tool.
    1.  Enable the ftrace tool.

        ```
        sudo bash -c "echo 1 > /sys/kernel/debug/tracing/events/writeback/insert_memcg_blkcg_link/enable"
        ```

    2.  View the output interface.

        ```
        sudo cat /sys/kernel/debug/tracing/trace_pipe
        ```

        The following sample response contains `memcg_ino=35 blkcg_ino=48`, which indicates that the mapping between the memcg and blkcg conforms to the one-to-one mapping rule.

        ```
        <... >-1537  [006] ....    99.511327: insert_memcg_blkcg_link: memcg_ino=35 blkcg_ino=48 old_blkcg_ino=0
        ```


## Verify whether cgroup writeback is effective

In this example, two processes that generate I/O are simulated to verify whether the cgroup writeback feature is effective.

**Note:**

-   Because the `dd` command is responding quickly and the screen rolls too fast to view, run the `iostat` command to view the result.
-   Because the `dd` command displays response data in sequence, 1 MB of data is generated for sequential I/O refresh. Therefore, you must set the threshold of `blkio.throttle.write_bps_device` to a value no less than 1 MB \(1048576 bytes\). If you set blkio.throttle.write\_bps\_device to a value less than 1 MB, I/O hangs may occur.

1.  Simulate two processes that generate I/O, and firstly set the `cgroup.procs` interface of blkcg based on the preceding limits.

    ```
    sudo mkdir /sys/fs/cgroup/blkio/blkcg1
    sudo mkdir /sys/fs/cgroup/memory/memcg1
    sudo bash -c "echo $$ > /sys/fs/cgroup/blkio/blkcg1/cgroup.procs"    # $$ is your process ID.
    sudo bash -c "echo $$ > /sys/fs/cgroup/memory/memcg1/cgroup.procs"    # $$ is your process ID.
    ```

2.  Use the `blkio.throttle.write_bps_device` interface in blkcg to limit buffered I/O.

    ```
    sudo bash -c "echo 254:48 10485760 > /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.write_bps_device"    # Configure writeback throttling of the disk to 10 MB/s based on the device number.
    ```

3.  Use the `dd` command that does not contain the `oflag=sync` parameter to generate buffered I/O.

    ```
    sudo dd if=/dev/zero of=/mnt/vdd/testfile bs=4k count=10000
    ```

4.  Use the iostat tool to query results. View the `wMB/s` output column. If the value is 10 MB/s, the cgroup writeback feature has taken effect.

    ```
    iostat -xdm 1 vdd
    ```


