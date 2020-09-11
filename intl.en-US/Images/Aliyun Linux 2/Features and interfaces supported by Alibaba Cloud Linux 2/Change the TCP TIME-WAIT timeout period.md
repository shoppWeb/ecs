# Change the TCP TIME-WAIT timeout period

In the Linux kernel, TCP/IP connections stay in the TIME-WAIT state for 60 seconds, and this period cannot be changed. However, in some scenarios such as heavy TCP loads, network performance can be improved if this period can be reduced. In this context, Alibaba Cloud Linux 2 provides a kernel interface in the 4.19.43-13.al7 kernel version to change the TCP TIME-WAIT timeout period. This topic describes how to use the kernel interface.

The TIME-WAIT state is a mechanism in TCP/IP stacks that keeps sockets open after an application has shut down the sockets. By default, this state lasts for 60 seconds to ensure complete data transmission between the server and the client. If a large number of connections are in the TIME-WAIT state, network performance may be compromised. Therefore, Alibaba Cloud Linux 2 provides an interface to change the TIME-WAIT timeout period to improve network performance in high-concurrency scenarios. The value range of this interface is 1 to 600 seconds. The default value of the TIME-WAIT timeout period is 60 seconds.

## Precautions

A timeout period of less than 60 seconds may violate the TCP/IP quiet time restriction and may cause some old data to be accepted as new or duplicated new data rejected as old. Therefore, we recommend that you adjust the TIME-WAIT timeout period on the advice of Alibaba Cloud technicians. For more information about the TCP/IP quiet time concept, visit [IETF RFC 793](https://tools.ietf.org/html/rfc793).

## Configuration methods

You can use one of the following methods to change the TIME-WAIT timeout period. In both methods, the `[$TIME_VALUE]` parameter specifies the new timeout period that you want to set.

-   Run the `sysctl` command to change the TIME-WAIT timeout period.

    ```
    sysctl -w "net.ipv4.tcp_tw_timeout=[$TIME_VALUE]"
    ```

-   Run the `echo` command as the root user and change the TIME-WAIT timeout period in the `/proc/sys/net/ipv4/tcp_tw_timeout` interface.

    ```
    echo [$TIME_VALUE] > /proc/sys/net/ipv4/tcp_tw_timeout
    ```


