# 检测文件系统和块层的IO hang

IO hang是指在系统运行过程中，因某些IO耗时过长而引起的系统不稳定甚至宕机。为了准确检测出IO hang，Alibaba Cloud Linux 2扩展核心数据结构，增加了在较小的系统开销下，快速定位并检测IO hang的功能。本文主要介绍实现该功能的接口以及接口操作示例。

## 接口说明

|接口|描述|
|--|--|
|/sys/block/<device\>/queue/hang\_threshold|该接口能够查看和修改用于检测IO hang的阈值，单位为ms，默认值为5000。|
|/sys/block/<device\>/hang|该接口能够输出对应设备上超过IO hang阈值的读写IO个数。|
|/sys/kernel/debug/block/<device\>/rq\_hang|该接口能够获取IO hang的详细信息。|
|/proc/<pid\>/wait\_res|该接口能够获取进程正在等待的资源信息。|
|/proc/<pid\>/task/<tid\>/wait\_res|该接口能够获取线程正在等待的资源信息。|

以上接口中变量说明如下。

|变量名|说明|
|---|--|
|<device\>|块存储设备名。|
|<pid\>|进程ID。|
|<tid\>|线程ID。|

## 示例一

您可以根据需求调用接口`/sys/block/<device>/queue/hang_threshold`修改用于检测IO hang的阈值。本示例中将默认阈值5000 ms修改为10000 ms。

1.  将磁盘vdb下的用于检测IO hang的阈值修改为10000 ms。

    ```
    echo 10000 > /sys/block/vdb/queue/hang_threshold
    ```

2.  查看修改结果。

    ```
    cat /sys/block/vdb/queue/hang_threshold
    ```

    返回结果示例。

    ```
    10000
    ```


## 示例二

您可以调用接口`/sys/block/<device>/hang`查询磁盘上产生IO hang的读写IO个数。本示例查询的磁盘为vdb。

查询命令如下。

```
cat /sys/block/vdb/hang
```

返回结果示例。

```
0        1     # 左边参数表示产生IO hang的读IO的个数，右边参数表示产生IO hang的写IO的个数
```

## 示例三

您可以调用接口`/sys/kernel/debug/block/<device>/rq_hang`获取产生IO hang的详细信息。本示例中的磁盘为vdb。

查询命令如下。

```
cat /sys/kernel/debug/block/vdb/rq_hang
```

返回结果示例如下。

```
ffff9e50162fc600 {.op=WRITE, .cmd_flags=SYNC, .rq_flags=STARTED|ELVPRIV|IO_STAT|STATS, .state=in_flight, .tag=118, .internal_tag=67, .start_time_ns=1260981417094, .io_start_time_ns=1260981436160, .current_time=1268458297417, .bio = ffff9e4907c31c00, .bio_pages = { ffffc85960686740 }, .bio = ffff9e4907c31500, .bio_pages = { ffffc85960639000 }, .bio = ffff9e4907c30300, .bio_pages = { ffffc85960651700 }, .bio = ffff9e4907c31900, .bio_pages = { ffffc85960608b00 }}
```

上述示例显示了IO的详细信息，从信息中获取到IO请求开始时间`io_start_time_ns`已被赋值。表明该IO请求未被及时处理，从而导致IO耗时过长。

## 示例四

您可以调用接口`/proc/<pid>/wait_res`获取进程正在等待的资源信息。本示例所查询的进程ID为`577`。

查询命令如下。

```
cat /proc/577/wait_res
```

返回结果示例。

```
1 0000000000000000 4310058496 4310061448    #示例值依次对应Field 1 Field 2 Field 3 Field 4
```

返回结果示例中参数说明如下。

|参数|说明|
|--|--|
|Field 1|等待的资源类型。1表示文件系统中的缓存页（page），2表示块层bio。|
|Field 2|等待的资源（page/bio）地址。|
|Field 3|等待资源开始时间。|
|Field 4|读取该文件的当前时间，与Field 3的差值即为在该资源上等待的耗时。|

