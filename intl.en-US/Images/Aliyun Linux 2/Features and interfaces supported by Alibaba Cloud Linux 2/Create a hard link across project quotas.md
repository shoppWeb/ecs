# Create a hard link across project quotas

By default, the ext4 file system contains constraints. You are not allowed to create hard links across project quotas. However, in practice certain scenarios will require the creation of hard links. Alibaba Cloud Linux 2 provides a custom interface that can bypass the constraints of the ext4 file system to create hard links across project quotas. This topic describes the interface for the function and the sample interface.

Linux distributions support the following disk quota modes: user quota, group quota, and project quota. Compared with user quota and group quota, project quota provides a more fine-grained disk quota. Project quota identifies directories and files within the file system by project ID. This topic describes how to create a hard link across project ID directories in the ext4 file system.

## Interface description

The default value of the /proc/sys/fs/hardlink\_cross\_projid interface is 0. In this case, hard links cannot be created across project quotas. If the /proc/sys/fs/hardlink\_cross\_projid interface is set to 1, you can bypass the constraints of the ext4 file system to create hard links across project quotas.

For more information about the interface, see `Documentation/sysctl/fs.txt`. You can obtain the kernel document from the Debuginfo package and the source code package provided by Alibaba Cloud Linux 2. For more information, see [Use Alibaba Cloud Linux 2](/intl.en-US/Images/Aliyun Linux 2/Overview of Aliyun Linux 2.md).

## Example

You can run the following command to query the value of the /proc/sys/fs/hardlink\_cross\_projid interface:

```
cat /proc/sys/fs/hardlink_cross_projid
```

A value of `0` is returned, indicating that hard links cannot be created across project quotas.

You can change the value from 0 to 1 to create hard link across project quotas.

```
echo 1 > /proc/sys/fs/hardlink_cross_projid
```

