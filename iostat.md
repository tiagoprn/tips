$ iostat -c -d -t -m /dev/md1 2 100

Takes a snapshot of the status of the device /dev/md1. Capture the data every two seconds (this is the 2 after the device) and do it 100 times (this is the 100 at the end of the command). This means I captured 200 seconds of data in two-second intervals. The -c option displays the CPU report, -d displays the device utilization report, -t prints the time for each report displayed (good for getting a time history), and -m displays the statistics in megabytes per second (MB/s in the output).

With the "-x" parameter you can display extended statistics:

    Units       Definition
    rrqm/s      The number of read requests merged per second queued to the device.
    wrqm/s      The number of write requests merged per second queued to the device.
    r/s         The number of read requests issued to the device per second.
    w/s         The number of write requests issued to the device per second.
    rMB/s       The number of megabytes read from the device per second. (I chose to used MB/s for the output.)
    wMB/s       The number of megabytes written to the device per second. (I chose to use MB/s for the output.)
    avgrq-sz    The average size (in sectors) of the requests issued to the device.
    avgqu-sz    The average queue length of the requests issued to the device.
    await       The average time (milliseconds) for I/O requests issued to the device to be served. This includes the time spent by the requests in queue and the time spent servicing them.
    r_await     The average time (in milliseconds) for read requests issued to the device to be served. This includes the time spent by the requests in queue and the time spent servicing them.
    w_await     The average time (in milliseconds) for write requests issued to the device to be served. This includes the time spent by the requests in queue and the time spent servicing them.
    svctm       The average service time (in milliseconds) for I/O requests issued to the device. Warning! Do not trust this field; it will be removed in a future version of sysstat.
    %util       Percentage of CPU time during which I/O requests were issued to the device (bandwidth utilization for the device). Device saturation occurs when this values is close to 100%.
