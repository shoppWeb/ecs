# Enable the PSI feature for the cgroup v1 interface

In the Linux kernel, only the cgroup v2 interface supports the Pressure Stall Information \(PSI\) feature. Alibaba Cloud Linux 2 supports the PSI feature for the cgroup v1 interface in the kernel version 4.19.81-17.al7 to allow you to monitor the CPU, memory, and I/O performance. This topic describes how to enable the PSI feature in the cgroup v1 interface and query relevant information.

PSI is a kernel feature that can be used to monitor the CPU, memory, and I/O performance. For more information about the PSI feature, see the kernel document `Documentation/accounting/psi.txt`. The kernel document is contained in the Debuginfo package and source code package of Alibaba Cloud Linux 2. For information about how to download the Debuginfo package and source code package, see [Use Alibaba Cloud Linux 2](/intl.en-US/Images/Aliyun Linux 2/Overview of Aliyun Linux 2.md).

## Enable the PSI feature for the cgroup v1 interface

By default, the PSI feature of the cgroup v1 interface is disabled. You can complete the following steps to enable the PSI feature:

1.  Run the `grubby` command to change the startup parameter.

    The default value of the `args` parameter is `"psi=1"`, which indicates that the PSI feature has been enabled for cgroup v2. Change the value of the parameter to `"psi=1 psi_v1=1"`, which indicates that the PSI feature is enabled for cgroup v1 in Alibaba Cloud Linux 2. In this example, the kernel version is `4.19.81-17.al7.x86_64`. You must use your actual kernel version during the operation. To query the kernel version, run the `uname -a` command.

    ```
    sudo grubby --update-kernel="/boot/vmlinuz-4.19.81-17.al7.x86_64" --args="psi=1 psi_v1=1"
    ```

2.  Restart the system to apply the change.

    ```
    sudo reboot
    ```


## Verify that the PSI feature has been enabled for the cgroup v1 interface

After the system restarts, you can run the following command to verify that the PSI feature has been enabled for the cgroup v1 interface in `/proc/cmdline` of the kernel.

```
cat /proc/cmdline | grep "psi=1 psi_v1=1"
```

## Query the monitoring data of the CPU, memory, and I/O performance

When you enable the PSI feature for the cgroup v1 interface, the PSI monitoring data of the CPU, memory, and I/O performance are all transferred to the cpuacct controller. You can query detailed monitoring data by running the following commands:

```
cat /sys/fs/cgroup/cpuacct/cpu.pressure
cat /sys/fs/cgroup/cpuacct/memory.pressure
cat /sys/fs/cgroup/cpuacct/io.pressure
```

