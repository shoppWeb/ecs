# Block IO限流增强监控接口

为了更方便地监控Linux block IO限流，Alibaba Cloud Linux 2在内核版本4.19.81-17.al7增加相关接口，用于增强block IO限流的监控统计能力。本文主要介绍新增接口及使用示例。

很多场景中您会用到Linux block IO限流（bps/iops），特别是在支持控制群组回写（cgroup writeback）后，IO限流使用场景更加广泛。提高block IO限流的监控能力能使您更方便进行IO限流相关操作，因此Alibaba Cloud Linux 2新增了Block IO限流增强监控接口。

## 接口说明

|接口|描述|
|--|--|
|blkio.throttle.io\_service\_time|该接口表示从block IO限流层开始下发到IO完成的耗时。单位：ns|
|blkio.throttle.io\_wait\_time|该接口表示在block IO限流层被限流的耗时。单位：ns|
|blkio.throttle.io\_completed|该接口表示已完成的IO个数，用于计算block IO限流层的平均时延。单位：个|
|blkio.throttle.total\_io\_queued|该接口表示历史发生限流的IO总个数，通过周期性的监控可以计算出当前周期发生限流的IO个数，从而辅助分析IO时延是否与限流有关。单位：个|
|blkio.throttle.total\_bytes\_queued|该接口表示历史发生限流的IO总字节数，同blkio.throttle.total\_io\_queued，只是以IO大小的形式展现。单位：字节|

以上接口的路径为/sys/fs/cgroup/blkio/<cgroup\>/，其中`<cgroup>`为控制群组。

## 示例

您可以通过增强block IO限流的监控统计能力的接口获取某个磁盘上的平均IO时延。本示例中通过监控磁盘vdd两个时间点的平均写IO时延，时间间隔为5 s，进而统计出磁盘vdd的平均IO时延。示例参数说明如下。

|参数|说明|
|--|--|
|write\_wait\_time<N\>|获取在block IO限流层被限流的耗时。|
|write\_service\_time<N\>|获取从block IO限流层开始下发到IO完成的耗时。|
|write\_completed<N\>|获取已完成的IO个数。|

1.  在T1时刻获取监控数据。

    ```
    write_wait_time1 = `cat /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.io_wait_time | grep -w "254:48 Write" | awk '{print $3}'`
    write_service_time1 = `cat /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.io_service_time | grep -w "254:48 Write" | awk '{print $3}'`
    write_completed1 = `cat /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.io_completed | grep -w "254:48 Write" | awk '{print $3}'`
    ```

2.  等待5 s后，在T2时刻获取监控数据。

    ```
    write_wait_time2 = `cat /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.io_wait_time | grep -w "254:48 Write" | awk '{print $3}'`
    write_service_time2 = `cat /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.io_service_time | grep -w "254:48 Write" | awk '{print $3}'`
    write_completed2 = `cat /sys/fs/cgroup/blkio/blkcg1/blkio.throttle.io_completed | grep -w "254:48 Write" | awk '{print $3}'`
    ```

3.  统计5 s内的平均IO时延。

    平均IO时延的计算规则：（T2时刻的总IO耗时 - T1时刻的总IO耗时）/（T2时刻已完成的IO个数 - T1时刻已完成的IO个数）。

    ```
    avg_delay = `echo "((write_wait_time2 + write_service_time2) - (write_wait_time1+write_service_time1)) / (write_completed2 - write_completed1)" | bc`
    ```


