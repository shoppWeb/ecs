# Detect I/O hangs of file systems and block layers

An I/O hang occurs when the system becomes unstable or even goes down due to time-consuming I/O requests. To accurately detect I/O hangs, Alibaba Cloud Linux 2 extends the core data structure and provides the function to quickly locate and detect I/O hangs with low system overheads. This topic describes the interfaces for this function and the usage examples of these interfaces.

## Interface description

|Interface|Description|
|---------|-----------|
|/sys/block/<device\>/queue/hang\_threshold|The interface can detect the threshold for I/O hangs. Unit of the threshold: milliseconds. Default value: 5000. The interface allows you to define the threshold for I/O hangs.|
|/sys/block/<device\>/hang|The interface can return the number of I/O operations that exceeds the threshold for I/O hangs on the device.|
|/sys/kernel/debug/block/<device\>/rq\_hang|The interface can query details about I/O hangs.|
|/proc/<pid\>/wait\_res|The interface can query information about the resources that a process is waiting for.|
|/proc/<pid\>/task/<tid\>/wait\_res|The interface can query information about the resources that a thread is waiting for.|

The following table describes variables in the preceding interfaces.

|Variable|Description|
|--------|-----------|
|<device\>|The name of the Block Storage device.|
|<pid\>|The ID of the process.|
|<tid\>|The ID of the thread.|

## Example 1

You can call the `/sys/block/<device>/queue/hang_threshold` interface to change the threshold for I/O hangs. In this example, the threshold is changed from 5,000 ms \(the default value\) to 10,000 ms.

1.  Change the threshold for the vdb disk to 10,000 ms.

    ```
    echo 10000 > /sys/block/vdb/queue/hang_threshold
    ```

2.  View the result of the threshold change.

    ```
    cat /sys/block/vdb/queue/hang_threshold
    ```

    A response similar to the following one is returned:

    ```
    10000
    ```


## Example 2

You can call the `/sys/block/<device>/hang` interface to query the number of I/O operations that have I/O hangs on a disk. In this example, the queried disk is vdb.

The following query command is used:

```
cat /sys/block/vdb/hang
```

A response similar to the following one is returned:

```
0        1     # The value on the left indicates the number of read operations that have I/O hangs. The value on the right indicates the number of write operations that have I/O hangs.
```

## Example 3

You can call the `/sys/kernel/debug/block/<device>/rq_hang` interface to query the details of I/O hangs. In this example, the vdb disk is used.

The following query command is used:

```
cat /sys/kernel/debug/block/vdb/rq_hang
```

A response similar to the following one is returned:

```
ffff9e50162fc600 {.op=WRITE, .cmd_flags=SYNC, .rq_flags=STARTED|ELVPRIV|IO_STAT|STATS, .state=in_flight, .tag=118, .internal_tag=67, .start_time_ns=1260981417094, .io_start_time_ns=1260981436160, .current_time=1268458297417, .bio = ffff9e4907c31c00, .bio_pages = { ffffc85960686740 }, .bio = ffff9e4907c31500, .bio_pages = { ffffc85960639000 }, .bio = ffff9e4907c30300, .bio_pages = { ffffc85960651700 }, .bio = ffff9e4907c31900, .bio_pages = { ffffc85960608b00 }}
```

The preceding response shows the details of an I/O operation. `io_start_time_ns`, which indicates the start time of the I/O request, is assigned a value. This indicates that the I/O request was not processed in time, leading to prolonged I/O consumption.

## Example 4

You can call the `/proc/<pid>/wait_res` interface to query information about the resources that a process is waiting for. In this example, the `577` process is used.

The following query command is used:

```
cat /proc/577/wait_res
```

A response similar to the following one is returned:

```
1 0000000000000000 4310058496 4310061448    # 1 is the value of Field 1, 0000000000000000 the value of Field 2, 4310058496 the value of Field 3, and 4310061448 the value of Field 4.
```

The following table describes the parameters in the sample responses.

|Parameter|Description|
|---------|-----------|
|Field 1|The type of the resource that the process is waiting for. A value of 1 indicates pages in the file system. A value of 2 indicates the block I/O layer.|
|Field 2|The address of the resource \(page or block I/O layer\) that the process is waiting for.|
|Field 3|The time at which the process began waiting for the resource.|
|Field 4|The time when the file is read. The difference between Field 4 and Field 3 is the time consumed while the process waits for the resource.|

