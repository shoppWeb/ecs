# 修改TCP TIME-WAIT超时时间

在Linux的内核中，TCP/IP协议的TIME-WAIT状态持续60秒且无法修改。但在某些场景下，例如TCP负载过高时，适当调小该值有助于提升网络性能。因此Alibaba Cloud Linux 2在内核版本4.19.43-13.al7新增内核接口，用于修改TCP TIME-WAIT超时时间。本文主要介绍该接口的使用方法。

TCP/IP协议的TIME-WAIT状态是指应用关闭用于通信的套接口（socket）之后，TCP/IP协议栈保持socket处于打开状态。该状态默认持续60秒，用来保证完成服务器和客户端的数据传输。当处于TIME-WAIT状态的连接数过多时，可能会影响到网络性能。因此Alibaba Cloud Linux 2提供了可修改TIME-WAIT超时时间的接口，用于在特定场景提高网络性能。例如，高并发业务场景。该接口的取值范围为\[1, 600\]，单位为秒。如果不修改该接口，TIME-WAIT超时时间的默认值保持60秒不变。

## 注意事项

将TCP TIME-WAIT超时时间修改为小于60秒与TCP/IP协议quiet time概念相违背，可能导致您的系统将旧数据当做新数据接收，或者复制的新数据当做旧数据拒绝。因此请在网络专家建议下调整。了解TCP/IP协议quiet time的相关概念，请参见[IETF RFC 793标准](https://tools.ietf.org/html/rfc793)。

## 配置方法

您可以通过以下两种方式修改TIME-WAIT超时时间，其中参数`[$TIME_VALUE]`为您修改的TIME-WAIT超时时间。

-   通过`sysctl`命令修改TIME-WAIT超时时间。

    ```
    sysctl -w "net.ipv4.tcp_tw_timeout=[$TIME_VALUE]"
    ```

-   以root权限使用`echo`命令，将值修改到`/proc/sys/net/ipv4/tcp_tw_timeout`接口中。

    ```
    echo [$TIME_VALUE] > /proc/sys/net/ipv4/tcp_tw_timeout
    ```


