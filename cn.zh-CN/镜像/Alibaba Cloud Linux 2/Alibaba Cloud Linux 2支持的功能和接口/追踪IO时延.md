# 追踪IO时延

Alibaba Cloud Linux 2优化了IO时延分析工具iostat的原始数据来源`/proc/diskstats`接口，增加了对设备侧的读、写及特殊IO（discard）等耗时的统计，此外还提供了一个方便追踪IO时延的工具bcc。本文将分别介绍优化后的`/proc/diskstats`接口以及bcc工具。

## 接口说明

/proc/diskstats接口在Alibaba Cloud Linux 2中可查询磁盘IO信息、设备侧的读耗时、设备侧的写耗时及设备侧discard耗时。

示例：以root权限查询/proc/diskstats接口。

```
cat /proc/diskstats
```

返回结果示例如下。

```
254       0 vda 6328 3156 565378 2223 1610 424 25160 4366 0 1358 5332 0 0 0 0 2205 3347 0
```

返回结果中，最后三个域为Alibaba Cloud Linux 2新增域，域说明如下。

|域|描述|
|--|--|
|第16个域|设备侧的读耗时，单位为ms。|
|第17个域|设备侧的写耗时，单位为ms。|
|第18个域|设备侧的discard耗时，单位为ms。|

**说明：** 其他域的说明，您可以参考内核文档`Documentation/iostats.txt`中对该接口的相关说明。内核文档通过Alibaba Cloud Linux 2的Debuginfo包和源码包获取，如何获取请参见[使用Alibaba Cloud Linux 2](/cn.zh-CN/镜像/Alibaba Cloud Linux 2/Aliyun Linux 2概述.md)。

## bcc工具

Alibaba Cloud Linux 2提供一个方便用户追踪IO时延的工具bcc。您需要先下载该工具才能使用，下载命令如下。

```
yum install -y bcc-tools
```

您可以通过以下两种命令查看bcc工具的说明。

-   通过以下命令获取bcc工具说明。

    ```
    /usr/share/bcc/tools/alibiolatency -h
    ```

    说明展示。

    ```
    usage: alibiolatency [-h] [-d DEVICE] [-i [DIS_INTERVAL]]
                         [-t [AVG_THRESHOLD_TIME]] [-T [THRESHOLD_TIME]] [-r]
    
    Summarize block device I/O latency
    
    optional arguments:
      -h, --help            show this help message and exit
      -d DEVICE, --device DEVICE
                            inspect specified device
      -i [DIS_INTERVAL], --dis_interval [DIS_INTERVAL]
                            specify display interval
      -t [AVG_THRESHOLD_TIME], --avg_threshold_time [AVG_THRESHOLD_TIME]
                            display only when average request process time is
                            greater than this value
      -T [THRESHOLD_TIME], --threshold_time [THRESHOLD_TIME]
                            dump request life cycle when single request process
                            time is greater than this value
      -r, --dump_raw        dump every io request life cycle
    
    examples:
        ./alibiolatency          # summarize block I/O latency(default display interval is 2s)
        ./alibiolatency -d sda3  # inspect specified device /dev/sda3
        ./alibiolatency -i 2     # specify display interval, 2s
        ./alibiolatency -t 10    # display only when average request process time is greater than 10ms
        ./alibiolatency -T 20    # dump request life cycle when single request process time is greater than 20ms
        ./alibiolatency -r       # dump every io request life cycle
    ```

-   通过`man`命令获取bcc工具说明。

    ```
    man bcc-alibiolatency
    ```


