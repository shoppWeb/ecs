# Known issues about Alibaba Cloud Linux 2

This topic describes known issues of Alibaba Cloud Linux 2 images, the scope of these issues, and their corresponding solutions.

## Performance issues may occur after you enable the CONFIG\_PARAVIRT\_SPINLOCK kernel feature

-   Problem description: After you enable the CONFIG\_PARAVIRT\_SPINLOCK kernel feature, application performance is significantly affected when an ECS instance has a large number of vCPUs and a large number of lock contentions exist in applications. For example, short-lived connections deteriorate the performance of a NGINX application.
-   Solution: We recommend that you disable the CONFIG\_PARAVIRT\_SPINLOCK kernel feature for Alibaba Cloud Linux 2 \(disabled by default\). And if you are not sure how to resolve the kernel problem, do not enable the CONFIG\_PARAVIRT\_SPINLOCK feature.

## System instability and performance issues may occur after you set the THP switch of kernel features to always

-   Problem description: After you set the Transparent Hugepage \(THP\) switch in your production environment to always, the system becomes unstable and its performance is deteriorated.
-   Solution: For information about how to optimize THP-related performance, see [Transparent huge page THP-related performance optimization in Alibaba Cloud Linux 2](https://www.alibabacloud.com/help/doc-detail/161963.htm).

## A delegation conflict occurs in NFS v4.0

-   Problem description: A delegation conflict occurs in NFS v4.0. For more information, see [Delegation in NFS Version 4](https://docs.oracle.com/cd/E19253-01/816-4555/rfsrefer-140/index.html).
-   Solution: We recommend that you disable the Delegation feature when you use NFS v4.0. For information about how to disable this feature at the server side, visit [How to Select Different Versions of NFS on a Server](https://docs.oracle.com/cd/E19253-01/816-4555/rfsadmin-965/index.html).

## Defects in NFS v4.1 or v4.2 cause failures to exit applications

-   Problem description: In NFS v4.1 or v4.2, if you use Asynchronous I/O \(AIO\) in applications to distribute requests and close the corresponding file descriptors before all I/O operations are returned, a livelock may be triggered and the corresponding process cannot be ended.
-   Solution: This problem was fixed in kernel versions 4.19.30-10.al7 and later. Application exit failure is not likely to occur. Decide whether you need to upgrade the kernel to fix this issue. To upgrade the kernel version, run the sudo yum update kernel -y command.

    **Note:**

    -   The kernel upgrade may result in system boot failure. Exercise caution when you perform this action.
    -   Before you upgrade the kernel, make sure that you have created a snapshot or a custom image to back up data. For more information, see [Create a normal snapshot](/intl.en-US/Snapshots/Use snapshots/Create a normal snapshot.md) or [Create a custom image from an instance](/intl.en-US/Images/Custom image/Create custom image/Create a custom image from an instance.md).

## System performance is affected after security vulnerabilities such as Meltdown and Spectre are fixed

-   Problem description: By default, the repair of important security vulnerabilities such as Meltdown or Spectre in processors is enabled in the kernel of Alibaba Cloud Linux 2. This affects system performance. Therefore, performance may be deteriorated during performance benchmark testing.
-   Solution: Meltdown and Spectre are two important vulnerabilities in Intel chips. These vulnerabilities allow attackers to steal sensitive application data from the system memory. We recommend that you enable the repair feature. However, if you want to maximize system performance, you can disable the repair feature. For more information, see [How to fix CPU vulnerabilities in the Alibaba Cloud Linux 2 system](https://www.alibabacloud.com/help/doc-detail/154567.htm).

